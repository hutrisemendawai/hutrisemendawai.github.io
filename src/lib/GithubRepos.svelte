<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  let repos = [];
  let allRepos = [];
  let loading = true;
  let sectionRef;
  let listItems = [];
  
  let currentPage = 1;
  const itemsPerPage = 6;
  let totalPages = 1;

  gsap.registerPlugin(ScrollTrigger);

  onMount(async () => {
    try {
      // Fetch a larger batch to allow client-side pagination while handling filtering
      const response = await fetch("https://api.github.com/users/hutrisemendawai/repos?sort=updated&per_page=60");
      if (response.ok) {
        const rawRepos = await response.json();
        // Filter out the requested repositories and ensure they are public
        const excludeList = ["hutrisemendawai.github.io", "hutrisemendawai"];
        allRepos = rawRepos.filter(repo => !excludeList.includes(repo.name) && !repo.private);
        totalPages = Math.ceil(allRepos.length / itemsPerPage);
        
        updatePageObjects(false);
      } else {
        console.error("Failed to fetch repos", response.status);
      }
    } catch (e) {
      console.error("Error fetching repos", e);
    } finally {
      loading = false;
    }
  });

  function updatePageObjects(animateWithGsap = true) {
    if (!sectionRef) {
      const start = (currentPage - 1) * itemsPerPage;
      const end = start + itemsPerPage;
      repos = allRepos.slice(start, end);
      return;
    }

    const applyData = () => {
      const start = (currentPage - 1) * itemsPerPage;
      const end = start + itemsPerPage;
      repos = allRepos.slice(start, end);

      // Allow DOM to update array before animating in
      setTimeout(() => {
        if (!animateWithGsap) {
          ScrollTrigger.create({
            trigger: ".github-repos-container",
            start: "top 70%",
            once: true,
            onEnter: () => {
              gsap.fromTo(listItems.filter(Boolean),
                { opacity: 0, y: 50 },
                {
                  opacity: 1,
                  y: 0,
                  duration: 0.8,
                  ease: "power2.out",
                  stagger: 0.1
                }
              );
            }
          });
        } else {
          gsap.fromTo(listItems.filter(Boolean),
            { opacity: 0, y: 50 },
            {
              opacity: 1,
              y: 0,
              duration: 0.8,
              ease: "power2.out",
              stagger: 0.1,
              overwrite: "auto"
            }
          );
        }
      }, 50);
    };

    if (animateWithGsap) {
      const currentItems = listItems.filter(Boolean);
      if (currentItems.length > 0) {
        // Animate out existing items smoothly
        gsap.to(currentItems, {
          opacity: 0,
          y: -20,
          duration: 0.4,
          ease: "power2.in",
          stagger: 0.05,
          onComplete: applyData,
          overwrite: "auto"
        });
      } else {
        applyData();
      }
    } else {
      applyData();
    }
  }

  function prevPage() {
    if (currentPage > 1) {
      currentPage--;
      updatePageObjects(true);
    }
  }

  function nextPage() {
    if (currentPage < totalPages) {
      currentPage++;
      updatePageObjects(true);
    }
  }

  function getLanguageColor(lang) {
    if(!lang) return "var(--text-muted)";
    const colors = {
      Svelte: "#ff3e00",
      Java: "#b07219",
      TypeScript: "#3178c6",
      JavaScript: "#f1e05a",
      Dart: "#00B4AB",
      HTML: "#e34c26",
      CSS: "#563d7c",
      Go: "#00ADD8",
      Python: "#3572A5",
      C: "#555555",
      "C++": "#f34b7d",
      "C#": "#178600",
      Ruby: "#701516",
      PHP: "#4F5D95",
      Rust: "#dea584",
      Kotlin: "#A97BFF",
      Swift: "#F05138"
    };
    return colors[lang] || "var(--text-muted)";
  }
</script>

