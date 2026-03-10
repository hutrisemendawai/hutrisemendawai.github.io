<script>
  import { onMount, tick } from 'svelte';
  import gsap from 'gsap';
  import { ScrollTrigger } from 'gsap/ScrollTrigger';

  gsap.registerPlugin(ScrollTrigger);

  let footerContainer;

  onMount(() => {
    gsap.from(footerContainer.children, {
      scrollTrigger: {
        trigger: footerContainer,
        start: "top 90%",
        toggleActions: "play none none reverse"
      },
      y: 30,
      opacity: 0,
      duration: 1,
      stagger: 0.2,
      ease: "power2.out"
    });
  });
</script>

<footer bind:this={footerContainer} class="site-footer">
  <div class="footer-content">
    <div class="brand">
      <span class="icon">🚀</span>
      <h3>Hutri-SpacePlanet</h3>
    </div>
    <div class="links">
      <a href="https://linkedin.com/in/ahmadhutrisemendawai" target="_blank" rel="noopener noreferrer">LinkedIn</a>
      <a href="https://github.com/ahmadhutrisemendawai" target="_blank" rel="noopener noreferrer">GitHub</a>
      <a href="mailto:contact@example.com">Email</a>
    </div>
    <div class="copyright">
      <p>&copy; 2026 Ahmad Hutri Semendawai. All systems operational.</p>
    </div>
  </div>
</footer>

<style>
  .site-footer {
    padding: 6rem 2rem 3rem;
    position: relative;
    border-top: 1px solid var(--glass-border);
    margin-top: 100px;
    background: linear-gradient(to top, var(--space-black), transparent);
  }

  .footer-content {
    max-width: 1200px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    gap: 2rem;
  }

  .brand {
    display: flex;
    align-items: center;
    gap: 15px;
  }

  .brand .icon {
    font-size: 2rem;
  }

  .brand h3 {
    font-family: 'Space Mono', monospace;
    font-size: 1.5rem;
    background: linear-gradient(90deg, var(--starlight), var(--neon-blue));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }

  .links {
    display: flex;
    gap: 2rem;
  }

  .links a {
    font-family: 'Space Mono', monospace;
    font-size: 1rem;
    color: var(--text-muted);
    text-transform: uppercase;
    letter-spacing: 1px;
    position: relative;
  }

  .links a::after {
    content: '';
    position: absolute;
    bottom: -5px;
    left: 0;
    width: 0;
    height: 2px;
    background: var(--neon-blue);
    transition: width 0.3s ease;
  }

  .links a:hover {
    color: var(--neon-blue);
  }

  .links a:hover::after {
    width: 100%;
  }

  .copyright p {
    font-size: 0.9rem;
    color: var(--text-muted);
    opacity: 0.7;
  }
</style>
