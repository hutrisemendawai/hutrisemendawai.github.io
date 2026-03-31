<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import rocketIcon from '../assets/rocketicon.png';

  let navRef;
  let scrolled = $state(false);
  let activeSection = $state("hero");
  let mobileOpen = $state(false);

  const navLinks = [
    { id: "hero", label: "Home" },
    { id: "about", label: "About" },
    { id: "experience", label: "Experience" },
    { id: "education", label: "Education" },
    { id: "projects", label: "Projects" },
  ];

  function scrollToSection(id) {
    const el = document.getElementById(id);
    if (!el) return;
    if (document.startViewTransition) {
      document.startViewTransition(() => {
        el.scrollIntoView({ behavior: "smooth" });
      });
    } else {
      el.scrollIntoView({ behavior: "smooth" });
    }
    mobileOpen = false;
  }

  onMount(() => {
    const onScroll = () => {
      scrolled = window.scrollY > 40;

      // Determine active section based on scroll
      const sections = navLinks.map(l => document.getElementById(l.id)).filter(Boolean);
      let current = "hero";
      for (const sec of sections) {
        const rect = sec.getBoundingClientRect();
        if (rect.top <= 200) {
          current = sec.id;
        }
      }
      activeSection = current;
    };

    window.addEventListener("scroll", onScroll, { passive: true });

    // Animate navbar entry
    gsap.from(navRef, {
      y: -80,
      opacity: 0,
      duration: 1,
      ease: "power3.out",
      delay: 1.2,
    });

    return () => window.removeEventListener("scroll", onScroll);
  });
</script>

<nav bind:this={navRef} class="navbar" class:scrolled>
  <div class="nav-inner">
    <button class="nav-brand" onclick={() => scrollToSection("hero")}>
      <img src={rocketIcon} alt="Logo" class="brand-icon" width="24" height="24" />
      <span class="brand-text">Hutri</span>
    </button>

    <button class="mobile-toggle" onclick={() => mobileOpen = !mobileOpen} aria-label="Toggle menu">
      <span class="bar" class:open={mobileOpen}></span>
      <span class="bar" class:open={mobileOpen}></span>
      <span class="bar" class:open={mobileOpen}></span>
    </button>

    <div class="nav-links" class:mobile-open={mobileOpen}>
      {#each navLinks as link}
        <button
          class="nav-link"
          class:active={activeSection === link.id}
          onclick={() => scrollToSection(link.id)}
        >
          {link.label}
          <span class="link-indicator"></span>
        </button>
      {/each}
      <button class="nav-cta" onclick={() => scrollToSection("about")}>
        Let's Connect
      </button>
    </div>
  </div>
</nav>

<style>
  .navbar {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 9990;
    padding: 1rem 2rem;
    transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  }

  .navbar.scrolled {
    padding: 0.6rem 2rem;
    background: rgba(5, 5, 16, 0.85);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    border-bottom: 1px solid rgba(255, 255, 255, 0.06);
    box-shadow: 0 4px 30px rgba(0, 0, 0, 0.4);
  }

  .nav-inner {
    max-width: 1200px;
    margin: 0 auto;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  /* Brand */
  .nav-brand {
    display: flex;
    align-items: center;
    gap: 10px;
    background: none;
    border: none;
    cursor: pointer;
    padding: 0;
  }

  .brand-icon {
    display: block;
    object-fit: contain;
    transition: transform 0.3s ease;
  }

  .nav-brand:hover .brand-icon {
    transform: rotate(-15deg) scale(1.1);
  }

  .brand-text {
    font-family: 'Space Mono', monospace;
    font-size: 1.3rem;
    font-weight: 700;
    background: linear-gradient(135deg, var(--starlight), var(--neon-blue));
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    letter-spacing: -0.5px;
  }

  /* Nav Links */
  .nav-links {
    display: flex;
    align-items: center;
    gap: 0.25rem;
  }

  .nav-link {
    position: relative;
    background: none;
    border: none;
    color: var(--text-muted);
    font-family: 'Inter', sans-serif;
    font-size: 0.88rem;
    font-weight: 500;
    padding: 8px 16px;
    cursor: pointer;
    transition: color 0.3s ease;
    letter-spacing: 0.3px;
  }

  .nav-link:hover {
    color: var(--starlight);
  }

  .nav-link.active {
    color: var(--neon-blue);
  }

  .link-indicator {
    position: absolute;
    bottom: 2px;
    left: 50%;
    transform: translateX(-50%) scaleX(0);
    width: 20px;
    height: 2px;
    background: var(--neon-blue);
    border-radius: 1px;
    transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    box-shadow: 0 0 8px rgba(0, 212, 255, 0.4);
  }

  .nav-link.active .link-indicator {
    transform: translateX(-50%) scaleX(1);
  }

  /* CTA */
  .nav-cta {
    margin-left: 1rem;
    padding: 8px 20px;
    font-family: 'Space Mono', monospace;
    font-size: 0.8rem;
    color: var(--neon-blue);
    background: rgba(0, 212, 255, 0.06);
    border: 1px solid rgba(0, 212, 255, 0.3);
    border-radius: 50px;
    cursor: pointer;
    transition: all 0.3s ease;
    letter-spacing: 0.5px;
    text-transform: uppercase;
  }

  .nav-cta:hover {
    background: rgba(0, 212, 255, 0.12);
    border-color: var(--neon-blue);
    box-shadow: 0 0 15px rgba(0, 212, 255, 0.2);
    transform: translateY(-1px);
  }

  /* Mobile toggle */
  .mobile-toggle {
    display: none;
    flex-direction: column;
    gap: 5px;
    background: none;
    border: none;
    cursor: pointer;
    padding: 4px;
  }

  .bar {
    width: 22px;
    height: 2px;
    background: var(--text-muted);
    border-radius: 2px;
    transition: all 0.3s ease;
  }

  .bar.open:nth-child(1) {
    transform: rotate(45deg) translate(5px, 5px);
  }
  .bar.open:nth-child(2) {
    opacity: 0;
  }
  .bar.open:nth-child(3) {
    transform: rotate(-45deg) translate(5px, -5px);
  }

  @media (max-width: 768px) {
    .mobile-toggle {
      display: flex;
    }

    .nav-links {
      position: fixed;
      top: 0;
      right: -100%;
      width: 280px;
      height: 100vh;
      flex-direction: column;
      align-items: flex-start;
      padding: 5rem 2rem 2rem;
      gap: 0.5rem;
      background: rgba(5, 5, 16, 0.97);
      backdrop-filter: blur(20px);
      -webkit-backdrop-filter: blur(20px);
      border-left: 1px solid rgba(255, 255, 255, 0.06);
      transition: right 0.4s cubic-bezier(0.4, 0, 0.2, 1);
      z-index: 9989;
    }

    .nav-links.mobile-open {
      right: 0;
    }

    .nav-link {
      font-size: 1rem;
      padding: 12px 8px;
      width: 100%;
      text-align: left;
    }

    .nav-cta {
      margin-left: 0;
      margin-top: 1rem;
    }
  }
</style>
