<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import rocketIcon from '../assets/rocketicon.png';
  import KineticText from './KineticText.svelte';

  gsap.registerPlugin(ScrollTrigger);

  let sectionRef;
  let planetRef;
  let carouselRef;
  /** @type {HTMLElement[]} */
  let slideRefs = [];

  // Lightbox state
  let lightboxOpen = $state(false);
  let lightboxSrc = $state('');
  let lightboxAlt = $state('');

  // Carousel state
  let currentIndex = $state(0);
  let prevIndex = 0;
  let isAnimating = false;

  function openLightbox(src, alt) {
    lightboxSrc = src;
    lightboxAlt = alt;
    lightboxOpen = true;
  }

  function closeLightbox() {
    lightboxOpen = false;
  }

  function handleOverlayClick(e) {
    // Close when clicking the overlay itself (not children)
    if (e.target === e.currentTarget) {
      closeLightbox();
    }
  }

  function next() {
    if (isAnimating) return;
    currentIndex = (currentIndex + 1) % projects.length;
  }

  function prev() {
    if (isAnimating) return;
    currentIndex = (currentIndex - 1 + projects.length) % projects.length;
  }

  function goTo(index) {
    if (isAnimating || index === currentIndex) return;
    currentIndex = index;
  }

  function handleKeydown(e) {
    if (e.key === 'Escape' && lightboxOpen) {
      closeLightbox();
    }
    if (!lightboxOpen) {
      if (e.key === 'ArrowLeft') { e.preventDefault(); prev(); }
      if (e.key === 'ArrowRight') { e.preventDefault(); next(); }
    }
  }

  // ── 3D Coverflow slide transition ───────────────────────────────────────
  function animateSlideTransition(fromIdx, toIdx) {
    if (!slideRefs[fromIdx] || !slideRefs[toIdx]) return;
    isAnimating = true;

    const total = projects.length;
    let d = toIdx - fromIdx;
    if (d > total / 2) d -= total;
    if (d < -total / 2) d += total;
    const dir = d > 0 ? 1 : -1;
    const dur = 0.65;

    // EXIT: current slide tilts to the side and fades out
    gsap.to(slideRefs[fromIdx], {
      x: dir * -38 + '%',
      rotateY: dir * -30,
      scale: 0.82,
      opacity: 0,
      duration: dur * 0.75,
      ease: 'power2.in',
      onComplete() { gsap.set(slideRefs[fromIdx], { pointerEvents: 'none' }); },
    });

    // ENTER: new slide arrives from the opposite side
    gsap.set(slideRefs[toIdx], {
      x: dir * 45 + '%',
      rotateY: dir * 28,
      scale: 0.82,
      opacity: 0,
      pointerEvents: 'none',
    });
    gsap.to(slideRefs[toIdx], {
      x: '0%',
      rotateY: 0,
      scale: 1,
      opacity: 1,
      pointerEvents: 'all',
      duration: dur,
      ease: 'power3.out',
      delay: 0.08,
      onComplete() { isAnimating = false; },
    });

    // LAYERED PARALLAX: info and visuals animate at different rates
    const infoEl = slideRefs[toIdx].querySelector('.project-info');
    const visualsEl = slideRefs[toIdx].querySelector('.project-visuals');
    if (infoEl) {
      gsap.fromTo(infoEl,
        { x: dir * 65, opacity: 0, y: 10 },
        { x: 0, opacity: 1, y: 0, duration: dur, ease: 'power2.out', delay: 0.12 },
      );
    }
    if (visualsEl) {
      gsap.fromTo(visualsEl,
        { x: dir * 110, opacity: 0, scale: 0.92 },
        { x: 0, opacity: 1, scale: 1, duration: dur * 1.15, ease: 'back.out(1.4)', delay: 0.22 },
      );
    }

    // TECH TAG STAGGER: tags animate in sequentially
    const techTags = slideRefs[toIdx].querySelectorAll('.tech-tag');
    if (techTags.length) {
      gsap.fromTo(techTags,
        { y: 12, opacity: 0 },
        { y: 0, opacity: 1, duration: 0.35, stagger: 0.06, ease: 'power1.out', delay: 0.4 },
      );
    }
  }

  $effect(() => {
    const newIdx = currentIndex;
    if (newIdx === prevIndex) return;
    animateSlideTransition(prevIndex, newIdx);
    prevIndex = newIdx;
  });

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
    {
      title: "Nota360",
      subtitle: "Sales Invoice Application",
      date: "Mar 2026",
      desc: "Nota360 is a web-based sales invoice application built for PT Karya Trita Sejahtera to simplify creating, storing, and printing professional sales receipts for food ingredient deliveries. Staff can quickly compose multi-item invoices, manage customer and product data, and export print-ready PDF files with company branding and electronic signatures.",
      tech: ["Svelte 5", "Vite", "PocketBase", "jsPDF", "JavaScript", "PDF Export"],
      images: [
        "https://github.com/user-attachments/assets/6802acfa-7235-4983-a133-152c8bb5fadd",
        "https://github.com/user-attachments/assets/2f1ddba7-b6e5-40ea-ad03-077b4d984b49",
        "https://github.com/user-attachments/assets/897e19cb-85a5-44b4-b612-5d1712a1fe2d",
      ],
    },
  ];

  onMount(() => {
    const triggers = [];

    // Initialize GSAP state for all slides
    slideRefs.forEach((slide, i) => {
      if (!slide) return;
      if (i === 0) {
        gsap.set(slide, { opacity: 1, x: 0, rotateY: 0, scale: 1, pointerEvents: 'all' });
      } else {
        gsap.set(slide, { opacity: 0, x: 0, rotateY: 0, scale: 0.9, pointerEvents: 'none' });
      }
    });

    const planetTl = gsap.timeline({
      scrollTrigger: { trigger: sectionRef, start: 'top bottom', end: 'bottom top', scrub: 1 },
    });
    planetTl.to(planetRef, { rotation: 60, scale: 1.08, ease: 'none' });
    triggers.push(planetTl.scrollTrigger);

    // Set initial GSAP transform to include the centering offset
    gsap.set(planetRef, { xPercent: -50 });

    // Entry animation for the carousel as a whole
    const entryTween = gsap.fromTo(
      carouselRef,
      { opacity: 0, y: 40 },
      {
        opacity: 1,
        y: 0,
        duration: 0.7,
        ease: 'power2.out',
        scrollTrigger: {
          trigger: carouselRef,
          start: 'top 85%',
          toggleActions: 'play none none none',
        },
      },
    );

    // Stagger-in initial tech tags when the carousel scrolls into view
    const initialTags = slideRefs[0]?.querySelectorAll('.tech-tag');
    if (initialTags?.length) {
      gsap.fromTo(initialTags,
        { y: 12, opacity: 0 },
        {
          y: 0, opacity: 1, duration: 0.35, stagger: 0.06, ease: 'power1.out', delay: 0.5,
          scrollTrigger: { trigger: carouselRef, start: 'top 85%' },
        },
      );
    }

    return () => {
      triggers.forEach(t => t.kill());
      entryTween.kill();
    };
  });
