<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import "./app.css";

  import Hero from "./lib/Hero.svelte";
  import About from "./lib/About.svelte";
  import Experience from "./lib/Experience.svelte";
  import Education from "./lib/Education.svelte";
  import Projects from "./lib/Projects.svelte";
  import Footer from "./lib/Footer.svelte";

  gsap.registerPlugin(ScrollTrigger);

  let starContainer;
  let cursorGlow;
  let scrollProgress;

  onMount(() => {
    // ===== STARFIELD WITH PARALLAX LAYERS =====
    const layers = [
      { count: 80, sizeRange: [1, 2], parallaxSpeed: 0.3, className: 'star-far' },
      { count: 50, sizeRange: [2, 3], parallaxSpeed: 0.6, className: 'star-mid' },
      { count: 30, sizeRange: [3, 5], parallaxSpeed: 1.0, className: 'star-near' },
    ];

    layers.forEach(layer => {
      for (let i = 0; i < layer.count; i++) {
        const star = document.createElement("div");
        star.classList.add("star");
        const size = Math.random() * (layer.sizeRange[1] - layer.sizeRange[0]) + layer.sizeRange[0];
        star.style.width = `${size}px`;
        star.style.height = `${size}px`;
        star.style.top = `${Math.random() * 100}vh`;
        star.style.left = `${Math.random() * 100}vw`;
        star.style.animationDuration = `${Math.random() * 3 + 2}s`;
        star.style.animationDelay = `${Math.random() * 2}s`;
        star.dataset.parallax = String(layer.parallaxSpeed);
        starContainer.appendChild(star);
      }
    });

    // ===== PARALLAX STARS ON SCROLL =====
    const allStars = starContainer.querySelectorAll('.star');
    gsap.to(allStars, {
      scrollTrigger: {
        trigger: document.body,
        start: "top top",
        end: "bottom bottom",
        scrub: 0.5,
      },
      y: (i, el) => {
        const speed = parseFloat(el.dataset.parallax) || 0.5;
        return -speed * 300;
      },
      ease: "none",
    });

    // ===== CURSOR GLOW FOLLOWER =====
    const moveGlow = gsap.quickTo(cursorGlow, "left", { duration: 0.3, ease: "power2.out" });
    const moveGlowY = gsap.quickTo(cursorGlow, "top", { duration: 0.3, ease: "power2.out" });

    window.addEventListener("mousemove", (e) => {
      moveGlow(e.clientX);
      moveGlowY(e.clientY);
    });

    // ===== SCROLL PROGRESS BAR =====
    ScrollTrigger.create({
      trigger: document.body,
      start: "top top",
      end: "bottom bottom",
      onUpdate: (self) => {
        gsap.set(scrollProgress, { width: `${self.progress * 100}%` });
      },
    });

    // ===== SHOOTING STARS =====
    function createShootingStar() {
      const star = document.createElement("div");
      star.classList.add("shooting-star");
      starContainer.appendChild(star);

      const startX = Math.random() * window.innerWidth;
      const startY = Math.random() * window.innerHeight * 0.5;
      const angle = 15 + Math.random() * 30; // degrees
      const distance = 300 + Math.random() * 400;

      gsap.set(star, {
        x: startX,
        y: startY,
        rotation: angle,
        width: 60 + Math.random() * 80,
      });

      gsap.timeline()
        .to(star, {
          opacity: 1,
          duration: 0.1,
        })
        .to(star, {
          x: startX + Math.cos(angle * Math.PI / 180) * distance,
          y: startY + Math.sin(angle * Math.PI / 180) * distance,
          opacity: 0,
          duration: 0.6 + Math.random() * 0.4,
          ease: "power1.in",
          onComplete: () => {
            star.remove();
          },
        });
    }

    // Periodically fire shooting stars
    function scheduleShootingStar() {
      const delay = 2000 + Math.random() * 5000;
      setTimeout(() => {
        createShootingStar();
        scheduleShootingStar();
      }, delay);
    }
    scheduleShootingStar();

    // ===== NEBULA AMBIENT GLOW PARTICLES =====
    for (let i = 0; i < 5; i++) {
      const nebula = document.createElement("div");
      nebula.style.position = "fixed";
      nebula.style.width = `${200 + Math.random() * 300}px`;
      nebula.style.height = nebula.style.width;
      nebula.style.borderRadius = "50%";
      nebula.style.background = `radial-gradient(circle, ${
        ['rgba(157,78,221,0.04)', 'rgba(0,243,255,0.03)', 'rgba(0,255,136,0.03)'][Math.floor(Math.random() * 3)]
      } 0%, transparent 70%)`;
      nebula.style.top = `${Math.random() * 100}vh`;
      nebula.style.left = `${Math.random() * 100}vw`;
      nebula.style.pointerEvents = "none";
      nebula.style.zIndex = "-1";
      starContainer.appendChild(nebula);

      gsap.to(nebula, {
        x: () => (Math.random() - 0.5) * 100,
        y: () => (Math.random() - 0.5) * 100,
        duration: 8 + Math.random() * 10,
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut",
      });
    }
  });
</script>

<div class="scroll-progress" bind:this={scrollProgress}></div>
<div class="cursor-glow" bind:this={cursorGlow}></div>
<div class="starfield" bind:this={starContainer}></div>

<main>
  <Hero />
  <About />
  <Experience />
  <Education />
  <Projects />
  <Footer />
</main>
