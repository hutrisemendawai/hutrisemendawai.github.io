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
  import GithubRepos from "./lib/GithubRepos.svelte";
  import Footer from "./lib/Footer.svelte";

  gsap.registerPlugin(ScrollTrigger);

  let cursorRef;
  let scrollProgress;

  onMount(() => {
    if (!cursorRef || !scrollProgress) {
      return;
    }

    const isTouchDevice = window.matchMedia("(pointer: coarse)").matches;

    let cleanupCursor = () => {};

    if (!isTouchDevice) {
      const moveCursorX = gsap.quickTo(cursorRef, "left", { duration: 0.1, ease: "power3" });
      const moveCursorY = gsap.quickTo(cursorRef, "top", { duration: 0.1, ease: "power3" });

      const handleMouseMove = (event) => {
        moveCursorX(event.clientX);
        moveCursorY(event.clientY);
      };

      const isInteractiveTarget = (target) =>
        target instanceof Element && Boolean(target.closest("a, button, .interactive"));

      const handleMouseOver = (event) => {
        if (isInteractiveTarget(event.target)) {
          cursorRef.classList.add("hover");
        }
      };

      const handleMouseOut = (event) => {
        if (!isInteractiveTarget(event.target)) {
          return;
        }

        if (isInteractiveTarget(event.relatedTarget)) {
          return;
        }

        cursorRef.classList.remove("hover");
      };

      window.addEventListener("mousemove", handleMouseMove);
      document.addEventListener("mouseover", handleMouseOver);
      document.addEventListener("mouseout", handleMouseOut);

      cleanupCursor = () => {
        window.removeEventListener("mousemove", handleMouseMove);
        document.removeEventListener("mouseover", handleMouseOver);
        document.removeEventListener("mouseout", handleMouseOut);
        gsap.killTweensOf(cursorRef);
        cursorRef.classList.remove("hover");
      };
    }

    const progressTrigger = ScrollTrigger.create({
      trigger: document.body,
      start: "top top",
      end: "bottom bottom",
      onUpdate: (self) => {
        gsap.set(scrollProgress, { width: `${self.progress * 100}%` });
      },
    });

    return () => {
      cleanupCursor();
      progressTrigger.kill();
    };
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
  <GithubRepos />
  <Footer />
</main>
