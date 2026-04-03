<script>
  import { onMount, tick } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  const projects = [
    { title: "Nota Gen", category: "Web App / Tools", color: "#FF3333" },
    { title: "Hutri ERP", category: "Enterprise Systems", color: "#3333FF" },
    { title: "Nexus Engine", category: "Desktop Application", color: "#33FF33" },
    { title: "Singularity UI", category: "Design System", color: "#FFFF33" }
  ];

  let listItems = [];
  let hoverImageRef; // This acts as the abstract visual placeholder

  onMount(() => {
    gsap.registerPlugin(ScrollTrigger);

    listItems.forEach((item, i) => {
      if (item) {
        gsap.fromTo(item,
          { opacity: 0, y: 50 },
          {
            opacity: 1, y: 0, duration: 0.8, ease: "power2.out", delay: i * 0.1,
            scrollTrigger: {
              trigger: ".projects-container",
              start: "top 70%"
            }
          }
        );
      }
    });

    // Custom follower for the abstract project visual
    const moveImgX = gsap.quickTo(hoverImageRef, "left", { duration: 0.4, ease: "power3" });
    const moveImgY = gsap.quickTo(hoverImageRef, "top", { duration: 0.4, ease: "power3" });

    window.addEventListener("mousemove", (e) => {
      moveImgX(e.clientX);
      moveImgY(e.clientY);
    });
  });

  function handleMouseEnter(color) {
    if(!hoverImageRef) return;
    gsap.to(hoverImageRef, { 
      scale: 1, 
      opacity: 1, 
      backgroundColor: color,
      duration: 0.4, 
      ease: "back.out(1.7)" 
    });
  }

  function handleMouseLeave() {
    if(!hoverImageRef) return;
    gsap.to(hoverImageRef, { 
      scale: 0, 
      opacity: 0, 
      duration: 0.3, 
      ease: "power2.in" 
    });
  }
</script>

<div class="hover-visual" bind:this={hoverImageRef}></div>

<section id="projects" class="projects">
  <div class="container projects-container">
    <div class="section-header">
      <h2 class="section-title">FEATURED<br>WORK.</h2>
    </div>

    <div class="project-list">
      {#each projects as proj, i}
        <a 
          href="#/"
          class="project-row interactive" 
          bind:this={listItems[i]}
          on:mouseenter={() => handleMouseEnter(proj.color)}
          on:mouseleave={handleMouseLeave}
        >
          <div class="project-title">{proj.title}</div>
          <div class="project-category">{proj.category}</div>
          <div class="project-arrow">↗</div>
        </a>
      {/each}
    </div>
  </div>
</section>

<style>
  .projects {
    background-color: var(--monopo-dark);
    padding: 10rem 0;
    position: relative;
    /* Prevent abstract image from breaking scroll */
    overflow: hidden; 
  }

  .hover-visual {
    position: fixed;
    top: 0;
    left: 0;
    width: 300px;
    height: 400px;
    border-radius: 12px;
    pointer-events: none;
    z-index: 50;
    transform: translate(-50%, -50%) scale(0);
    opacity: 0;
    background-color: var(--monopo-white); /* Will be overridden by JS */
    mix-blend-mode: exclusion;
    box-shadow: 0 20px 40px rgba(0,0,0,0.5);
  }

  .section-header {
    margin-bottom: 6rem;
    padding-bottom: 2rem;
    border-bottom: 2px solid var(--text-main);
  }

  .section-title {
    font-size: clamp(3rem, 8vw, 8rem);
    color: var(--text-main);
  }

  .project-list {
    display: flex;
    flex-direction: column;
  }

  .project-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 3rem 0;
    border-bottom: 1px solid var(--border-light);
    color: var(--text-main);
    transition: padding 0.4s ease;
  }

  .project-row:hover {
    padding: 4rem 1rem;
    color: var(--monopo-white);
  }

  .project-title {
    font-family: 'Syne', sans-serif;
    font-size: clamp(2rem, 5vw, 5rem);
    font-weight: 800;
    flex: 2;
    transition: transform 0.3s ease;
  }

  .project-row:hover .project-title {
    transform: translateX(10px);
  }

  .project-category {
    flex: 1;
    font-family: 'Inter', sans-serif;
    font-size: 1rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    color: var(--text-muted);
    text-align: center;
  }

  .project-arrow {
    flex: 1;
    text-align: right;
    font-size: 3rem;
    font-family: 'Syne', sans-serif;
    transition: transform 0.3s ease;
  }

  .project-row:hover .project-arrow {
    transform: translate(10px, -10px);
  }

  @media (max-width: 900px) {
    .project-row {
      flex-direction: column;
      align-items: flex-start;
      gap: 1rem;
      padding: 2rem 0;
    }
    .project-category {
      text-align: left;
    }
    .project-arrow {
      display: none;
    }
    .project-row:hover {
      padding: 2rem 0;
    }
  }
</style>
