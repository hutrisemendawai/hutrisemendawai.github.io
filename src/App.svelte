<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import "./app.css";

  import Navbar from "./lib/Navbar.svelte";
  import Hero from "./lib/Hero.svelte";
  import About from "./lib/About.svelte";
  import Experience from "./lib/Experience.svelte";
  import Projects from "./lib/Projects.svelte";
  import Footer from "./lib/Footer.svelte";

  gsap.registerPlugin(ScrollTrigger);

  let cursorRef;
  let scrollProgress;

  onMount(() => {
    // ===== CUSTOM CURSOR FOLLOWER =====
    const moveCursorX = gsap.quickTo(cursorRef, "left", { duration: 0.1, ease: "power3" });
    const moveCursorY = gsap.quickTo(cursorRef, "top", { duration: 0.1, ease: "power3" });

    window.addEventListener("mousemove", (e) => {
      moveCursorX(e.clientX);
      moveCursorY(e.clientY);
    });

    // Custom cursor hover states
    const interactiveElements = document.querySelectorAll('a, button, .interactive');
    interactiveElements.forEach(el => {
      el.addEventListener('mouseenter', () => cursorRef.classList.add('hover'));
      el.addEventListener('mouseleave', () => cursorRef.classList.remove('hover'));
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
<div class="custom-cursor" bind:this={cursorRef}></div>

<Navbar />

<main>
  <Hero />
  <About />
  <Experience />
  <Projects />
  <Footer />
</main>
