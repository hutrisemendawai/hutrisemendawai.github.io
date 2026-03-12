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
  import ParticleBackground from "./lib/ParticleBackground.svelte";

  gsap.registerPlugin(ScrollTrigger);

  let cursorGlow;
  let scrollProgress;

  onMount(() => {
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
  });
</script>

<div class="scroll-progress" bind:this={scrollProgress}></div>
<div class="cursor-glow" bind:this={cursorGlow}></div>
<ParticleBackground />

<main>
  <Hero />
  <About />
  <Experience />
  <Education />
  <Projects />
  <Footer />
</main>
