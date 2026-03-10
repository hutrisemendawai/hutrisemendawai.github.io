<script>
  import { onMount, tick } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  gsap.registerPlugin(ScrollTrigger);

  let projectsContainer;
  let horizontalScrollArea;
  let titleRef;
  let currentProject = 0;
  let sections = [];

  function jumpToProject(i) {
    let st = ScrollTrigger.getById("projectsScroll");
    if (st && sections.length > 1) {
      let moveRatio = 0.6;
      let targetProgress = (i / (sections.length - 1)) * moveRatio;
      let targetScroll = st.start + (st.end - st.start) * targetProgress;
      window.scrollTo({ top: targetScroll, behavior: "smooth" });
    }
  }

  const projects = [
    {
      title: "Data Collection for Fishery Activities",
      subtitle: "- New Generation -",
      date: "Dec 2025 – Present",
      desc: "Developed an offline-first Android application for collecting fishery activity data. Built with Flutter, the app allows users to input data without an internet connection and automatically synchronizes with a PocketBase backend once online. Designed the administrative web dashboard using Laravel.",
      tech: [
        "Flutter",
        "PHP",
        "Laravel",
        "PocketBase",
        "Android",
        "Offline-First",
      ],
      images: [
        "/images/placeholder1.jpg",
        "/images/placeholder2.jpg",
        "/images/placeholder3.jpg",
        "/images/placeholder4.jpg",
      ],
    },
    {
      title: "Asset360",
      subtitle: "Asset Management Information System",
      date: "Completed",
      desc: "Asset360 is a web-based asset management system designed to streamline asset tracking and lifecycle management. Offers comprehensive features including real-time status monitoring, assignment tracking, and unique QR code generation for fast identification. Built for scalability, security, and ease of use.",
      tech: [
        "Spring Boot",
        "Java",
        "PostgreSQL",
        "QR Integration",
        "Web Dashboard",
      ],
      images: [
        "/images/placeholder5.jpg",
        "/images/placeholder6.jpg",
        "/images/placeholder7.jpg",
        "/images/placeholder8.jpg",
        "/images/placeholder9.jpg",
      ],
    },
  ];

  onMount(async () => {
    await tick();

    // Title reveal
    gsap.from(titleRef, {
      scrollTrigger: {
        trigger: projectsContainer,
        start: "top 70%",
        toggleActions: "play none none reverse",
      },
      y: 50,
      opacity: 0,
      duration: 1,
      ease: "power3.out",
    });

    // Horizontal Scroll Setup
    sections = gsap.utils.toArray(".project-panel");
    let moveRatio = 0.6;

    let tl = gsap.timeline({
      scrollTrigger: {
        id: "projectsScroll",
        trigger: projectsContainer,
        start: "top top",
        pin: true,
        scrub: 1,
        end: "+=2000",
        onUpdate: (self) => {
          let p = self.progress / moveRatio;
          if (p > 1) p = 1;
          let newProject = Math.round(p * (sections.length - 1));
          if (currentProject !== newProject) {
            currentProject = newProject;
          }
        },
      },
    });

    tl.to(".projects-track", {
      xPercent: -50,
      ease: "none",
      duration: moveRatio,
    }).to({}, { duration: 1 - moveRatio }); // Hold at the end for footer spacing

    // Image staggering inside panels
    sections.forEach((panel) => {
      let images = gsap.utils.toArray(panel.querySelectorAll(".mockup-img"));
      gsap.from(images, {
        scrollTrigger: {
          trigger: panel,
          start: "left center",
          containerAnimation: tl,
          toggleActions: "play none none reverse",
        },
        y: 100,
        opacity: 0,
        rotation: 5,
        stagger: 0.15,
        duration: 1,
        ease: "back.out(1.5)",
      });
    });
  });
</script>

