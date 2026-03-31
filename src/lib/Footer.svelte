<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import rocketIcon from '../assets/rocketicon.png';

  gsap.registerPlugin(ScrollTrigger);

  let footerContainer;
  let brandRef;
  let linksRef;
  let warpContainer;
  let rocketBtn;

  function scrollToTop() {
    // Rocket launch animation then scroll with View Transition
    gsap.to(rocketBtn, {
      y: -20,
      scale: 1.2,
      duration: 0.3,
      ease: "power2.out",
      onComplete: () => {
        gsap.to(rocketBtn, {
          y: -100,
          opacity: 0,
          duration: 0.5,
          ease: "power2.in",
          onComplete: () => {
            // Use View Transitions API for smooth scroll-to-top
            if (document.startViewTransition) {
              document.startViewTransition(() => {
                window.scrollTo({ top: 0, behavior: "smooth" });
              });
            } else {
              window.scrollTo({ top: 0, behavior: "smooth" });
            }
            // Reset rocket after scroll
            setTimeout(() => {
              gsap.set(rocketBtn, { y: 0, opacity: 1, scale: 1 });
            }, 1000);
          },
        });
      },
    });
  }

  onMount(() => {
    // ===== WARP SPEED LINES =====
    for (let i = 0; i < 15; i++) {
      const line = document.createElement("div");
      line.classList.add("warp-line");
      line.style.top = `${10 + Math.random() * 80}%`;
      line.style.height = `${1 + Math.random() * 2}px`;
      warpContainer.appendChild(line);
    }

    const warpLines = warpContainer.querySelectorAll(".warp-line");

    gsap.fromTo(
      warpLines,
      {
        left: "-20%",
        width: "0%",
        opacity: 0,
      },
      {
        left: "120%",
        width: () => `${20 + Math.random() * 40}%`,
        opacity: () => 0.3 + Math.random() * 0.4,
        duration: () => 0.5 + Math.random() * 0.5,
        stagger: 0.04,
        ease: "power2.in",
        scrollTrigger: {
          trigger: footerContainer,
          start: "top 90%",
          toggleActions: "play none none reverse",
        },
      },
    );

    // ===== FOOTER CONTENT REVEAL =====
    gsap.from(brandRef, {
      scrollTrigger: {
        trigger: footerContainer,
        start: "top 85%",
        toggleActions: "play none none reverse",
      },
      y: 40,
      opacity: 0,
      scale: 0.8,
      duration: 1,
      ease: "back.out(1.7)",
      delay: 0.3,
    });

    // ===== BRAND HEARTBEAT GLOW =====
    gsap.to(brandRef, {
      textShadow:
        "0 0 20px rgba(0, 243, 255, 0.6), 0 0 40px rgba(0, 243, 255, 0.3)",
      duration: 1.5,
      repeat: -1,
      yoyo: true,
      ease: "sine.inOut",
    });

    // ===== SOCIAL LINKS STAGGER =====
    const linkElements = linksRef.querySelectorAll("a");
    gsap.fromTo(
      linkElements,
      {
        y: 30,
        opacity: 0,
      },
      {
        scrollTrigger: {
          trigger: footerContainer,
          start: "top 85%",
          toggleActions: "play none none none",
        },
        y: 0,
        opacity: 1,
        stagger: 0.15,
        duration: 0.8,
        ease: "back.out(1.5)",
        delay: 0.5,
      },
    );

    // ===== LINK HOVER GLOW =====
    linkElements.forEach((link) => {
      link.addEventListener("mouseenter", () => {
        gsap.to(link, {
          scale: 1.1,
          y: -3,
          duration: 0.3,
          ease: "power2.out",
        });
      });
      link.addEventListener("mouseleave", () => {
        gsap.to(link, {
          scale: 1,
          y: 0,
          duration: 0.3,
          ease: "power2.out",
        });
      });
    });

    // ===== ROCKET BUTTON REVEAL =====
    gsap.from(rocketBtn, {
      scrollTrigger: {
        trigger: footerContainer,
        start: "top 80%",
        toggleActions: "play none none reverse",
      },
      scale: 0,
      rotation: -180,
      duration: 0.8,
      ease: "back.out(2)",
      delay: 0.7,
    });
  });
</script>

