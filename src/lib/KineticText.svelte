<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  gsap.registerPlugin(ScrollTrigger);

  /** @type {string} */
  export let text = "Explore The Universe";
  /** @type {string} */
  export let variant = "wave"; // "wave" | "path" | "scatter"
  /** @type {string} */
  export let motionPath = "M 0,30 Q 150,-20 300,30 T 600,30";

  let containerEl;
  let chars = [];

  $: chars = text.split("").map((char, i) => ({
    char: char === " " ? "\u00A0" : char,
    isSpace: char === " ",
    index: i,
  }));

  onMount(() => {
    if (!containerEl) return;
    const charEls = containerEl.querySelectorAll(".kinetic-char");
    if (charEls.length === 0) return;

    if (variant === "wave") {
      // Wave animation - characters undulate like a wave on scroll
      gsap.fromTo(
        charEls,
        {
          y: 60,
          opacity: 0,
          rotateX: -90,
          scale: 0.5,
        },
        {
          y: 0,
          opacity: 1,
          rotateX: 0,
          scale: 1,
          stagger: {
            each: 0.03,
            from: "start",
          },
          duration: 0.8,
          ease: "back.out(1.7)",
          scrollTrigger: {
            trigger: containerEl,
            start: "top 85%",
            toggleActions: "play none none reverse",
          },
        },
      );

      // Continuous wave after reveal
      charEls.forEach((char, i) => {
        gsap.to(char, {
          y: -8,
          duration: 0.6,
          repeat: -1,
          yoyo: true,
          ease: "sine.inOut",
          delay: i * 0.05,
        });
      });
    } else if (variant === "path") {
      // Path animation using CSS offset-path
      charEls.forEach((char, i) => {
        const offsetStart = (i / charEls.length) * 100;
        char.style.offsetPath = `path('${motionPath}')`;
        char.style.offsetRotate = "0deg";
        char.style.offsetDistance = `${offsetStart}%`;
      });

      gsap.fromTo(
        charEls,
        { opacity: 0, scale: 0 },
        {
          opacity: 1,
          scale: 1,
          stagger: 0.04,
          duration: 0.6,
          ease: "elastic.out(1, 0.5)",
          scrollTrigger: {
            trigger: containerEl,
            start: "top 85%",
            toggleActions: "play none none reverse",
          },
        },
      );
    } else if (variant === "scatter") {
      // Scatter - characters fly in from random positions
      charEls.forEach((char) => {
        gsap.set(char, {
          x: (Math.random() - 0.5) * 200,
          y: (Math.random() - 0.5) * 200,
          rotation: (Math.random() - 0.5) * 180,
          scale: 0,
          opacity: 0,
        });
      });

      gsap.to(charEls, {
        x: 0,
        y: 0,
        rotation: 0,
        scale: 1,
        opacity: 1,
        stagger: 0.02,
        duration: 1,
        ease: "elastic.out(1, 0.5)",
        scrollTrigger: {
          trigger: containerEl,
          start: "top 85%",
          toggleActions: "play none none reverse",
        },
      });
    }
  });
</script>

<div class="kinetic-text-container {variant}" bind:this={containerEl}>
  {#each chars as { char, isSpace, index } (index)}
    <span
      class="kinetic-char"
      class:space={isSpace}
      style="--char-index: {index};"
    >
      {char}
    </span>
  {/each}
</div>

<style>
  .kinetic-text-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    perspective: 800px;
    overflow: visible;
    position: relative;
  }

  .kinetic-char {
    display: inline-block;
    font-family: 'Space Mono', monospace;
    font-size: 2.5rem;
    font-weight: 700;
    color: var(--starlight, #ffffff);
    text-shadow:
      0 0 10px rgba(0, 243, 255, 0.4),
      0 0 30px rgba(0, 243, 255, 0.15);
    will-change: transform, opacity;
    transform-style: preserve-3d;
    cursor: default;
    transition: color 0.3s ease, text-shadow 0.3s ease;
    line-height: 1.2;
  }

  .kinetic-char:hover {
    color: var(--neon-blue, #00f3ff);
    text-shadow:
      0 0 20px rgba(0, 243, 255, 0.8),
      0 0 40px rgba(0, 243, 255, 0.4),
      0 0 60px rgba(0, 243, 255, 0.2);
  }

  .kinetic-char.space {
    width: 0.5em;
  }

  /* Path variant uses offset-path for motion path */
  .path .kinetic-char {
    position: relative;
  }

  @media (max-width: 768px) {
    .kinetic-char {
      font-size: 1.5rem;
    }
  }
</style>