<section id="github-repos" class="github-repos" bind:this={sectionRef}>
  <div class="container github-repos-container">
    <div class="section-header">
      <h2 class="section-title">GITHUB<br>REPOSITORIES.</h2>
      <a href="https://github.com/hutrisemendawai?tab=repositories" target="_blank" rel="noopener noreferrer" class="view-all-link interactive">VIEW ALL REPOSITORIES ↗</a>
    </div>

    {#if loading}
      <div class="loading-state">Loading repositories from hyperspace...</div>
    {:else if repos.length === 0}
      <div class="error-state">No repositories found in this quadrant.</div>
    {:else}
      <div class="repo-carousel-wrapper">
        {#if totalPages > 1}
          <button class="nav-arrow prev-arrow interactive" on:click={prevPage} disabled={currentPage === 1} aria-label="Previous page">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M15 18l-6-6 6-6"/></svg>
          </button>
        {/if}

        <div class="repo-grid">
          {#each repos as repo, i}
            <a
              href={repo.html_url}
              target="_blank"
              rel="noopener noreferrer"
              class="repo-card interactive"
              bind:this={listItems[i]}
            >
              <div class="repo-top">
                <h3 class="repo-name">{repo.name}</h3>
                <div class="repo-desc">{repo.description || "No description provided."}</div>
              </div>
              <div class="repo-bottom">
                {#if repo.language}
                  <div class="repo-lang">
                    <span class="lang-dot" style="background-color: {getLanguageColor(repo.language)};"></span>
                    {repo.language}
                  </div>
                {/if}
                <div class="repo-stats">
                  <span class="stat">⭐ {repo.stargazers_count}</span>
                  <span class="stat">🔄 {repo.forks_count}</span>
                </div>
              </div>
            </a>
          {/each}
        </div>

        {#if totalPages > 1}
          <button class="nav-arrow next-arrow interactive" on:click={nextPage} disabled={currentPage === totalPages} aria-label="Next page">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9 18l6-6-6-6"/></svg>
          </button>
        {/if}
      </div>
    {/if}
  </div>
</section>

<style>
  .github-repos {
    /* Slightly different background from var(--monopo-dark) if needed, or stick to the theme */
    background-color: var(--bg-main); 
    padding: 10rem 0;
    position: relative;
    border-top: 1px solid var(--border-light);
  }

  .section-header {
    margin-bottom: 6rem;
    padding-bottom: 2rem;
    border-bottom: 2px solid var(--text-main);
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
  }

  .section-title {
    font-size: clamp(3rem, 8vw, 8rem);
    color: var(--text-main);
    line-height: 1;
  }

  .view-all-link {
    font-family: 'Inter', sans-serif;
    font-size: 1.2rem;
    color: var(--text-muted);
    text-decoration: none;
    text-transform: uppercase;
    letter-spacing: 1px;
    transition: color 0.3s ease;
    padding-bottom: 1rem;
  }

  .view-all-link:hover {
    color: var(--monopo-white);
  }

  .loading-state, .error-state {
    font-family: 'Inter', sans-serif;
    color: var(--text-muted);
    font-size: 1.2rem;
    padding: 3rem 0;
  }

  .repo-carousel-wrapper {
    display: flex;
    align-items: center;
    gap: 2rem;
    position: relative;
    width: 100%;
  }

  .repo-grid {
    flex: 1;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
    gap: 2rem;
  }

  .repo-card {
    background: rgba(255, 255, 255, 0.03);
    border: 1px solid var(--border-light);
    border-radius: 12px;
    padding: 2rem;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    height: 100%;
    text-decoration: none;
    color: var(--text-main);
    transition: transform 0.3s ease, background 0.3s ease, border-color 0.3s ease;
    backdrop-filter: blur(10px);
  }

  .repo-card:hover {
    transform: translateY(-10px);
    background: rgba(255, 255, 255, 0.08); /* More Glassmorphism highlight */
    border-color: rgba(255, 255, 255, 0.3);
  }

  .repo-name {
    font-family: 'Syne', sans-serif;
    font-size: 1.8rem;
    font-weight: 700;
    margin-bottom: 1rem;
    color: var(--monopo-white);
    /* break long project names */
    word-break: break-word;
  }

  .repo-desc {
    font-family: 'Inter', sans-serif;
    font-size: 1rem;
    color: var(--text-muted);
    line-height: 1.5;
    margin-bottom: 2rem;
    /* Limit the description length */
    display: -webkit-box;
    -webkit-line-clamp: 3;
    line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }

  .repo-bottom {
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-family: 'Inter', sans-serif;
    font-size: 0.9rem;
    color: var(--text-muted);
  }

  .repo-lang {
    display: flex;
    align-items: center;
    gap: 0.5rem;
  }

  .lang-dot {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    display: inline-block;
  }

  .repo-stats {
    display: flex;
    gap: 1rem;
  }

  .nav-arrow {
    background: transparent;
    border: 1px solid var(--border-light);
    color: var(--text-main);
    width: 60px;
    height: 60px;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    transition: all 0.3s ease;
    flex-shrink: 0;
  }

  .nav-arrow:hover:not(:disabled) {
    background: var(--text-main);
    color: var(--monopo-dark);
    border-color: var(--text-main);
    transform: scale(1.05);
  }

  .nav-arrow:disabled {
    opacity: 0.2;
    cursor: not-allowed;
  }

  .nav-arrow svg {
    width: 24px;
    height: 24px;
  }

  @media (max-width: 900px) {
    .section-header {
      flex-direction: column;
      align-items: flex-start;
      gap: 2rem;
    }
    
    .view-all-link {
      padding-bottom: 0;
    }
  }

  @media (max-width: 768px) {
    .github-repos {
      padding: 5rem 0;
    }
    .repo-carousel-wrapper {
      gap: 1rem;
    }
    .repo-grid {
      grid-template-columns: 1fr;
    }
    .nav-arrow {
      width: 40px;
      height: 40px;
    }
    .nav-arrow svg {
      width: 18px;
      height: 18px;
    }
  }
</style>