<footer bind:this={footerContainer} class="site-footer">
  <div class="warp-container" bind:this={warpContainer}></div>

  <div class="footer-content">
    <div class="brand" bind:this={brandRef}>
      <img src={rocketIcon} alt="rocket" class="icon" width="32" height="32" loading="lazy" decoding="async" />
      <h3>Hutri-SpacePlanet</h3>
    </div>

    <div class="links" bind:this={linksRef}>
      <a
        href="https://www.linkedin.com/in/hutrisemendawai/"
        target="_blank"
        rel="noopener noreferrer"
        class="social-link"
      >
        <span class="link-icon">💼</span>
        <span class="link-text">LinkedIn</span>
        <span class="link-glow"></span>
      </a>
      <a
        href="https://github.com/hutrisemendawai"
        target="_blank"
        rel="noopener noreferrer"
        class="social-link"
      >
        <span class="link-icon">💻</span>
        <span class="link-text">GitHub</span>
        <span class="link-glow"></span>
      </a>
      <a href="mailto:hutrisemendawai.gg@gmail.com" class="social-link">
        <span class="link-icon">✉️</span>
        <span class="link-text">Email</span>
        <span class="link-glow"></span>
      </a>
    </div>

    <button class="rocket-btn" bind:this={rocketBtn} on:click={scrollToTop}>
      <img src={rocketIcon} alt="rocket" class="rocket-icon" width="28" height="28" loading="lazy" decoding="async" />
      <span class="rocket-text">Back to Launch</span>
      <span class="rocket-trail"></span>
    </button>

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
    background: linear-gradient(to top, rgba(5, 5, 16, 0.95), transparent);
    overflow: hidden;
  }

  .warp-container {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 0;
    overflow: hidden;
  }

  .footer-content {
    max-width: 1200px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    gap: 2.5rem;
    position: relative;
    z-index: 1;
  }

  .brand {
    display: flex;
    align-items: center;
    gap: 15px;
    will-change: text-shadow;
  }

  .brand .icon {
    display: block;
    object-fit: contain;
    flex-shrink: 0;
    animation: float 3s ease-in-out infinite;
  }

  .brand h3 {
    font-family: "Space Mono", monospace;
    font-size: 1.8rem;
    background: linear-gradient(
      90deg,
      var(--starlight),
      var(--neon-blue),
      var(--nebula-purple)
    );
    background-size: 200% auto;
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    animation: auroraShift 4s ease infinite;
  }

  .links {
    display: flex;
    gap: 1.5rem;
    flex-wrap: wrap;
    justify-content: center;
  }

  /* ===== SOCIAL LINKS (refined) ===== */
  .social-link {
    display: flex;
    align-items: center;
    gap: 8px;
    font-family: "Space Mono", monospace;
    font-size: 0.88rem;
    color: var(--text-muted);
    text-transform: uppercase;
    letter-spacing: 1px;
    padding: 12px 24px;
    border: 1px solid var(--glass-border);
    border-radius: 50px;
    background: rgba(255, 255, 255, 0.02);
    position: relative;
    overflow: hidden;
    text-decoration: none;
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    will-change: transform;
  }

  .social-link:hover {
    color: var(--neon-blue);
    border-color: rgba(0, 212, 255, 0.3);
    background: rgba(0, 212, 255, 0.05);
    text-shadow: none;
    transform: translateY(-2px);
  }

  .link-glow {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 120%;
    height: 120%;
    transform: translate(-50%, -50%);
    background: radial-gradient(
      circle,
      rgba(0, 243, 255, 0.08) 0%,
      transparent 60%
    );
    opacity: 0;
    transition: opacity 0.3s ease;
    pointer-events: none;
  }

  .social-link:hover .link-glow {
    opacity: 1;
  }

  .link-icon {
    font-size: 1.2rem;
    transition: transform 0.3s ease;
  }

  .social-link:hover .link-icon {
    transform: scale(1.2) rotate(-5deg);
  }

  /* ===== ROCKET BUTTON ===== */
  .rocket-btn {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px 24px;
    background: transparent;
    border: 1px solid rgba(139, 92, 246, 0.3);
    border-radius: 50px;
    color: var(--starlight);
    font-family: "Space Mono", monospace;
    font-size: 0.85rem;
    cursor: pointer;
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    position: relative;
    overflow: hidden;
    text-transform: uppercase;
    letter-spacing: 1px;
    will-change: transform;
  }

  .rocket-btn::before {
    content: "";
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 0%;
    background: linear-gradient(to top, rgba(157, 78, 221, 0.2), transparent);
    transition: height 0.4s ease;
    z-index: -1;
  }

  .rocket-btn:hover::before {
    height: 100%;
  }

  .rocket-btn:hover {
    border-color: rgba(0, 212, 255, 0.4);
    box-shadow:
      0 0 15px rgba(139, 92, 246, 0.15);
    transform: translateY(-3px);
  }

  .rocket-icon {
    display: block;
    object-fit: contain;
    flex-shrink: 0;
    transition: transform 0.3s ease;
  }

  .rocket-btn:hover .rocket-icon {
    transform: translateY(-3px) rotate(-15deg);
  }

  .rocket-trail {
    position: absolute;
    bottom: -5px;
    left: 50%;
    transform: translateX(-50%);
    width: 30%;
    height: 8px;
    background: linear-gradient(to top, rgba(255, 107, 53, 0.4), transparent);
    border-radius: 50%;
    filter: blur(3px);
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  .rocket-btn:hover .rocket-trail {
    opacity: 1;
  }

  .copyright p {
    font-size: 0.9rem;
    color: var(--text-muted);
    opacity: 0.7;
  }

  @media (max-width: 768px) {
    .links {
      gap: 1rem;
    }
    .social-link {
      padding: 10px 18px;
      font-size: 0.85rem;
    }
  }

  /* ===== Scroll-Driven: Warp lines grow on scroll ===== */
  @supports (animation-timeline: scroll()) {
    .warp-container {
      animation: sda-warp-reveal linear both;
      animation-timeline: view();
      animation-range: entry 30% entry 100%;
    }

    @keyframes sda-warp-reveal {
      from { opacity: 0; }
      to { opacity: 1; }
    }

    .brand {
      animation: sda-brand-enter linear both;
      animation-timeline: view();
      animation-range: entry 20% entry 70%;
    }

    @keyframes sda-brand-enter {
      from {
        opacity: 0;
        transform: translateY(30px) scale(0.9);
      }
      to {
        opacity: 1;
        transform: translateY(0) scale(1);
      }
    }
  }
</style>
