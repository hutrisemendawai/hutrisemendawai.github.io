<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import rocketIcon from '../assets/rocketicon.png';

  gsap.registerPlugin(ScrollTrigger);

  let wrapperRef;
  let sectionRef;
  let planetRef;

  const projects = [
    {
      title: "Data Collection for Fishery Activities",
      subtitle: "- New Generation -",
      date: "Dec 2025 – Present",
      desc: "Developed an offline-first Android application for collecting fishery activity data. Built with Flutter, the app allows users to input data without an internet connection and automatically synchronizes with a PocketBase backend once online. Designed the administrative web dashboard using Laravel.",
      tech: ["Flutter", "PHP", "Laravel", "PocketBase", "Android", "Offline-First"],
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
      tech: ["Spring Boot", "Java", "PostgreSQL", "QR Integration", "Web Dashboard"],
      images: [
        "/images/placeholder5.jpg",
        "/images/placeholder6.jpg",
        "/images/placeholder7.jpg",
        "/images/placeholder8.jpg",
        "/images/placeholder9.jpg",
      ],
    },
  ];

  onMount(() => {
    const slides = Array.from(sectionRef.querySelectorAll('.project-slide'));
    let currentIndex = 0;

    // Set initial slide state via gsap to avoid transform cache conflicts
    slides.forEach((slide, i) => {
      gsap.set(slide, {
        opacity: i === 0 ? 1 : 0,
        y: i === 0 ? 0 : 60,
        pointerEvents: i === 0 ? 'auto' : 'none',
      });
    });

    function goTo(index) {
      if (index === currentIndex) return;
      const outSlide = slides[currentIndex];
      const inSlide = slides[index];
      const dir = index > currentIndex ? 1 : -1;

      gsap.killTweensOf(outSlide);
      gsap.killTweensOf(inSlide);

      gsap.to(outSlide, { opacity: 0, y: -60 * dir, duration: 0.35, ease: 'power2.in', onComplete: () => { gsap.set(outSlide, { pointerEvents: 'none' }); } });
      gsap.fromTo(inSlide, { opacity: 0, y: 60 * dir }, { opacity: 1, y: 0, duration: 0.35, ease: 'power2.out', onStart: () => { gsap.set(inSlide, { pointerEvents: 'auto' }); } });
      currentIndex = index;
    }

    // Rotate planet while scrolling through the wrapper
    const planetTl = gsap.timeline({
      scrollTrigger: {
        trigger: wrapperRef,
        start: 'top top',
        end: 'bottom bottom',
        scrub: 1,
      }
    });
    planetTl.to(planetRef, { rotation: 180, ease: 'none' });

    // Switch slides based on scroll position within wrapper
    const switchSt = ScrollTrigger.create({
      trigger: wrapperRef,
      start: 'top top',
      end: 'bottom bottom',
      scrub: false,
      onUpdate: (self) => {
        const newIndex = Math.min(
          projects.length - 1,
          Math.floor(self.progress * projects.length)
        );
        goTo(newIndex);
      }
    });

    return () => {
      planetTl.kill();
      switchSt.kill();
    };
  });
</script>

