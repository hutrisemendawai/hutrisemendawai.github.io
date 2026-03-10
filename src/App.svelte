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

  onMount(() => {
    // Generate starfield
    const numStars = 150;
    for (let i = 0; i < numStars; i++) {
      const star = document.createElement("div");
      star.classList.add("star");
      star.style.width = `${Math.random() * 3}px`;
      star.style.height = star.style.width;
      star.style.top = `${Math.random() * 100}vh`;
      star.style.left = `${Math.random() * 100}vw`;
      star.style.animationDuration = `${Math.random() * 3 + 2}s`;
      star.style.animationDelay = `${Math.random() * 2}s`;
      starContainer.appendChild(star);
    }
  });
</script>

<div class="starfield" bind:this={starContainer}></div>

<main>
  <Hero />
  <About />
  <Experience />
  <Education />
  <Projects />
  <Footer />
</main>