<section bind:this={projectsContainer} id="projects" class="projects-section">
  <div class="title-container" bind:this={titleRef}>
    <div class="header-content">
      <h2><span class="neon-text">/</span> Featured Projects</h2>
      <div class="project-nav">
        {#each projects as p, i}
          <button
            class="nav-btn {currentProject === i ? 'active' : ''}"
            on:click={() => jumpToProject(i)}
          >
            0{i + 1}
          </button>
          {#if i < projects.length - 1}
            <span class="nav-divider"></span>
          {/if}
        {/each}
      </div>
    </div>
    <p class="section-desc">Scroll to explore my universe of applications.</p>
  </div>

  <div class="horizontal-scroll-container" bind:this={horizontalScrollArea}>
    <div class="projects-track">
      {#each projects as project}
        <div class="project-panel">
          <div class="project-content">
            <div class="text-content glass-panel">
              <h3>{project.title}</h3>
              <h4>{project.subtitle}</h4>
              <span class="date">{project.date}</span>
              <p>{project.desc}</p>
              <div class="tech-stack">
                {#each project.tech as t}
                  <span class="tech-tag">{t}</span>
                {/each}
              </div>
            </div>

            <div class="images-content">
              <div class="image-grid grid-{Math.min(project.images.length, 5)}">
                {#each project.images.slice(0, 5) as img, i}
                  <div class="mockup-img img-{i + 1}">
                    <div class="placeholder-content">
                      <span class="icon">🚀</span>
                      <span>Screenshot {i + 1}</span>
                    </div>
                  </div>
                {/each}
              </div>
            </div>
          </div>
        </div>
      {/each}
    </div>
  </div>
</section>

<style>
  .projects-section {
    position: relative;
    width: 100%;
    height: 100vh;
    display: grid;
    grid-template-rows: auto 1fr;
    overflow: hidden;
    opacity: 1;
    transform: none;
    padding-top: 5rem; /* Ensure space below navbar/top edge */
  }

  .title-container {
    max-width: 1200px;
    width: 100%;
    margin: 0 auto;
    padding: 0 2rem 2rem 2rem;
    position: relative;
    z-index: 20;
  }

  .header-content {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    margin-bottom: 0.5rem;
    gap: 20px;
  }

  h2 {
    font-size: 3rem;
    display: flex;
    align-items: center;
    gap: 15px;
    margin-bottom: 0;
  }

  .project-nav {
    display: flex;
    align-items: center;
    gap: 15px;
    background: rgba(255, 255, 255, 0.05);
    padding: 8px 20px;
    border-radius: 30px;
    border: 1px solid var(--glass-border);
  }

  .nav-btn {
    background: none;
    border: none;
    color: var(--text-muted);
    font-family: "Space Mono", monospace;
    font-size: 1.2rem;
    cursor: pointer;
    transition: all 0.3s ease;
    padding: 5px;
  }

  .nav-btn.active {
    color: var(--neon-blue);
    text-shadow: 0 0 10px rgba(0, 243, 255, 0.6);
    font-weight: 700;
    transform: scale(1.1);
  }

  .nav-btn:hover:not(.active) {
    color: var(--starlight);
  }

  .nav-divider {
    width: 30px;
    height: 2px;
    background: var(--glass-border);
  }

  .neon-text {
    color: var(--neon-blue);
    text-shadow: 0 0 15px rgba(0, 243, 255, 0.6);
  }

  .section-desc {
    font-family: "Space Mono", monospace;
    color: var(--text-muted);
    font-size: 1.1rem;
    margin-bottom: 2rem; /* Add space below description */
  }

  .horizontal-scroll-container {
    width: 100%;
    height: 100%;
    display: flex;
    flex-wrap: nowrap;
    overflow: hidden;
    position: relative;
    z-index: 10;
  }

  .projects-track {
    display: flex;
    width: 200vw; /* 2 panels = 200vw */
    height: 100%;
    align-items: center;
  }

  .project-panel {
    flex-shrink: 0;
    width: 100vw;
    height: 100%;
    padding: 0 2rem;
    display: flex;
    align-items: flex-start; /* Align closer to the top now that header is separated */
    justify-content: center;
  }

  .project-content {
    display: flex;
    width: 100%;
    max-width: 1400px;
    height: 100%;
    max-height: 800px;
    gap: 3rem;
    padding-bottom: 2rem;
  }

  .text-content {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    padding: 1rem 3rem 2rem 3rem;
    border-color: rgba(0, 243, 255, 0.2);
    z-index: 10;
    overflow-y: auto;
  }

  /* Custom Scrollbar for Text Content */
  .text-content::-webkit-scrollbar {
    width: 6px;
  }
  .text-content::-webkit-scrollbar-track {
    background: rgba(255, 255, 255, 0.05);
    border-radius: 10px;
  }
  .text-content::-webkit-scrollbar-thumb {
    background: var(--neon-blue);
    border-radius: 10px;
  }

  .text-content h3 {
    font-size: 2.5rem;
    color: var(--starlight);
    line-height: 1.2;
    margin-bottom: 0.5rem;
  }

  .text-content h4 {
    font-size: 1.2rem;
    color: var(--neon-blue);
    margin-bottom: 1rem;
    font-weight: 400;
  }

  .date {
    font-family: "Space Mono", monospace;
    font-size: 0.9rem;
    color: var(--star-gold);
    display: inline-block;
    padding: 4px 12px;
    background: rgba(255, 214, 10, 0.1);
    border-radius: 20px;
    margin-bottom: 1.5rem;
    width: fit-content;
  }

  .text-content p {
    font-size: 1.1rem;
    line-height: 1.7;
    margin-bottom: 2rem;
    color: var(--text-main);
  }

  .tech-stack {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
  }

  .tech-tag {
    font-family: "Space Mono", monospace;
    font-size: 0.8rem;
    padding: 6px 14px;
    border: 1px solid var(--nebula-purple);
    border-radius: 4px;
    background: rgba(157, 78, 221, 0.1);
    color: #fff;
  }

  .images-content {
    flex: 1.2;
    display: flex;
    align-items: center;
    justify-content: center;
    perspective: 1000px;
  }

  .image-grid {
    display: grid;
    gap: 1.5rem;
    width: 100%;
    height: 100%;
    max-height: 600px;
    padding: 1rem;
  }

  .grid-4 {
    grid-template-columns: repeat(2, 1fr);
    grid-template-rows: repeat(2, 1fr);
  }

  .grid-5 {
    grid-template-columns: repeat(6, 1fr);
    grid-template-rows: repeat(2, 1fr);
  }

  .grid-5 .img-1 {
    grid-column: 1 / 4;
    grid-row: 1;
  }
  .grid-5 .img-2 {
    grid-column: 4 / 7;
    grid-row: 1;
  }
  .grid-5 .img-3 {
    grid-column: 1 / 3;
    grid-row: 2;
  }
  .grid-5 .img-4 {
    grid-column: 3 / 5;
    grid-row: 2;
  }
  .grid-5 .img-5 {
    grid-column: 5 / 7;
    grid-row: 2;
  }

  .mockup-img {
    background: linear-gradient(135deg, #1b1b2f, #050510);
    border: 1px solid var(--glass-border);
    border-radius: 12px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    transition:
      transform 0.3s ease,
      box-shadow 0.3s ease,
      border-color 0.3s ease;
    cursor: crosshair;
  }

  .mockup-img:hover {
    transform: scale(1.05) translateZ(20px);
    box-shadow: 0 15px 40px rgba(0, 243, 255, 0.2);
    border-color: var(--neon-blue);
    z-index: 10;
  }

  .placeholder-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    color: var(--text-muted);
    font-family: "Space Mono", monospace;
    font-size: 0.9rem;
  }

  .placeholder-content .icon {
    font-size: 2rem;
    opacity: 0.5;
  }

  @media (max-width: 1024px) {
    .projects-section {
      height: auto;
      min-height: 100vh;
    }
    .project-content {
      flex-direction: column;
      height: auto;
      max-height: none;
      gap: 2rem;
      overflow-y: visible;
      padding-top: 2rem;
    }
    .text-content {
      padding: 2rem;
    }
    .images-content {
      min-height: 400px;
    }
  }
</style>