<!-- Tall wrapper enables scrolling; sticky section stays in view -->
<div class="projects-wrapper" bind:this={wrapperRef}>
  <section class="planet-projects-section" bind:this={sectionRef}>
    <div class="projects-title">
      <h2><span class="neon-text">/</span> Featured Projects</h2>
      <p class="section-desc">Scroll to explore my universe of applications.</p>
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

    <div class="projects-container">
      {#each projects as project, index}
        <div class="project-slide">
          <div class="project-info">
            <span class="project-number">0{index + 1}</span>
            <h3>{project.title}</h3>
            <h4>{project.subtitle}</h4>
            <span class="date">{project.date}</span>
            <p class="desc">{project.desc}</p>
            <div class="tech-stack">
              {#each project.tech as t}
                <span class="tech-tag">{t}</span>
              {/each}
            </div>
          </div>

          <div class="project-visuals">
            {#each project.images.slice(0, 4) as img, i}
              <div class="mockup-img img-{i + 1}">
                <div class="placeholder-content">
                  <img src={rocketIcon} alt="rocket" class="icon" />
                  <span>Screenshot {i + 1}</span>
                </div>
              </div>
            {/each}
          </div>
        </div>
      {/each}
    </div>
  </section>
</div>

<style>
  /* ===== WRAPPER — tall so there's scroll room ===== */
  .projects-wrapper {
    position: relative;
    height: 300vh; /* 100vh per project + 100vh hold */
  }

  /* ===== STICKY SECTION — stays in view while wrapper scrolls ===== */
  .planet-projects-section {
    position: sticky;
    top: 0;
    width: 100%;
    height: 100vh;
    background: #020308;
    overflow: hidden;
    z-index: 10;
    opacity: 1;
    transform: none;
  }

  .projects-title {
    position: absolute;
    top: 6vh;
    left: 0;
    width: 100%;
    text-align: center;
    z-index: 20;
  }

  .projects-title h2 {
    font-size: 3rem;
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 15px;
    margin-bottom: 0.5rem;
    color: #fff;
  }

  .neon-text {
    color: var(--neon-blue);
    text-shadow: 0 0 15px rgba(0, 243, 255, 0.6);
  }

  .section-desc {
    font-family: 'Space Mono', monospace;
    color: var(--text-muted, #8b92ba);
    font-size: 1.1rem;
    margin: 0;
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

  /* ===== SLIDES ===== */
  .projects-container {
    position: absolute;
    top: 20vh;
    left: 0;
    width: 100%;
    height: 60vh;
    z-index: 10;
  }

  .project-slide {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    padding: 2vh 8vw 0;
    box-sizing: border-box;
    perspective: 1200px;
  }

  /* ===== INFO ===== */
  .project-info {
    flex: 0 0 45%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    color: var(--text-main, #d1d5eb);
    z-index: 10;
    position: relative;
  }

  .project-number {
    position: absolute;
    top: -30%;
    left: -5%;
    font-family: 'Space Mono', monospace;
    font-size: 8rem;
    font-weight: 800;
    color: rgba(255, 255, 255, 0.02);
    pointer-events: none;
    z-index: -1;
    line-height: 1;
  }

  .project-info h3 {
    font-size: 2.8rem;
    color: var(--starlight, #fff);
    line-height: 1.2;
    margin-bottom: 0.5rem;
  }

  .project-info h4 {
    font-size: 1.2rem;
    color: var(--neon-blue);
    margin-bottom: 1.5rem;
    font-weight: 400;
  }

  .date {
    font-family: 'Space Mono', monospace;
    font-size: 0.9rem;
    color: var(--star-gold, #ffd60a);
    display: inline-block;
    padding: 6px 16px;
    background: rgba(255, 214, 10, 0.1);
    border-radius: 20px;
    margin-bottom: 2rem;
    width: fit-content;
    text-shadow: 0 0 6px rgba(255, 214, 10, 0.3);
  }

  .desc {
    font-size: 1.1rem;
    line-height: 1.8;
    margin-bottom: 2rem;
    color: var(--text-main, #d1d5eb);
  }

  .tech-stack {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
  }

  .tech-tag {
    font-family: 'Space Mono', monospace;
    font-size: 0.85rem;
    padding: 6px 16px;
    border: 1px solid var(--nebula-purple);
    border-radius: 20px;
    background: rgba(157, 78, 221, 0.08);
    color: #fff;
    transition: all 0.3s ease;
  }

  /* ===== VISUALS ===== */
  .project-visuals {
    flex: 0 0 45%;
    height: 100%;
    position: relative;
    transform-style: preserve-3d;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .mockup-img {
    position: absolute;
    background: linear-gradient(135deg, rgba(27, 27, 47, 0.8), rgba(10, 10, 26, 0.9));
    border: 1px solid var(--glass-border, rgba(255, 255, 255, 0.1));
    border-radius: 16px;
    box-shadow: 0 15px 35px rgba(0, 0, 0, 0.6);
    display: flex;
    align-items: center;
    justify-content: center;
    backdrop-filter: blur(10px);
    transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
    cursor: crosshair;
  }

  .mockup-img:hover {
    border-color: var(--neon-blue);
    box-shadow: 0 0 30px rgba(0, 243, 255, 0.3);
    filter: brightness(1.2);
  }

  .img-1 { width: 70%; height: 60%; z-index: 4; transform: translateZ(50px) rotateY(-15deg); }
  .img-2 { width: 50%; height: 40%; top: 10%; right: -5%; z-index: 3; transform: translateZ(-20px) rotateY(-5deg); border-color: rgba(157, 78, 221, 0.5); }
  .img-3 { width: 45%; height: 35%; bottom: 5%; left: 0%; z-index: 5; transform: translateZ(100px) rotateY(-20deg); }
  .img-4 { width: 35%; height: 30%; top: 10%; left: 5%; z-index: 2; transform: translateZ(-60px) rotateY(10deg); }

  .placeholder-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    color: var(--text-muted, #8b92ba);
    font-family: 'Space Mono', monospace;
  }

  .placeholder-content .icon {
    width: 40px;
    height: 40px;
    opacity: 0.6;
    filter: drop-shadow(0 0 8px rgba(255, 255, 255, 0.2));
  }

  @media (max-width: 1024px) {
    .projects-wrapper {
      height: 400vh;
    }
    .project-slide {
      flex-direction: column;
      padding: 0 4vw;
      justify-content: center;
      gap: 2rem;
    }
    .project-info {
      flex: 0 0 auto;
      text-align: center;
      align-items: center;
    }
    .tech-stack { justify-content: center; }
    .project-visuals { flex: 0 0 40%; width: 100%; }
    .planet-horizon { width: 200vw; height: 200vw; margin-left: -100vw; }
    .project-number { display: none; }
    .project-info h3 { font-size: 2.2rem; }
    .desc { font-size: 1rem; }
  }
</style>
