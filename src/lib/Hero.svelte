<script>
  import { onMount } from "svelte";
  import gsap from "gsap";

  let heroRef;
  let titleRef1;
  let titleRef2;
  let heroCursorRef;

  onMount(() => {
    if (!heroRef || !titleRef1 || !titleRef2) {
      return;
    }

    const ctx = gsap.context(() => {
      gsap.timeline()
        .fromTo(titleRef1, 
          { y: 150, rotate: 5, opacity: 0 },
          { y: 0, rotate: 0, opacity: 1, duration: 1.2, ease: "power4.out" }
        )
        .fromTo(titleRef2,
          { y: 150, rotate: -5, opacity: 0 },
          { y: 0, rotate: 0, opacity: 1, duration: 1.2, ease: "power4.out" },
          "-=1"
        );
    }, heroRef);

    let cleanupCursor = () => {};

    if (heroCursorRef && !window.matchMedia("(pointer: coarse)").matches) {
      const moveCursorX = gsap.quickTo(heroCursorRef, "left", { duration: 0.45, ease: "power3.out" });
      const moveCursorY = gsap.quickTo(heroCursorRef, "top", { duration: 0.45, ease: "power3.out" });
      const rotateCursor = gsap.quickTo(heroCursorRef, "rotation", { duration: 0.6, ease: "power3.out" });

      const showCursor = () => {
        gsap.to(heroCursorRef, { opacity: 1, scale: 1, duration: 0.35, ease: "power3.out", overwrite: "auto" });
      };

      const hideCursor = () => {
        gsap.to(heroCursorRef, { opacity: 0, scale: 0.55, rotation: 0, duration: 0.25, ease: "power2.in", overwrite: "auto" });
      };

      const handlePointerMove = (event) => {
        const bounds = heroRef.getBoundingClientRect();
        const x = event.clientX - bounds.left;
        const y = event.clientY - bounds.top;

        moveCursorX(x);
        moveCursorY(y);

        const tilt = gsap.utils.clamp(-14, 14, (x - bounds.width / 2) / 28);
        rotateCursor(tilt);
      };

      heroRef.addEventListener("pointerenter", showCursor);
      heroRef.addEventListener("pointerleave", hideCursor);
      heroRef.addEventListener("pointermove", handlePointerMove);

      cleanupCursor = () => {
        heroRef.removeEventListener("pointerenter", showCursor);
        heroRef.removeEventListener("pointerleave", hideCursor);
        heroRef.removeEventListener("pointermove", handlePointerMove);
        gsap.killTweensOf(heroCursorRef);
      };
    }

    return () => {
      cleanupCursor();
      ctx.revert();
    };
  });
</script>

<section class="hero" bind:this={heroRef}>
  <div class="video-overlay"></div>
  <!-- Abstract CSS background representing "Aether" instead of video for now -->
  <div class="aether-bg"></div>
  <div class="hero-cursor-card" bind:this={heroCursorRef}>
    <span class="hero-cursor-dot"></span>
  </div>

  <div class="hero-content container">
    <div class="title-mask">
      <h1 class="hero-title" bind:this={titleRef1}>I CAPTURE</h1>
    </div>
    <div class="title-mask title-right">
      <h1 class="hero-title text-stroke" bind:this={titleRef2}>THE FUTURE.</h1>
    </div>
  </div>

  <div class="scroll-indicator">
    <p>Scroll to explore</p>
    <div class="line"></div>
  </div>
</section>

<style>
  .hero {
    height: 100vh;
    display: flex;
    align-items: center;
    padding: 0;
    position: relative;
    background: #0a0a0a;
  }

  .aether-bg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: radial-gradient(circle at 50% 50%, rgba(255, 51, 51, 0.1) 0%, rgba(0,0,0,0) 50%);
    opacity: 0.8;
  }

  .video-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: url('data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSI0IiBoZWlnaHQ9IjQiPgo8cmVjdCB3aWR0aD0iNCIgaGVpZ2h0PSI0IiBmaWxsPSIjZmZmIiBmaWxsLW9wYWNpdHk9IjAuMDUiLz4KPC9zdmc+') repeat;
    opacity: 0.5;
    z-index: 1;
  }

  .hero-cursor-card {
    position: absolute;
    top: 0;
    left: 0;
    width: clamp(120px, 14vw, 190px);
    aspect-ratio: 1 / 1;
    border-radius: 14px;
    background: #dbe93f;
    box-shadow: 0 16px 40px rgba(0, 0, 0, 0.35);
    opacity: 0;
    pointer-events: none;
    transform: translate(-50%, -50%) scale(0.55);
    z-index: 2;
    will-change: left, top, transform, opacity;
  }

  .hero-cursor-dot {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 10px;
    height: 10px;
    border-radius: 50%;
    background: #1a28ff;
    box-shadow: 0 0 18px rgba(26, 40, 255, 0.6);
    transform: translate(-50%, -50%);
  }

  .hero-content {
    position: relative;
    z-index: 2;
    display: flex;
    flex-direction: column;
    gap: 0;
    width: 100%;
    max-width: 100%;
    padding: 0 4rem;
    padding-bottom: 8rem;
  }

  .title-mask {
    overflow-y: hidden;
    overflow-x: visible;
    padding-bottom: 2rem;
    margin-bottom: -2rem; /* Account for over-descending parts */
  }

  .title-right {
    text-align: right;
  }

  .hero-title {
    font-size: clamp(2.75rem, 7.8vw, 8rem);
    color: #f5f5f5;
    line-height: 0.86;
    white-space: nowrap;
    letter-spacing: -0.05em;
  }

  @media (max-width: 1200px) {
    .hero-title {
      font-size: clamp(2.6rem, 8.2vw, 7rem);
    }
  }

  @media (max-width: 900px) {
    .hero-content {
      padding: 0 1.5rem;
      padding-bottom: 6rem;
    }

    .hero-cursor-card {
      display: none;
    }

    .hero-title {
      font-size: clamp(2.5rem, 15vw, 5rem);
      white-space: normal;
      letter-spacing: -0.04em;
    }
  }

  @media (max-width: 560px) {
    .hero-title {
      font-size: clamp(2.1rem, 15.5vw, 3.9rem);
      line-height: 0.9;
      letter-spacing: -0.03em;
    }
  }

  .scroll-indicator {
    position: absolute;
    bottom: 3rem;
    left: 50%;
    transform: translateX(-50%);
    z-index: 2;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1rem;
    opacity: 0.7;
  }

  .scroll-indicator p {
    font-family: 'Space Mono', monospace;
    font-size: 0.75rem;
    text-transform: uppercase;
    letter-spacing: 2px;
  }

  .line {
    width: 1px;
    height: 60px;
    background: linear-gradient(to bottom, rgba(255,255,255,1), rgba(255,255,255,0));
  }
</style>
