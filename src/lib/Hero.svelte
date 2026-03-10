<script>
  import { onMount } from 'svelte';
  import gsap from 'gsap';

  let heroContainer;
  let title;
  let subtitle;
  let planet;
  let astronaut;

  onMount(() => {
    let tl = gsap.timeline({ defaults: { ease: 'power3.out' } });
    tl.from(heroContainer, { opacity: 0, duration: 1 })
      .from(planet, { scale: 0.5, opacity: 0, duration: 1.5, ease: "back.out(1.5)" }, "-=0.8")
      .from(title, { y: 50, opacity: 0, duration: 1 }, "-=1")
      .from(subtitle, { y: 30, opacity: 0, duration: 0.8 }, "-=0.8");
  });
</script>

<section class="hero-section" bind:this={heroContainer} id="hero">
  <div class="planet" bind:this={planet}></div>
  <div class="content">
    <h1 bind:this={title}>Ahmad Hutri Semendawai</h1>
    <h2 bind:this={subtitle}>He/Him | Java Programmer - ERP</h2>
    <div class="scroll-indicator">
      <div class="mouse">
        <div class="wheel"></div>
      </div>
      <p>Scroll to explore Hutri-SpacePlanet</p>
    </div>
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
  }
  h1 {
    font-size: 4rem;
    margin-bottom: 0.5rem;
    background: linear-gradient(90deg, #fff, var(--neon-blue), var(--nebula-purple));
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    text-shadow: 0 0 20px rgba(0, 243, 255, 0.4);
    letter-spacing: -1px;
  }
  .planet {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 60vw;
    height: 60vw;
    max-width: 800px;
    max-height: 800px;
    border-radius: 50%;
    background: radial-gradient(circle at 30% 30%, #9d4edd, #3c096c 40%, #050510 80%);
    box-shadow: 0 0 120px rgba(157, 78, 221, 0.4), inset -50px -50px 100px rgba(0,0,0,0.8);
    filter: blur(2px);
    z-index: 0;
    opacity: 0.7;
    animation: rotatePlanet 100s linear infinite;
  }
  @keyframes rotatePlanet {
    0% { transform: translate(-50%, -50%) rotate(0deg); }
    100% { transform: translate(-50%, -50%) rotate(360deg); }
  }
  .content {
    z-index: 2;
  }
  h2 {
    font-size: 1.5rem;
    color: var(--text-muted);
    font-weight: 400;
    margin-bottom: 3rem;
  }
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
    animation: bounce 2s infinite;
  }
  .scroll-indicator p {
    font-family: 'Space Mono', monospace;
    font-size: 0.9rem;
    color: var(--neon-blue);
    text-transform: uppercase;
    letter-spacing: 2px;
  }
  .mouse {
    width: 30px;
    height: 50px;
    border: 2px solid var(--neon-blue);
    border-radius: 15px;
    position: relative;
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
    animation: scroll 1.5s infinite;
  }
  @keyframes scroll {
    0% { top: 10px; opacity: 1; }
    100% { top: 30px; opacity: 0; }
  }
  @keyframes bounce {
    0%, 20%, 50%, 80%, 100% { transform: translate(-50%, 0); }
    40% { transform: translate(-50%, -10px); }
    60% { transform: translate(-50%, -5px); }
  }
  
  @media (max-width: 768px) {
    h1 {
      font-size: 2.5rem;
    }
    h2 {
      font-size: 1.2rem;
    }
  }
</style>
