<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import rocketIcon from '../assets/rocketicon.png';

  gsap.registerPlugin(ScrollTrigger);

  let wrapperRef;
  let sectionRef;
  let planetRef;
  let currentIndex = 0;

  const projects = [{
      title: "Data Collection for Fishery Activities",
      subtitle: "- New Generation -",
      date: "Dec 2025 – Present",
      desc: "Developed an offline-first Android application for collecting fishery activity data. Built with Flutter, the app allows users to input data without an internet connection and automatically synchronizes with a PocketBase backend once online. Designed the admin web dashboard using Laravel.",
      tech: ["Flutter", "PHP", "Laravel", "PocketBase", "Android", "Offline-First"],
      images: ["/images/placeholder1.jpg", "/images/placeholder2.jpg", "/images/placeholder3.jpg"],
    },
    {
      title: "Asset360",
      subtitle: "Asset Management Information System",
      date: "Completed",
      desc: "Asset360 is a web-based asset management system designed to streamline asset tracking and lifecycle management. Features real-time status monitoring, assignment tracking, and QR code generation for fast identification. Built for scalability, security, and ease of use.",
      tech: ["Spring Boot", "Java", "PostgreSQL", "QR Integration", "Web Dashboard"],
      images: ["/images/placeholder5.jpg", "/images/placeholder6.jpg", "/images/placeholder7.jpg"],
    },
  ];

  onMount(() => {
    const planetTl = gsap.timeline({
      scrollTrigger: { trigger: wrapperRef, start: 'top top', end: 'bottom bottom', scrub: 1 },
    });
    planetTl.to(planetRef, { rotation: 180, ease: 'none' });

    const switchSt = ScrollTrigger.create({
      trigger: wrapperRef,
      start: 'top top',
      end: 'bottom bottom',
      onUpdate: (self) => {
        currentIndex = Math.min(projects.length - 1, Math.floor(self.progress * projects.length));
      },
    });

    return () => { planetTl.kill(); switchSt.kill(); };
  });
</script>

