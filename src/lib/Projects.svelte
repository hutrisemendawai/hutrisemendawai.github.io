<script>
  import { onMount, tick } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  gsap.registerPlugin(ScrollTrigger);

  let projectsContainer;
  let horizontalScrollArea;
  let titleRef;
  let progressBar;
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

  // Magnetic hover function for images
  function handleImageMouseMove(e) {
    const img = e.currentTarget;
    const rect = img.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;
    const centerX = rect.width / 2;
    const centerY = rect.height / 2;

    const moveX = ((x - centerX) / centerX) * 10;
    const moveY = ((y - centerY) / centerY) * 10;

    gsap.to(img, {
      x: moveX,
      y: moveY,
      rotateX: -moveY * 0.5,
      rotateY: moveX * 0.5,
      duration: 0.3,
      ease: "power2.out",
      transformPerspective: 600,
    });

    // Spotlight effect
    img.style.setProperty("--img-spot-x", `${x}px`);
    img.style.setProperty("--img-spot-y", `${y}px`);
  }

  function handleImageMouseLeave(e) {
    const img = e.currentTarget;
    gsap.to(img, {
      x: 0,
      y: 0,
      rotateX: 0,
      rotateY: 0,
      duration: 0.6,
      ease: "elastic.out(1, 0.5)",
    });
  }

  onMount(async () => {
    await tick();

    // ===== TITLE REVEAL =====
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

    // ===== HORIZONTAL SCROLL SETUP =====
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
          // Update project progress bar
          if (progressBar) {
            gsap.set(progressBar, { scaleX: self.progress });
          }
        },
      },
    });

    tl.to(".projects-track", {
      xPercent: -50,
      ease: "none",
      duration: moveRatio,
    }).to({}, { duration: 1 - moveRatio });

    // ===== IMAGE STAGGERING =====
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
        scale: 0.9,
        stagger: 0.12,
        duration: 0.8,
        ease: "back.out(1.5)",
      });

      // ===== TECH TAGS FLY IN =====
      let techTags = gsap.utils.toArray(panel.querySelectorAll(".tech-tag"));
      gsap.from(techTags, {
        scrollTrigger: {
          trigger: panel,
          start: "left center",
          containerAnimation: tl,
          toggleActions: "play none none reverse",
        },
        x: -30,
        opacity: 0,
        stagger: 0.06,
        duration: 0.5,
        ease: "back.out(1.5)",
        delay: 0.3,
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
            <span class="nav-num">0{i + 1}</span>
          </button>
          {#if i < projects.length - 1}
            <span class="nav-divider"></span>
          {/if}
        {/each}
      </div>
    </div>
    <p class="section-desc">Scroll to explore my universe of applications.</p>
    <!-- Progress bar for project scroll -->
    <div class="project-progress-track">
      <div class="project-progress-bar" bind:this={progressBar}></div>
    </div>
  </div>

  <div class="horizontal-scroll-container" bind:this={horizontalScrollArea}>
    <div class="projects-track">
      {#each projects as project}
        <div class="project-panel">
          <div class="project-content">
            <div class="text-content neon-border-card">
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
                  <div
                    class="mockup-img img-{i + 1}"
                    on:mousemove={handleImageMouseMove}
                    on:mouseleave={handleImageMouseLeave}
                    role="img"
                    aria-label="Screenshot {i + 1}"
                  >
                    <div class="img-spotlight"></div>
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
    padding-top: 5rem;
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

  .neon-text {
    color: var(--neon-blue);
    text-shadow: 0 0 15px rgba(0, 243, 255, 0.6);
    animation: glowPulse 3s ease-in-out infinite;
  }

  /* ===== PROJECT NAV (Uiverse.io inspired pill) ===== */
  .project-nav {
    display: flex;
    align-items: center;
    gap: 15px;
    background: rgba(255, 255, 255, 0.05);
    padding: 8px 20px;
    border-radius: 30px;
    border: 1px solid var(--glass-border);
    backdrop-filter: blur(10px);
    transition: border-color 0.3s ease;
  }

  .project-nav:hover {
    border-color: rgba(0, 243, 255, 0.2);
  }

  .nav-btn {
    background: none;
    border: none;
    color: var(--text-muted);
    font-family: "Space Mono", monospace;
    font-size: 1.2rem;
    cursor: pointer;
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    padding: 5px 10px;
    border-radius: 8px;
    position: relative;
  }

  .nav-btn.active {
    color: var(--neon-blue);
    text-shadow: 0 0 10px rgba(0, 243, 255, 0.6);
    font-weight: 700;
    transform: scale(1.1);
    background: rgba(0, 243, 255, 0.08);
  }

  .nav-btn:hover:not(.active) {
    color: var(--starlight);
    background: rgba(255, 255, 255, 0.03);
  }

  .nav-divider {
    width: 30px;
    height: 2px;
    background: var(--glass-border);
    transition: background 0.3s ease;
  }

  .section-desc {
    font-family: "Space Mono", monospace;
    color: var(--text-muted);
    font-size: 1.1rem;
    margin-bottom: 1rem;
  }

  /* ===== PROJECT PROGRESS BAR ===== */
  .project-progress-track {
    width: 100%;
    height: 3px;
    background: rgba(255, 255, 255, 0.05);
    border-radius: 2px;
    overflow: hidden;
  }

  .project-progress-bar {
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, var(--neon-blue), var(--nebula-purple), var(--aurora-green));
    background-size: 200% auto;
    animation: auroraShift 4s ease infinite;
    transform-origin: left;
    transform: scaleX(0);
    border-radius: 2px;
    box-shadow: 0 0 8px var(--neon-blue);
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
    width: 200vw;
    height: 100%;
    align-items: center;
  }

  .project-panel {
    flex-shrink: 0;
    width: 100vw;
    height: 100%;
    padding: 0 2rem;
    display: flex;
    align-items: flex-start;
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
    z-index: 10;
    overflow-y: auto;
  }

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
    text-shadow: 0 0 6px rgba(255, 214, 10, 0.3);
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
    padding: 6px 16px;
    border: 1px solid var(--nebula-purple);
    border-radius: 20px;
    background: rgba(157, 78, 221, 0.08);
    color: #fff;
    transition: all 0.3s ease;
    cursor: default;
    will-change: transform;
  }

  .tech-tag:hover {
    background: rgba(157, 78, 221, 0.25);
    border-color: var(--neon-blue);
    box-shadow: 0 0 12px rgba(0, 243, 255, 0.3);
    transform: translateY(-2px) scale(1.05);
    color: var(--neon-blue);
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

  .grid-5 .img-1 { grid-column: 1 / 4; grid-row: 1; }
  .grid-5 .img-2 { grid-column: 4 / 7; grid-row: 1; }
  .grid-5 .img-3 { grid-column: 1 / 3; grid-row: 2; }
  .grid-5 .img-4 { grid-column: 3 / 5; grid-row: 2; }
  .grid-5 .img-5 { grid-column: 5 / 7; grid-row: 2; }

  /* ===== MAGNETIC IMAGE CARDS ===== */
  .mockup-img {
    position: relative;
    background: linear-gradient(135deg, #1b1b2f, #0a0a1a);
    border: 1px solid var(--glass-border);
    border-radius: 12px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: crosshair;
    will-change: transform;
    transform-style: preserve-3d;
    transition: border-color 0.3s ease, box-shadow 0.3s ease;
  }

  .mockup-img:hover {
    border-color: var(--neon-blue);
    box-shadow:
      0 15px 40px rgba(0, 243, 255, 0.2),
      0 0 30px rgba(0, 243, 255, 0.08);
    z-index: 10;
  }

  .img-spotlight {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: radial-gradient(
      200px circle at var(--img-spot-x, -100px) var(--img-spot-y, -100px),
      rgba(0, 243, 255, 0.12),
      transparent 60%
    );
    pointer-events: none;
    z-index: 1;
  }

  .placeholder-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    color: var(--text-muted);
    font-family: "Space Mono", monospace;
    font-size: 0.9rem;
    position: relative;
    z-index: 2;
  }

  .placeholder-content .icon {
    font-size: 2rem;
    opacity: 0.5;
    filter: drop-shadow(0 0 6px rgba(255, 255, 255, 0.2));
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
