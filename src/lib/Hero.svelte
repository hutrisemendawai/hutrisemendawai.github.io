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
      <h1 class="text-huge" bind:this={titleRef1}>I CAPTURE</h1>
    </div>
    <div class="title-mask title-right">
      <h1 class="text-huge text-stroke" bind:this={titleRef2}>THE FUTURE.</h1>
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
    padding: 0 4rem;
    padding-bottom: 8rem;
  }

  .title-mask {
    overflow: hidden;
    padding-bottom: 2rem;
    margin-bottom: -2rem; /* Account for over-descending parts */
  }

  .title-right {
    text-align: right;
  }

  .text-huge {
    font-size: clamp(4rem, 15vw, 14rem);
    color: #f5f5f5;
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