<div class="projects-wrapper" bind:this={wrapperRef}>
  <section class="projects-section" bind:this={sectionRef}>

    <div class="section-header">
      <h2><span class="slash">/</span> Featured Projects</h2>
      <p class="section-sub">Scroll to explore my universe of applications.</p>
    </div>

    <div class="slides-area">
      {#each projects as project, index}
        <div class="project-slide" class:active={index === currentIndex}>

          <div class="project-info">
            <span class="proj-num">0{index + 1}</span>
            <h3>{project.title}</h3>
            <h4>{project.subtitle}</h4>
            <span class="date-badge">{project.date}</span>
            <p class="desc">{project.desc}</p>
            <div class="tech-stack">
              {#each project.tech as tag}
                <span class="tech-tag">{tag}</span>
              {/each}
            </div>
          </div>

          <div class="project-visuals">
            <div class="card card-main">
              <div class="card-inner">
                <img src={rocketIcon} alt="" class="card-icon" />
                <span>Screenshot 1</span>
              </div>
            </div>
            <div class="card card-top">
              <div class="card-inner">
                <img src={rocketIcon} alt="" class="card-icon" />
                <span>Screenshot 2</span>
              </div>
            </div>
            <div class="card card-btm">
              <div class="card-inner">
                <img src={rocketIcon} alt="" class="card-icon" />
                <span>Screenshot 3</span>
              </div>
            </div>
          </div>

        </div>
      {/each}
    </div>

    <div class="nav-dots">
      {#each projects as _, i}
        <span class="nav-dot" class:active={i === currentIndex}></span>
      {/each}
    </div>

    <div class="planet-container">
      <div class="planet-horizon" bind:this={planetRef}>
        <div class="planet-glow"></div>
        <div class="planet-surface"></div>
        <div class="planet-ring-1"></div>
        <div class="planet-ring-2"></div>
        <div class="planet-decorator dec-1"></div>
        <div class="planet-decorator dec-2"></div>
        <div class="planet-decorator dec-3"></div>
      </div>
    </div>

  </section>
</div>

<style>
  /* ===== WRAPPER ===== */
  .projects-wrapper {
    position: relative;
    height: 300vh;
    width: 100vw;
    margin-left: calc(-50vw + 50%);
  }

  /* ===== STICKY SECTION ===== */
  .projects-section {
    position: sticky;
    top: 0;
    width: 100%;
    height: 100vh;
    background: #020308;
    overflow: hidden;
    z-index: 10;
    opacity: 1;
    transform: none;
    display: block;
    padding: 0;
  }

  /* ===== HEADER ===== */
  .section-header {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    padding: 3.5vh 0 0;
    text-align: center;
    z-index: 20;
  }

  .section-header h2 {
    font-size: 2.6rem;
    color: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 12px;
    margin: 0 0 0.35rem;
  }

  .slash {
    color: var(--neon-blue, #00f3ff);
    text-shadow: 0 0 15px rgba(0, 243, 255, 0.6);
  }

  .section-sub {
    font-family: 'Space Mono', monospace;
    color: var(--text-muted, #8b92ba);
    font-size: 0.95rem;
    margin: 0;
  }

  /* ===== SLIDES AREA ===== */
  .slides-area {
    position: absolute;
    top: 17vh;
    left: 0;
    width: 100%;
    height: 55vh;
    z-index: 10;
  }

  .project-slide {
    position: absolute;
    inset: 0;
    display: flex;
    align-items: center;
    gap: 3vw;
    padding: 0 6vw;
    box-sizing: border-box;
    opacity: 0;
    transform: translateY(24px);
    transition: opacity 0.4s ease, transform 0.4s ease;
    pointer-events: none;
  }

  .project-slide.active {
    opacity: 1;
    transform: translateY(0);
    pointer-events: auto;
  }

  /* ===== PROJECT INFO ===== */
  .project-info {
    flex: 0 0 46%;
    position: relative;
  }

  .proj-num {
    position: absolute;
    top: -1rem;
    left: -0.5rem;
    font-family: 'Space Mono', monospace;
    font-size: 5.5rem;
    font-weight: 800;
    color: rgba(255, 255, 255, 0.025);
    line-height: 1;
    pointer-events: none;
    user-select: none;
    z-index: -1;
  }

  .project-info h3 {
    font-size: 2rem;
    color: #fff;
    line-height: 1.25;
    margin: 0 0 0.35rem;
  }

  .project-info h4 {
    font-size: 1rem;
    color: var(--neon-blue, #00f3ff);
    font-weight: 400;
    margin: 0 0 0.8rem;
  }

  .date-badge {
    display: inline-block;
    font-family: 'Space Mono', monospace;
    font-size: 0.8rem;
    color: var(--star-gold, #ffd60a);
    background: rgba(255, 214, 10, 0.1);
    border-radius: 20px;
    padding: 4px 14px;
    margin-bottom: 0.9rem;
    text-shadow: 0 0 6px rgba(255, 214, 10, 0.3);
  }

  .desc {
    font-size: 0.9rem;
    line-height: 1.75;
    color: var(--text-main, #d1d5eb);
    margin: 0 0 0.9rem;
  }

  .tech-stack {
    display: flex;
    flex-wrap: wrap;
    gap: 7px;
  }

  .tech-tag {
    font-family: 'Space Mono', monospace;
    font-size: 0.75rem;
    padding: 4px 12px;
    border: 1px solid var(--nebula-purple, #9d4edd);
    border-radius: 20px;
    background: rgba(157, 78, 221, 0.08);
    color: #fff;
  }

  /* ===== PROJECT VISUALS ===== */
  .project-visuals {
    flex: 0 0 48%;
    height: 100%;
    position: relative;
    overflow: hidden;
  }

  .card {
    position: absolute;
    background: linear-gradient(135deg, rgba(27, 27, 47, 0.85), rgba(10, 10, 26, 0.95));
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 14px;
    box-shadow: 0 12px 30px rgba(0, 0, 0, 0.6);
    display: flex;
    align-items: center;
    justify-content: center;
    backdrop-filter: blur(8px);
    transition: border-color 0.3s ease, box-shadow 0.3s ease;
  }

  .card:hover {
    border-color: var(--neon-blue, #00f3ff);
    box-shadow: 0 0 25px rgba(0, 243, 255, 0.25);
  }

  /* Main card — centered, dominant */
  .card-main {
    width: 62%;
    height: 56%;
    top: 50%;
    left: 28%;
    transform: translate(-50%, -50%) rotate(-2deg);
    z-index: 3;
  }

  /* Top-right secondary */
  .card-top {
    width: 42%;
    height: 36%;
    top: 5%;
    right: 1%;
    transform: rotate(4deg);
    z-index: 4;
    border-color: rgba(157, 78, 221, 0.4);
  }

  /* Bottom-left tertiary */
  .card-btm {
    width: 40%;
    height: 34%;
    bottom: 6%;
    left: 1%;
    transform: rotate(-5deg);
    z-index: 2;
    border-color: rgba(0, 243, 255, 0.3);
  }

  .card-inner {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 7px;
    color: var(--text-muted, #8b92ba);
    font-family: 'Space Mono', monospace;
    font-size: 0.75rem;
  }

  .card-icon {
    width: 30px;
    height: 30px;
    opacity: 0.55;
    filter: drop-shadow(0 0 6px rgba(255, 255, 255, 0.2));
  }

  /* ===== NAV DOTS (right side) ===== */
  .nav-dots {
    position: absolute;
    right: 2vw;
    top: 50%;
    transform: translateY(-50%);
    display: flex;
    flex-direction: column;
    gap: 10px;
    z-index: 20;
  }

  .nav-dot {
    display: block;
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.2);
    border: 1px solid rgba(255, 255, 255, 0.3);
    transition: all 0.3s ease;
  }

  .nav-dot.active {
    background: var(--neon-blue, #00f3ff);
    border-color: var(--neon-blue, #00f3ff);
    box-shadow: 0 0 8px rgba(0, 243, 255, 0.6);
    transform: scale(1.3);
  }

  /* ===== PLANET ===== */
  .planet-container {
    position: absolute;
    bottom: -15vh;
    left: 0;
    width: 100%;
    height: 40vh;
    pointer-events: none;
    z-index: 5;
  }

  .planet-horizon {
    position: absolute;
    top: 0;
    left: 50%;
    width: 150vw;
    height: 150vw;
    margin-left: -75vw;
    border-radius: 50%;
    background: radial-gradient(circle at 50% 10%, #060913 0%, #010206 60%);
    box-shadow:
      inset 0 15px 50px rgba(0, 243, 255, 0.2),
      inset 0 -100px 100px #000,
      0 0 80px rgba(0, 243, 255, 0.1);
    border-top: 2px solid rgba(0, 243, 255, 0.4);
    overflow: hidden;
    transform-origin: center center;
  }

  .planet-glow {
    position: absolute;
    top: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 100%;
    height: 100px;
    background: radial-gradient(ellipse at center, rgba(0, 243, 255, 0.5) 0%, transparent 60%);
    filter: blur(20px);
    z-index: 1;
  }

  .planet-surface {
    position: absolute;
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background-image:
      linear-gradient(rgba(255, 255, 255, 0.05) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255, 255, 255, 0.05) 1px, transparent 1px);
    background-size: 4vw 4vw;
    mask-image: radial-gradient(circle at 50% 30%, black 10%, transparent 70%);
    -webkit-mask-image: radial-gradient(circle at 50% 30%, black 10%, transparent 70%);
    z-index: 2;
  }

  .planet-ring-1 {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 98%;
    height: 98%;
    transform: translate(-50%, -50%);
    border-radius: 50%;
    border: 1px dashed rgba(0, 243, 255, 0.3);
    z-index: 3;
  }

  .planet-ring-2 {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 94%;
    height: 94%;
    transform: translate(-50%, -50%);
    border-radius: 50%;
    border: 2px solid rgba(157, 78, 221, 0.1);
    border-top: 2px solid rgba(157, 78, 221, 0.5);
    border-bottom: 2px solid rgba(157, 78, 221, 0.5);
    z-index: 4;
  }

  .planet-decorator {
    position: absolute;
    background: var(--neon-blue);
    box-shadow: 0 0 15px var(--neon-blue);
    border-radius: 10px;
    z-index: 5;
  }
  .dec-1 { top: 5%; left: 30%; width: 80px; height: 3px; transform: rotate(15deg); }
  .dec-2 { top: 8%; right: 25%; width: 40px; height: 3px; transform: rotate(-20deg); background: var(--nebula-purple); box-shadow: 0 0 15px var(--nebula-purple); }
  .dec-3 { top: 1%; left: 55%; width: 6px; height: 6px; border-radius: 50%; }

  /* ===== RESPONSIVE ===== */
  @media (max-width: 1024px) {
    .projects-wrapper { height: 400vh; }

    .slides-area { top: 15vh; height: 60vh; }

    .project-slide {
      flex-direction: column;
      padding: 1vh 5vw 0;
      gap: 1.5vh;
      justify-content: flex-start;
    }

    .project-info { flex: 0 0 auto; text-align: center; }
    .project-info h3 { font-size: 1.6rem; }
    .desc { font-size: 0.85rem; }
    .tech-stack { justify-content: center; }
    .proj-num { display: none; }

    .project-visuals { flex: 0 0 auto; width: 100%; height: 25vh; }
    .planet-horizon { width: 200vw; height: 200vw; margin-left: -100vw; }
  }
</style>
