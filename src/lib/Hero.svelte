<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import rocketIcon from '../assets/rocketicon.png';
  import SpaceMascot from './SpaceMascot.svelte';
  import Interactive3DScene from './Interactive3DScene.svelte';

  let heroContainer;
  let titleChars;
  let subtitle;
  let planet;
  let ctaBtn;
  let scrollIndicator;
  let asteroidsContainer;

  /**
   * Navigate to a section with View Transitions API support.
   * Falls back to smooth scroll when the API is not available.
   * @param {string} targetId
   */
  function navigateWithViewTransition(targetId) {
    const target = document.getElementById(targetId);
    if (!target) return;

    if (document.startViewTransition) {
      document.startViewTransition(() => {
        target.scrollIntoView({ behavior: "smooth" });
      });
    } else {
      target.scrollIntoView({ behavior: "smooth" });
    }
  }

  onMount(() => {
    // ===== HERO ENTRY TIMELINE =====
    const charEls = titleChars.querySelectorAll('.hero-char');
    let tl = gsap.timeline({ defaults: { ease: "power3.out" } });

    tl.from(heroContainer, { opacity: 0, duration: 0.8 })
      .from(
        planet,
        { scale: 0.3, opacity: 0, duration: 1.8, ease: "elastic.out(1, 0.5)" },
        "-=0.5",
      )
      // Typing effect - reveal chars one by one
      .from(charEls, {
        opacity: 0,
        y: 40,
        rotateX: -90,
        stagger: 0.04,
        duration: 0.6,
        ease: "back.out(1.7)",
      }, "-=1")
      .from(subtitle, { y: 30, opacity: 0, duration: 0.8 }, "-=0.4")
      .fromTo(ctaBtn, {
        y: 30,
        opacity: 0,
      }, {
        y: 0,
        opacity: 1,
        duration: 0.8,
        ease: "back.out(1.7)",
      }, "-=0.4")
      .from(scrollIndicator, {
        y: 20,
        opacity: 0,
        duration: 0.6,
      }, "-=0.2");

    // ===== PARALLAX PLANET ON MOUSE =====
    const planetMove = gsap.quickTo(planet, "x", { duration: 0.5, ease: "power2.out" });
    const planetMoveY = gsap.quickTo(planet, "y", { duration: 0.5, ease: "power2.out" });

    heroContainer.addEventListener("mousemove", (e) => {
      const rect = heroContainer.getBoundingClientRect();
      const centerX = rect.width / 2;
      const centerY = rect.height / 2;
      const mouseX = e.clientX - rect.left;
      const mouseY = e.clientY - rect.top;

      planetMove((mouseX - centerX) * 0.03);
      planetMoveY((mouseY - centerY) * 0.03);
    });

    heroContainer.addEventListener("mouseleave", () => {
      gsap.to(planet, { x: 0, y: 0, duration: 0.8, ease: "elastic.out(1, 0.5)" });
    });

    // ===== FLOATING ASTEROIDS =====
    for (let i = 0; i < 8; i++) {
      const asteroid = document.createElement("div");
      asteroid.classList.add("asteroid");
      const size = 4 + Math.random() * 12;
      asteroid.style.width = `${size}px`;
      asteroid.style.height = `${size}px`;

      const angle = (i / 8) * Math.PI * 2;
      const radius = 250 + Math.random() * 200;
      asteroid.style.left = `calc(50% + ${Math.cos(angle) * radius}px)`;
      asteroid.style.top = `calc(50% + ${Math.sin(angle) * radius}px)`;
      asteroidsContainer.appendChild(asteroid);

      gsap.to(asteroid, {
        rotation: 360,
        duration: 15 + Math.random() * 20,
        repeat: -1,
        ease: "none",
      });

      gsap.to(asteroid, {
        x: () => (Math.random() - 0.5) * 60,
        y: () => (Math.random() - 0.5) * 60,
        duration: 3 + Math.random() * 4,
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut",
        delay: Math.random() * 2,
      });
    }

    // ===== CTA BUTTON RIPPLE + VIEW TRANSITION =====
    ctaBtn.addEventListener("click", (e) => {
      const ripple = document.createElement("span");
      ripple.classList.add("ripple");
      const rect = ctaBtn.getBoundingClientRect();
      ripple.style.left = `${e.clientX - rect.left}px`;
      ripple.style.top = `${e.clientY - rect.top}px`;
      ripple.style.width = ripple.style.height = "10px";
      ctaBtn.appendChild(ripple);
      setTimeout(() => ripple.remove(), 600);

      // Use View Transitions API for smooth navigation
      navigateWithViewTransition("about");
    });

    // ===== SCROLL INDICATOR PULSE RINGS =====
    gsap.to(".pulse-ring", {
      scale: 2.5,
      opacity: 0,
      duration: 2,
      repeat: -1,
      stagger: 0.5,
      ease: "power1.out",
    });
  });

  // Split name into characters for typing animation
  const nameText = "Ahmad Hutri Semendawai";
  const nameChars = nameText.split("").map((char) =>
    char === " " ? { char: "\u00A0", isSpace: true } : { char, isSpace: false }
  );
