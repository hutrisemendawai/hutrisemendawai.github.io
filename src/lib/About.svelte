<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  let aboutRef;
  let marqueeContentRef1;
  let marqueeContentRef2;
  let textRefs = [];

  onMount(() => {
    if (!aboutRef) {
      return;
    }

    gsap.registerPlugin(ScrollTrigger);

    const ctx = gsap.context(() => {
      if (marqueeContentRef1) {
        gsap.to(marqueeContentRef1, {
          xPercent: -100,
          ease: "none",
          duration: 15,
          repeat: -1
        });
      }

      if (marqueeContentRef2) {
        gsap.to(marqueeContentRef2, {
          xPercent: -100,
          ease: "none",
          duration: 15,
          repeat: -1
        });
      }

      textRefs.filter(Boolean).forEach((el) => {
        gsap.fromTo(el, 
          { opacity: 0, y: 50 },
          { 
            opacity: 1, 
            y: 0, 
            duration: 1, 
            ease: "power3.out",
            scrollTrigger: {
              trigger: el,
              start: "top 85%",
            }
          }
        );
      });
    }, aboutRef);

    return () => {
      ctx.revert();
    };
  });
</script>

<section id="about" class="about" bind:this={aboutRef}>
  <div class="marquee-wrapper">
    <div class="marquee">
      <div class="marquee-content" bind:this={marqueeContentRef1}>
        <span>AI-EMPOWERED • NEXT-GEN DELIVERY •</span>
        <span>AI-EMPOWERED • NEXT-GEN DELIVERY •</span>
        <span>AI-EMPOWERED • NEXT-GEN DELIVERY •</span>
      </div>
      <!-- Duplicate for seamless loop -->
      <div class="marquee-content" bind:this={marqueeContentRef2}>
        <span>AI-EMPOWERED • NEXT-GEN DELIVERY •</span>
        <span>AI-EMPOWERED • NEXT-GEN DELIVERY •</span>
        <span>AI-EMPOWERED • NEXT-GEN DELIVERY •</span>
      </div>
    </div>
  </div>

  <div class="container about-content">
    <div class="grid">
      <div class="column left">
        <h2 class="section-title" bind:this={textRefs[0]}>COLLECTIVE<br>INTELLIGENCE.</h2>
      </div>
      <div class="column right">
        <!-- Using block-level elements without putting div inside p -->
        <div class="body-text" bind:this={textRefs[1]}>
          I don’t just write code. I augment development with state-of-the-art AI.
        </div>
        <div class="tooling" bind:this={textRefs[2]}>
          <div class="tool-row">
            <span class="tool-name">ANTIGRAVITY</span>
            <span class="tool-desc">Agentic Orchestration</span>
          </div>
          <div class="tool-row">
            <span class="tool-name">COPILOT PRO</span>
            <span class="tool-desc">Contextual Intelligence</span>
          </div>
          <div class="tool-row">
            <span class="tool-name">OLLAMA QWEN</span>
            <span class="tool-desc">Locally Hosted Edge AI</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<style>
  .about {
    background-color: var(--monopo-black);
    padding: 0;
    min-height: 100vh;
  }

  .marquee-wrapper {
    width: 100%;
    overflow: hidden;
    padding: 2rem 0;
    border-top: 1px solid var(--border-light);
    border-bottom: 1px solid var(--border-light);
    background: var(--monopo-dark);
  }

  .marquee {
    display: flex;
    white-space: nowrap;
  }

  .marquee-content {
    display: flex;
    flex-shrink: 0;
  }

  .marquee span {
    font-family: 'Syne', sans-serif;
    font-size: clamp(3rem, 8vw, 8rem);
    font-weight: 800;
    color: transparent;
    -webkit-text-stroke: 1px var(--text-main);
    padding: 0 2rem;
  }

  .about-content {
    padding-top: 8rem;
    padding-bottom: 8rem;
  }

  .grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 4rem;
  }

  .section-title {
    font-size: clamp(2rem, 5vw, 5rem);
    line-height: 1;
    color: var(--text-main);
  }

  .body-text {
    font-size: clamp(1.2rem, 2vw, 2rem);
    line-height: 1.4;
    font-weight: 400;
    margin-bottom: 4rem;
    color: var(--text-main);
  }

  .tool-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 2rem;
    padding: 1.5rem 0;
    border-bottom: 1px solid var(--border-light);
    transition: all 0.3s ease;
  }

  .tool-row:hover {
    background: rgba(255,255,255,0.05);
    padding-left: 1rem;
    padding-right: 1rem;
  }

  .tool-name {
    flex-shrink: 0;
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 1.5rem;
  }

  .tool-desc {
    text-align: right;
    color: var(--text-muted);
    font-family: 'Inter', sans-serif;
    font-size: 0.9rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    max-width: 250px;
  }

  @media (max-width: 900px) {
    .grid {
      grid-template-columns: 1fr;
    }
  }
</style>
