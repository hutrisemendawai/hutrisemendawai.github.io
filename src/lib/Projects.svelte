<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import ProjectDetails from "./ProjectDetails.svelte";

  const projects = [
    { 
      id: "sales-invoice",
      title: "Sales Invoice Website", 
      category: "Web Application", 
      color: "#FF3333",
      tech: ["Svelte", "PocketBase", "Vite"],
      intro: "A modern, highly responsive sales invoice system built for streamlined transactions.",
      details: [
          { type: 'text', content: 'Built with Svelte and Vite for a blazing fast frontend, and powered by PocketBase for an embedded, lightweight backend. Spesifically built for PT Karya Trita Sejahtera.' },
          { type: 'image_placeholder', content: 'Screenshot 1' },
          { type: 'image_placeholder', content: 'Screenshot 2' }
      ]
    },
    { 
      id: "idempiere",
      title: "iDempiere Plugins", 
      category: "Enterprise Systems", 
      color: "#3333FF",
      tech: ["Java", "JasperReports", "PostgreSQL"],
      intro: "Enterprise resource planning extensions tailored to specific business flows. Spesifically build to integrate with PT Charoen Pokphand Indonesia ERP System.",
      details: [
          { type: 'text', content: 'Fully utilizing and customizing PostgreSQL to handle complex enterprise data requirements. Developed entirely using Java and generated custom operational reports with JasperReports.' }
      ]
    },
    { 
      id: "fishery",
      title: "Data Collections for Fishery", 
      category: "Offline-First Apps", 
      color: "#33FF33",
      tech: ["Flutter", "Laravel"],
      intro: "An offline-capable mobile application designed for field data collection in remote fishery locations.",
      details: [
          { type: 'text', content: 'I built this cross-platform Android app using Flutter. Data is stored locally while the user is offline and syncs automatically with the central server once connected to the internet.' },
          { type: 'text', content: 'The system also features a Laravel-based web dashboard for administrators to securely manage, monitor, and export all compiled data.' }
      ]
    },
    { 
      id: "project-management",
      title: "Project Management Website", 
      category: "Web Platform", 
      color: "#FFFF33",
      tech: ["Svelte", "Open Source"],
      intro: "An open-source, highly interactive productivity web application.",
      details: [
          { type: 'text', content: 'I recently worked on this fully open-source project management platform, utilizing Svelte to craft a modern user experience and interface.' },
          { type: 'text', content: 'The website is designed to look and feel like Spotify, keeping users engaged while they work. It features an interactive Kanban board for users to easily manage their projects.' },
          { type: 'text', content: 'This project will be regularly updated for more features.' }
      ]
    }
  ];

  let listItems = [];
  let hoverImageRef; // This acts as the abstract visual placeholder
  let activeProject = null;
  let projectsRef;

  gsap.registerPlugin(ScrollTrigger);

  onMount(() => {
    if (!projectsRef) {
      return;
    }

    const ctx = gsap.context(() => {
      listItems.filter(Boolean).forEach((item, i) => {
        gsap.fromTo(item,
          { opacity: 0, y: 50 },
          {
            opacity: 1,
            y: 0,
            duration: 0.8,
            ease: "power2.out",
            delay: i * 0.1,
            scrollTrigger: {
              trigger: ".projects-container",
              start: "top 70%"
            }
          }
        );
      });
    }, projectsRef);

    let cleanupPointer = () => {};

    if (hoverImageRef && !window.matchMedia("(pointer: coarse)").matches) {
      const moveImgX = gsap.quickTo(hoverImageRef, "left", { duration: 0.4, ease: "power3" });
      const moveImgY = gsap.quickTo(hoverImageRef, "top", { duration: 0.4, ease: "power3" });

      const handlePointerMove = (event) => {
        moveImgX(event.clientX);
        moveImgY(event.clientY);
      };

      const hideFollower = () => {
        handleMouseLeave();
      };

      window.addEventListener("pointermove", handlePointerMove);
      document.addEventListener("mouseleave", hideFollower);

      cleanupPointer = () => {
        window.removeEventListener("pointermove", handlePointerMove);
        document.removeEventListener("mouseleave", hideFollower);
      };
    }

    return () => {
      cleanupPointer();
      ctx.revert();
      if (hoverImageRef) {
        gsap.killTweensOf(hoverImageRef);
      }
    };
  });

  function handleMouseEnter(color) {
    if(!hoverImageRef) return;
    gsap.to(hoverImageRef, { 
      scale: 1, 
      opacity: 1, 
      backgroundColor: color,
      duration: 0.4, 
      ease: "back.out(1.7)",
      overwrite: "auto"
    });
  }

  function handleMouseLeave() {
    if(!hoverImageRef) return;
    gsap.to(hoverImageRef, { 
      scale: 0, 
      opacity: 0, 
      duration: 0.3, 
      ease: "power2.in",
      overwrite: "auto"
    });
  }
</script>

<div class="hover-visual" bind:this={hoverImageRef}></div>

<section id="projects" class="projects" bind:this={projectsRef}>
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
          on:click|preventDefault={() => { activeProject = proj; }}
        >
          <div class="project-title">{proj.title}</div>
          <div class="project-category">{proj.category}</div>
          <div class="project-arrow">↗</div>
        </a>
      {/each}
    </div>
  </div>
</section>

{#if activeProject}
  <ProjectDetails project={activeProject} on:close={() => { activeProject = null; }} />
{/if}

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