</script>

<section class="hero-section" bind:this={heroContainer} id="hero">
  <div class="planet-wrapper">
    <div class="planet" bind:this={planet}>
      <div class="planet-ring"></div>
    </div>
    <div class="asteroids" bind:this={asteroidsContainer}></div>
  </div>

  <!-- Interactive 3D Scene (Feature 3: WebGL/3D) -->
  <div class="hero-3d-scene">
    <Interactive3DScene />
  </div>

  <div class="content">
    <h1 bind:this={titleChars}>
      {#each nameChars as { char }, i}
        <span class="hero-char">{char}</span>
      {/each}
    </h1>
    <h2 bind:this={subtitle}>
      <span class="role-badge">Software Engineer</span>
      <span class="separator">·</span>
      Java Programmer — ERP Specialist
    </h2>

    <button class="cta-btn ripple-effect" bind:this={ctaBtn}>
      <img src={rocketIcon} alt="rocket" class="cta-icon" width="24" height="24" loading="eager" decoding="async" />
      <span class="cta-text">Explore My Universe</span>
      <span class="cta-glow"></span>
    </button>

    <div class="scroll-indicator" bind:this={scrollIndicator}>
      <div class="mouse-wrapper">
        <div class="pulse-ring"></div>
        <div class="pulse-ring"></div>
        <div class="mouse">
          <div class="wheel"></div>
        </div>
      </div>
      <p>Scroll to explore</p>
    </div>
  </div>

  <!-- State Machine Mascot (Feature 4: Micro-Animations) -->
  <div class="hero-mascot">
    <SpaceMascot />
  </div>
</section>

<style>
  .hero-section {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    text-align: center;
    position: relative;
    padding-top: 0;
    opacity: 1;
    transform: none;
    overflow: hidden;
  }

  h1 {
    font-size: 3.5rem;
    margin-bottom: 0.75rem;
    color: var(--starlight);
    letter-spacing: -1.5px;
    perspective: 500px;
    font-weight: 700;
    text-shadow: 0 0 40px rgba(0, 212, 255, 0.2);
  }

  .hero-char {
    display: inline-block;
    will-change: transform, opacity;
  }

  @keyframes heroGradientShift {
    0% { background-position: 0% center; }
    50% { background-position: 100% center; }
    100% { background-position: 0% center; }
  }

  /* ===== PLANET ===== */
  .planet-wrapper {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    z-index: 0;
  }

  .planet {
    width: 60vw;
    height: 60vw;
    max-width: 800px;
    max-height: 800px;
    border-radius: 50%;
    background: radial-gradient(
      circle at 30% 30%,
      #8b5cf6,
      #3c096c 40%,
      #050510 80%
    );
    box-shadow:
      0 0 80px rgba(139, 92, 246, 0.25),
      0 0 40px rgba(0, 212, 255, 0.06),
      inset -50px -50px 100px rgba(0, 0, 0, 0.8);
    filter: blur(2px);
    opacity: 0.5;
    position: relative;
    will-change: transform;
  }

  .planet-ring {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%) rotateX(75deg);
    width: 130%;
    height: 130%;
    border-radius: 50%;
    border: 2px solid rgba(139, 92, 246, 0.15);
    box-shadow: 0 0 20px rgba(139, 92, 246, 0.1);
  }

  .asteroids {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
  }

  :global(.asteroid) {
    position: absolute;
    border-radius: 40% 60% 50% 40%;
    background: linear-gradient(135deg, #444, #222);
    box-shadow: 0 0 6px rgba(255, 255, 255, 0.1);
    will-change: transform;
  }

  /* ===== CONTENT ===== */
  .content {
    z-index: 2;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1rem;
  }

  h2 {
    font-size: 1.1rem;
    color: var(--text-muted);
    font-weight: 400;
    margin-bottom: 2.5rem;
    display: flex;
    align-items: center;
    gap: 10px;
    flex-wrap: wrap;
    justify-content: center;
  }

  .role-badge {
    display: inline-block;
    padding: 4px 14px;
    background: rgba(0, 212, 255, 0.08);
    border: 1px solid rgba(0, 212, 255, 0.2);
    border-radius: 50px;
    color: var(--neon-blue);
    font-size: 0.85rem;
    font-weight: 500;
    letter-spacing: 0.5px;
  }

  .separator {
    color: rgba(255, 255, 255, 0.15);
    font-size: 1.2rem;
  }

  /* ===== CTA BUTTON (Uiverse.io inspired) ===== */
  .cta-btn {
    position: relative;
    display: inline-flex;
    align-items: center;
    gap: 12px;
    padding: 14px 32px;
    min-width: 260px;
    min-height: 52px;
    font-family: 'Space Mono', monospace;
    font-size: 0.88rem;
    color: var(--starlight);
    background: rgba(0, 212, 255, 0.06);
    border: 1px solid rgba(0, 212, 255, 0.3);
    border-radius: 50px;
    cursor: pointer;
    overflow: hidden;
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    text-transform: uppercase;
    letter-spacing: 1.5px;
    z-index: 1;
    justify-content: center;
    box-shadow: 0 0 20px rgba(0, 212, 255, 0.08);
  }

  .cta-btn::before {
    content: '';
    position: absolute;
    top: 0;
    left: -100%;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, transparent, rgba(0, 212, 255, 0.1), transparent);
    transition: left 0.5s ease;
    z-index: -1;
  }

  .cta-btn:hover::before {
    left: 100%;
  }

  .cta-btn:hover {
    background: rgba(0, 212, 255, 0.1);
    border-color: var(--neon-blue);
    box-shadow:
      0 0 25px rgba(0, 212, 255, 0.15),
      0 0 50px rgba(0, 212, 255, 0.05);
    transform: translateY(-2px);
  }

  .cta-btn:active {
    transform: scale(0.97);
  }

  .cta-icon {
    display: block;
    object-fit: contain;
    transition: transform 0.3s ease;
  }

  .cta-btn:hover .cta-icon {
    transform: translateX(3px) rotate(-15deg);
  }

  .cta-glow {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 200%;
    height: 200%;
    transform: translate(-50%, -50%);
    background: radial-gradient(circle, rgba(0, 212, 255, 0.05) 0%, transparent 60%);
    pointer-events: none;
    animation: ctaGlowPulse 3s ease-in-out infinite;
  }

  @keyframes ctaGlowPulse {
    0%, 100% { opacity: 0.3; transform: translate(-50%, -50%) scale(1); }
    50% { opacity: 0.8; transform: translate(-50%, -50%) scale(1.1); }
  }

  /* ===== SCROLL INDICATOR ===== */
  .scroll-indicator {
    position: absolute;
    bottom: 40px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    opacity: 0.7;
  }

  .scroll-indicator p {
    font-family: "Space Mono", monospace;
    font-size: 0.7rem;
    color: var(--text-muted);
    text-transform: uppercase;
    letter-spacing: 3px;
  }

  .mouse-wrapper {
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .pulse-ring {
    position: absolute;
    width: 50px;
    height: 70px;
    border: 1px solid rgba(0, 212, 255, 0.3);
    border-radius: 25px;
  }

  .mouse {
    width: 30px;
    height: 50px;
    border: 2px solid var(--neon-blue);
    border-radius: 15px;
    position: relative;
    box-shadow: 0 0 10px rgba(0, 212, 255, 0.2);
  }

  .wheel {
    width: 4px;
    height: 8px;
    background-color: var(--neon-blue);
    border-radius: 2px;
    position: absolute;
    top: 10px;
    left: 50%;
    transform: translateX(-50%);
    animation: scrollWheel 1.5s infinite;
    box-shadow: 0 0 6px var(--neon-blue);
  }

  @keyframes scrollWheel {
    0% { top: 10px; opacity: 1; }
    100% { top: 30px; opacity: 0; }
  }

  @media (max-width: 768px) {
    h1 {
      font-size: 2.5rem;
    }
    h2 {
      font-size: 1.2rem;
    }
    .cta-btn {
      padding: 12px 24px;
      font-size: 0.85rem;
    }
    .hero-3d-scene {
      display: none;
    }
    .hero-mascot {
      display: none;
    }
  }

  /* ===== Interactive 3D Scene position ===== */
  .hero-3d-scene {
    position: absolute;
    top: 10%;
    right: 2%;
    z-index: 1;
    opacity: 0.8;
    pointer-events: none;
    animation: scene3dFloat 6s ease-in-out infinite;
  }

  @keyframes scene3dFloat {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-12px); }
  }

  /* ===== Space Mascot position ===== */
  .hero-mascot {
    position: absolute;
    bottom: 12%;
    left: 5%;
    z-index: 3;
    opacity: 0.9;
  }

  /* ===== View Transition Names ===== */
  .planet {
    view-transition-name: hero-planet;
  }

  h1 {
    view-transition-name: hero-title;
  }

  /* ===== Scroll-Driven: Hero parallax shift ===== */
  @supports (animation-timeline: scroll()) {
    .planet-wrapper {
      animation: hero-planet-parallax linear both;
      animation-timeline: scroll(root);
      animation-range: 0% 30%;
    }

    @keyframes hero-planet-parallax {
      from { transform: translate(-50%, -50%) scale(1); }
      to { transform: translate(-50%, -60%) scale(0.9); opacity: 0.3; }
    }

    .scroll-indicator {
      animation: scroll-indicator-fade linear both;
      animation-timeline: scroll(root);
      animation-range: 0% 10%;
    }

    @keyframes scroll-indicator-fade {
      from { opacity: 0.7; transform: translateX(-50%) translateY(0); }
      to { opacity: 0; transform: translateX(-50%) translateY(20px); }
    }
  }
</style>
