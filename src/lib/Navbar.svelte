<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  
  let navRef;
  
  onMount(() => {
    if (!navRef) {
      return;
    }

    // Reveal nav after a slight delay
    const navTween = gsap.fromTo(navRef, 
      { y: -50, opacity: 0 },
      { y: 0, opacity: 1, duration: 1, ease: "power4.out", delay: 0.5 }
    );

    return () => {
      navTween.kill();
    };
  });
</script>

<nav bind:this={navRef}>
  <div class="nav-container container">
    <a href="/" class="brand interactive">AETHER</a>
    <div class="nav-links">
      <a href="#about" class="interactive">Capabilities</a>
      <a href="#services" class="interactive">Services</a>
      <a href="#projects" class="interactive">Work</a>
      <a href="#contact" class="interactive btn-nav">Let's Talk</a>
    </div>
  </div>
</nav>

<style>
  nav {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 100;
    padding: 2rem;
    mix-blend-mode: difference;
  }

  .nav-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .brand {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 1.5rem;
    color: #fff;
    letter-spacing: -0.05em;
  }

  .nav-links {
    display: flex;
    gap: 3rem;
    align-items: center;
  }

  .nav-links a {
    font-size: 0.85rem;
    text-transform: uppercase;
    font-weight: 600;
    letter-spacing: 1px;
    color: rgba(255, 255, 255, 0.7);
    transition: color 0.3s ease;
  }

  .nav-links a:hover {
    color: #fff;
  }

  .btn-nav {
    border: 1px solid rgba(255,255,255,0.3);
    padding: 0.5rem 1.5rem;
    border-radius: 30px;
  }
  .btn-nav:hover {
    background: #fff;
    color: #000 !important;
  }
</style>
