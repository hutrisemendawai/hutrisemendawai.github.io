<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  
  let navRef;
  let menuOpen = false;

  function toggleMenu() {
    menuOpen = !menuOpen;
  }

  function closeMenu() {
    menuOpen = false;
  }
  
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
    <div class="nav-links" class:open={menuOpen}>
      <a href="#about" class="interactive" on:click={closeMenu}>Capabilities</a>
      <a href="#services" class="interactive" on:click={closeMenu}>Services</a>
      <a href="#projects" class="interactive" on:click={closeMenu}>Work</a>
      <a href="#contact" class="interactive btn-nav" on:click={closeMenu}>Let's Talk</a>
    </div>
    <button class="hamburger" class:open={menuOpen} on:click={toggleMenu} aria-label="Toggle menu" aria-expanded={menuOpen ? 'true' : 'false'}>
      <span></span>
      <span></span>
      <span></span>
    </button>
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

  .hamburger {
    display: none;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 5px;
    background: none;
    border: none;
    cursor: pointer;
    padding: 4px;
    z-index: 200;
  }

  .hamburger span {
    display: block;
    width: 24px;
    height: 2px;
    background: #fff;
    border-radius: 2px;
    transition: transform 0.3s ease, opacity 0.3s ease;
    transform-origin: center;
  }

  .hamburger.open span:nth-child(1) {
    transform: translateY(7px) rotate(45deg);
  }
  .hamburger.open span:nth-child(2) {
    opacity: 0;
    transform: scaleX(0);
  }
  .hamburger.open span:nth-child(3) {
    transform: translateY(-7px) rotate(-45deg);
  }

  @media (max-width: 768px) {
    nav {
      padding: 1.25rem 1.5rem;
      mix-blend-mode: normal;
      background: rgba(10, 10, 10, 0.9);
      backdrop-filter: blur(12px);
      -webkit-backdrop-filter: blur(12px);
    }

    .hamburger {
      display: flex;
    }

    .nav-links {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100vh;
      background: rgba(10, 10, 10, 0.97);
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 2.5rem;
      z-index: 150;
    }

    .nav-links.open {
      display: flex;
    }

    .nav-links a {
      font-size: 1.5rem;
      color: rgba(255,255,255,0.9);
    }

    .btn-nav {
      padding: 0.75rem 2rem;
      font-size: 1rem;
    }
  }
</style>