</script>

<div class="projects-wrapper" bind:this={sectionRef}>
  <section class="projects-section" id="projects">

    <!-- Planet integrated as background decoration -->
    <div class="planet-container" bind:this={planetRef}>
      <div class="planet-glow"></div>
      <div class="planet-surface"></div>
      <div class="planet-ring-1"></div>
      <div class="planet-ring-2"></div>
      <div class="planet-decorator dec-1"></div>
      <div class="planet-decorator dec-2"></div>
      <div class="planet-decorator dec-3"></div>
    </div>

    <div class="section-header">
      <span class="section-label">Portfolio</span>
      <h2>Featured Projects</h2>
      <p class="section-sub">Exploring my universe of applications.</p>
      <!-- Feature 5: Kinetic Typography (wave variant) -->
      <div class="kinetic-projects-label">
        <KineticText text="BUILD · DEPLOY · INNOVATE" variant="wave" />
      </div>
    </div>

    <div class="carousel-outer" bind:this={carouselRef}
         role="region"
         aria-label="Featured projects carousel">

      <div class="carousel-viewport">
        {#each projects as project, index}
          <div class="project-slide" bind:this={slideRefs[index]}>

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
                {#if project.images[0]?.startsWith('http')}
                  {#if project.images[0]}
                    <button class="card card-main card-clickable" type="button" onclick={() => openLightbox(project.images[0], `${project.title} screenshot 1`)}>
                      <img src={project.images[0]} alt="{project.title} screenshot 1" class="card-screenshot" />
                    </button>
                  {/if}
                  {#if project.images[1]}
                    <button class="card card-top card-clickable" type="button" onclick={() => openLightbox(project.images[1], `${project.title} screenshot 2`)}>
                      <img src={project.images[1]} alt="{project.title} screenshot 2" class="card-screenshot" />
                    </button>
                  {/if}
                  {#if project.images[2]}
                    <button class="card card-btm card-clickable" type="button" onclick={() => openLightbox(project.images[2], `${project.title} screenshot 3`)}>
                      <img src={project.images[2]} alt="{project.title} screenshot 3" class="card-screenshot" />
                    </button>
                  {/if}
                {:else}
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
                {/if}
              </div>

            </div>
          {/each}
      </div>

      <!-- Navigation arrows -->
      <button class="carousel-btn carousel-prev" type="button" onclick={prev} aria-label="Previous project">&#8249;</button>
      <button class="carousel-btn carousel-next" type="button" onclick={next} aria-label="Next project">&#8250;</button>

      <!-- Slide counter + dots -->
      <div class="carousel-footer">
        <span class="carousel-counter">
          {(currentIndex + 1).toString().padStart(2, '0')} / {projects.length.toString().padStart(2, '0')}
        </span>
        <div class="carousel-dots">
          {#each projects as _, i}
            <button
              class="carousel-dot"
              class:active={i === currentIndex}
              type="button"
              onclick={() => goTo(i)}
              aria-label="Go to project {i + 1}"
            ></button>
          {/each}
        </div>
      </div>

    </div>

  </section>
</div>

<!-- Lightbox Modal -->
<svelte:window onkeydown={handleKeydown} />
{#if lightboxOpen}
  <!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
  <!-- svelte-ignore a11y_interactive_supports_focus -->
  <!-- svelte-ignore a11y_click_events_have_key_events -->
  <div class="lightbox-overlay" role="dialog" aria-modal="true" aria-label="Screenshot detail view" tabindex="-1" onclick={handleOverlayClick}>
    <button class="lightbox-close" type="button" onclick={closeLightbox} aria-label="Close">✕</button>
    <div class="lightbox-content">
      <img src={lightboxSrc} alt={lightboxAlt} class="lightbox-image" />
    </div>
  </div>
{/if}

<style>
  /* ===== WRAPPER ===== */
  .projects-wrapper {
    position: relative;
    width: 100vw;
    margin-left: calc(-50vw + 50%);
    overflow: hidden;
  }

  /* ===== SECTION ===== */
  .projects-section {
    width: 100%;
    background: var(--space-black);
    overflow: visible;
    z-index: 10;
    opacity: 1;
    transform: none;
    display: block;
    padding: 0 0 6rem;
    min-height: auto;
    position: relative;
  }

  /* Gradient fade at top for smooth entry from previous section. */
  .projects-section::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 120px;
    background: linear-gradient(to bottom, #050510, rgba(5, 5, 16, 0));
    pointer-events: none;
    z-index: 1;
  }

  /* ===== HEADER ===== */
  .section-header {
    padding: 5rem 0 3rem;
    text-align: center;
    z-index: 20;
    position: relative;
  }

  .section-header h2 {
    font-size: 2.5rem;
    color: #fff;
    margin: 0 0 0.35rem;
    letter-spacing: -1px;
  }

  .section-sub {
    font-family: 'Space Mono', monospace;
    color: var(--text-muted, #8b92ba);
    font-size: 0.95rem;
    margin: 0;
  }

  .kinetic-projects-label {
    margin-top: 1rem;
    opacity: 0.7;
  }

  /* ===== CAROUSEL ===== */
  .carousel-outer {
    position: relative;
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 4rem 5rem;
    z-index: 10;
  }

  .carousel-viewport {
    position: relative;
    height: 420px;
    overflow: hidden;
    border-radius: 4px;
    perspective: 1400px;
    perspective-origin: center center;
  }

  /* carousel-track removed — slides are now absolutely positioned */

  .project-slide {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    gap: 3vw;
    box-sizing: border-box;
    padding: 0.5rem 2vw 1rem;
    will-change: transform, opacity;
    user-select: none;
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
    z-index: 0;
  }

  .project-info h3 {
    font-size: 2rem;
    color: #fff;
    line-height: 1.25;
    margin: 0 0 0.35rem;
  }

  .project-info h4 {
    font-size: 1rem;
    color: var(--neon-blue, #00d4ff);
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
    font-size: 0.72rem;
    padding: 4px 12px;
    border: 1px solid rgba(139, 92, 246, 0.2);
    border-radius: 20px;
    background: rgba(139, 92, 246, 0.06);
    color: var(--text-muted);
    letter-spacing: 0.3px;
  }

  /* ===== PROJECT VISUALS ===== */
  .project-visuals {
    flex: 0 0 48%;
    height: 340px;
    position: relative;
    overflow: visible;
  }

  .card {
    position: absolute;
    background: linear-gradient(135deg, rgba(20, 20, 45, 0.9), rgba(10, 10, 26, 0.95));
    border: 1px solid rgba(255, 255, 255, 0.06);
    border-radius: 16px;
    box-shadow: 0 12px 30px rgba(0, 0, 0, 0.4);
    display: flex;
    align-items: center;
    justify-content: center;
    backdrop-filter: blur(8px);
    transition: border-color 0.3s ease, box-shadow 0.3s ease;
    overflow: hidden;
  }

  .card:hover {
    border-color: rgba(0, 212, 255, 0.25);
    box-shadow: 0 0 20px rgba(0, 212, 255, 0.1);
  }

  /* Main card — centered, dominant */
  .card-main {
    width: 62%;
    height: 56%;
    top: 50%;
    left: 50%;
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
    border-color: rgba(139, 92, 246, 0.4);
  }

  /* Bottom-left tertiary */
  .card-btm {
    width: 40%;
    height: 34%;
    bottom: 6%;
    left: 1%;
    transform: rotate(-5deg);
    z-index: 2;
    border-color: rgba(0, 212, 255, 0.3);
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

  .card-screenshot {
    width: 100%;
    height: 100%;
    object-fit: cover;
    border-radius: 16px;
  }

  /* ===== PLANET — integrated background ===== */
  .planet-container {
    position: absolute;
    bottom: -30%;
    left: 50%;
    width: 120vw;
    height: 120vw;
    border-radius: 50%;
    pointer-events: none;
    z-index: 1;
    background: radial-gradient(circle at 50% 10%, #060913 0%, #010206 60%);
    box-shadow:
      inset 0 15px 50px rgba(0, 212, 255, 0.15),
      inset 0 -100px 100px #000,
      0 0 120px rgba(0, 212, 255, 0.08);
    border-top: 2px solid rgba(0, 212, 255, 0.35);
    overflow: visible;
    transform-origin: center center;
    opacity: 0.7;
  }

  .planet-glow {
    position: absolute;
    top: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 60%;
    height: 80px;
    background: radial-gradient(ellipse at center, rgba(0, 212, 255, 0.45) 0%, transparent 70%);
    filter: blur(25px);
    z-index: 1;
  }

  .planet-surface {
    position: absolute;
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background-image:
      linear-gradient(rgba(255, 255, 255, 0.04) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255, 255, 255, 0.04) 1px, transparent 1px);
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
    border: 1px dashed rgba(0, 212, 255, 0.2);
    z-index: 1;
  }

  .planet-ring-2 {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 94%;
    height: 94%;
    transform: translate(-50%, -50%);
    border-radius: 50%;
    border: 2px solid rgba(139, 92, 246, 0.08);
    border-top: 2px solid rgba(139, 92, 246, 0.35);
    border-bottom: 2px solid rgba(139, 92, 246, 0.35);
    z-index: 1;
  }

  .planet-decorator {
    position: absolute;
    background: var(--neon-blue);
    box-shadow: 0 0 15px var(--neon-blue);
    border-radius: 10px;
    z-index: 2;
  }
  .dec-1 { top: 5%; left: 30%; width: 80px; height: 3px; transform: rotate(15deg); }
  .dec-2 { top: 8%; right: 25%; width: 40px; height: 3px; transform: rotate(-20deg); background: var(--nebula-purple); box-shadow: 0 0 15px var(--nebula-purple); }
  .dec-3 { top: 1%; left: 55%; width: 6px; height: 6px; border-radius: 50%; }

  /* ===== CAROUSEL NAVIGATION ===== */
  .carousel-btn {
    position: absolute;
    top: 44%;
    transform: translateY(-50%);
    background: rgba(5, 5, 16, 0.75);
    border: 1px solid rgba(0, 212, 255, 0.3);
    color: #fff;
    width: 46px;
    height: 46px;
    border-radius: 50%;
    font-size: 2rem;
    line-height: 1;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background 0.2s ease, border-color 0.2s ease, box-shadow 0.2s ease, transform 0.2s ease;
    z-index: 20;
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    padding: 0;
  }

  .carousel-prev { left: 0; }
  .carousel-next { right: 0; }

  .carousel-btn:hover {
    background: rgba(0, 212, 255, 0.12);
    border-color: rgba(0, 212, 255, 0.6);
    box-shadow: 0 0 16px rgba(0, 212, 255, 0.15);
    transform: translateY(-50%) scale(1.1);
  }

  .carousel-btn:active {
    transform: translateY(-50%) scale(0.95);
  }

  /* ===== CAROUSEL FOOTER (counter + dots) ===== */
  .carousel-footer {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 1.5rem;
    padding-top: 2rem;
  }

  .carousel-counter {
    font-family: 'Space Mono', monospace;
    font-size: 0.78rem;
    color: var(--text-muted, #8b92ba);
    letter-spacing: 2px;
    min-width: 3.5rem;
    text-align: center;
  }

  .carousel-dots {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .carousel-dot {
    width: 8px;
    height: 8px;
    border-radius: 4px;
    border: none;
    background: rgba(255, 255, 255, 0.2);
    cursor: pointer;
    padding: 0;
    transition: background 0.3s ease, width 0.3s ease, box-shadow 0.3s ease;
  }

  .carousel-dot.active {
    width: 24px;
    background: var(--neon-blue, #00d4ff);
    box-shadow: 0 0 8px rgba(0, 212, 255, 0.5);
  }

  .carousel-dot:hover:not(.active) {
    background: rgba(255, 255, 255, 0.4);
  }

  /* ===== RESPONSIVE ===== */
  @media (max-width: 1024px) {
    .carousel-outer { padding: 0 3rem 4rem; }
    .carousel-viewport { height: 660px; }

    .project-slide {
      flex-direction: column;
      gap: 1.5rem;
    }

    .project-info { flex: 0 0 auto; text-align: center; }
    .project-info h3 { font-size: 1.6rem; }
    .desc { font-size: 0.85rem; }
    .tech-stack { justify-content: center; }
    .proj-num { display: none; }

    .project-visuals { flex: 0 0 auto; width: 100%; height: 260px; }

    .planet-container {
      width: 160vw;
      height: 160vw;
      bottom: -35%;
    }
  }

  @media (max-width: 768px) {
    .carousel-outer { padding: 0 2.5rem 3.5rem; }
    .carousel-viewport { height: 580px; }

    .section-header h2 { font-size: 2rem; }
    .section-sub { font-size: 0.85rem; }

    .project-info h3 { font-size: 1.4rem; }
    .project-info h4 { font-size: 0.9rem; }
    .desc { font-size: 0.8rem; line-height: 1.65; }

    .date-badge { font-size: 0.72rem; }
    .tech-tag { font-size: 0.7rem; padding: 3px 10px; }

    .project-visuals { height: 220px; }

    .card-main { width: 60%; height: 54%; }
    .card-top { width: 40%; height: 34%; }
    .card-btm { width: 38%; height: 32%; }

    .carousel-btn { width: 36px; height: 36px; font-size: 1.5rem; }

    .planet-container {
      width: 180vw;
      height: 180vw;
      bottom: -30%;
    }
  }

  /* ===== Scroll-Driven: Planet parallax ===== */
  @supports (animation-timeline: scroll()) {
    .planet-container {
      animation: sda-planet-rotate linear both;
      animation-timeline: scroll(root);
    }

    @keyframes sda-planet-rotate {
      from { transform: translateX(-50%) rotate(0deg); }
      to { transform: translateX(-50%) rotate(30deg); }
    }
  }

  /* ===== CLICKABLE CARDS ===== */
  .card-clickable {
    cursor: pointer;
    padding: 0;
    font: inherit;
    color: inherit;
    transition: transform 0.3s ease, border-color 0.3s ease, box-shadow 0.3s ease;
  }

  .card-clickable:hover {
    transform: scale(1.05);
    border-color: rgba(0, 212, 255, 0.4);
    box-shadow: 0 0 24px rgba(0, 212, 255, 0.2), 0 12px 30px rgba(0, 0, 0, 0.5);
  }

  .card-clickable.card-main:hover {
    transform: translate(-50%, -50%) rotate(-2deg) scale(1.05);
  }

  .card-clickable:active {
    transform: scale(0.97);
  }

  .card-clickable.card-main:active {
    transform: translate(-50%, -50%) rotate(-2deg) scale(0.97);
  }

  /* ===== LIGHTBOX ===== */
  .lightbox-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.85);
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 10000;
    animation: lightbox-fade-in 0.3s ease forwards;
    cursor: pointer;
  }

  .lightbox-content {
    position: relative;
    max-width: 90vw;
    max-height: 85vh;
    animation: lightbox-zoom-in 0.35s cubic-bezier(0.16, 1, 0.3, 1) forwards;
    cursor: default;
  }

  .lightbox-image {
    display: block;
    max-width: 100%;
    max-height: 85vh;
    border-radius: 12px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.6), 0 0 40px rgba(0, 212, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.1);
    object-fit: contain;
  }

  .lightbox-close {
    position: fixed;
    top: 1.5rem;
    right: 1.5rem;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    border: 1px solid rgba(255, 255, 255, 0.2);
    background: rgba(5, 5, 16, 0.8);
    color: #fff;
    font-size: 1.2rem;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background 0.2s ease, border-color 0.2s ease, transform 0.2s ease;
    z-index: 10001;
    backdrop-filter: blur(4px);
    -webkit-backdrop-filter: blur(4px);
  }

  .lightbox-close:hover {
    background: rgba(255, 255, 255, 0.1);
    border-color: rgba(0, 212, 255, 0.5);
    transform: rotate(90deg);
  }

  @keyframes lightbox-fade-in {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  @keyframes lightbox-zoom-in {
    from {
      opacity: 0;
      transform: scale(0.85);
    }
    to {
      opacity: 1;
      transform: scale(1);
    }
  }
</style>
