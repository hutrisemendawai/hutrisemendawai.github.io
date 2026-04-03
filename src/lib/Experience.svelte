<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  const services = [
    { title: "WEBSITES", desc: "Immersive landing pages, intricate web applications, and digital experiences." },
    { title: "DESKTOP APPS", desc: "Robust standalone applications built for top-tier performance on native operating systems." },
    { title: "MOBILE APPS", desc: "Cross-platform solutions capturing users on the go with fluid, native-feeling interactions." },
    { title: "UNIQUE REQ", desc: "Custom hardware integrations, bleeding-edge tech, or bizarrely brilliant unconventional ideas." }
  ];

  let listItems = [];
  let bgRef;

  onMount(() => {
    gsap.registerPlugin(ScrollTrigger);

    listItems.forEach((item, i) => {
      if (item) {
        gsap.fromTo(item,
          { opacity: 0, x: -50 },
          {
            opacity: 1, x: 0, duration: 0.8, ease: "power3.out",
            scrollTrigger: {
              trigger: item,
              start: "top 90%"
            }
          }
        );
      }
    });
  });

  function handleHover(index) {
    if(!bgRef) return;
    // Animate background color slightly based on the hovered service
    const colors = ["#1a0505", "#051a1a", "#05051a", "#1a1505"];
    gsap.to(bgRef, { backgroundColor: colors[index], duration: 0.5 });
  }

  function handleLeave() {
    if(!bgRef) return;
    gsap.to(bgRef, { backgroundColor: "var(--monopo-black)", duration: 0.5 });
  }
</script>

<section id="services" class="services" bind:this={bgRef}>
  <div class="container relative-content">
    <div class="header-area">
      <h2 class="section-title">I BUILD<br>IT ALL.</h2>
      <p class="subtitle">From standard to bespoke.</p>
    </div>

    <ul class="service-list">
      {#each services as svc, i}
        <li 
          bind:this={listItems[i]} 
          class="service-item interactive"
          on:mouseenter={() => handleHover(i)}
          on:mouseleave={handleLeave}
        >
          <div class="service-title">{svc.title}</div>
          <div class="service-desc">{svc.desc}</div>
        </li>
      {/each}
    </ul>
  </div>
</section>

<style>
  .services {
    background-color: var(--monopo-black);
    transition: background-color 0.5s ease;
    padding-top: 10rem;
    padding-bottom: 10rem;
  }

  .relative-content {
    position: relative;
    z-index: 2;
    display: grid;
    grid-template-columns: 1fr 2fr;
    gap: 4rem;
  }

  .header-area {
    position: sticky;
    top: 20vh;
    height: fit-content;
  }

  .section-title {
    font-size: clamp(3rem, 6vw, 6rem);
    color: var(--text-main);
    margin-bottom: 1rem;
  }

  .subtitle {
    font-family: 'Space Mono', 'Inter', monospace;
    text-transform: uppercase;
    font-size: 0.85rem;
    letter-spacing: 2px;
    color: var(--text-muted);
  }

  .service-list {
    list-style: none;
    display: flex;
    flex-direction: column;
  }

  .service-item {
    padding: 4rem 0;
    border-bottom: 1px solid var(--border-light);
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 2rem;
    align-items: center;
    cursor: none; /* using our custom cursor */
  }

  .service-item:first-child {
    border-top: 1px solid var(--border-light);
  }

  .service-title {
    font-family: 'Syne', sans-serif;
    font-size: clamp(2rem, 4vw, 4rem);
    font-weight: 800;
    color: var(--text-main);
    transition: transform 0.4s cubic-bezier(0.165, 0.84, 0.44, 1);
  }

  .service-item:hover .service-title {
    transform: translateX(30px);
    color: var(--accent-color);
  }

  .service-desc {
    font-size: 1rem;
    color: var(--text-muted);
    line-height: 1.6;
    opacity: 0.5;
    transition: opacity 0.3s ease;
  }

  .service-item:hover .service-desc {
    opacity: 1;
  }

  @media (max-width: 900px) {
    .relative-content {
      grid-template-columns: 1fr;
    }
    .header-area {
      position: relative;
      top: 0;
      margin-bottom: 4rem;
    }
    .service-item {
      grid-template-columns: 1fr;
      padding: 2rem 0;
    }
  }
</style>
