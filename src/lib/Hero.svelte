<script>
  import { onMount } from "svelte";
  import gsap from "gsap";

  let heroRef;
  let titleRef1;
  let titleRef2;

  onMount(() => {
    const tl = gsap.timeline();
    
    tl.fromTo(titleRef1, 
      { y: 150, rotate: 5, opacity: 0 },
      { y: 0, rotate: 0, opacity: 1, duration: 1.2, ease: "power4.out" }
    )
    .fromTo(titleRef2,
      { y: 150, rotate: -5, opacity: 0 },
      { y: 0, rotate: 0, opacity: 1, duration: 1.2, ease: "power4.out" },
      "-=1"
    );
  });
</script>

<section class="hero" bind:this={heroRef}>
  <div class="video-overlay"></div>
  <!-- Abstract CSS background representing "Aether" instead of video for now -->
  <div class="aether-bg"></div>

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
