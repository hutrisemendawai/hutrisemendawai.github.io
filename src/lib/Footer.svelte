<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  let footerRef;
  let textRef;

  onMount(() => {
    gsap.registerPlugin(ScrollTrigger);
    
    // Animate footer text on scroll into view
    gsap.fromTo(textRef,
      { y: 100, opacity: 0 },
      {
        y: 0, opacity: 1, duration: 1.2, ease: "power4.out",
        scrollTrigger: {
          trigger: footerRef,
          start: "top 80%"
        }
      }
    );
  });
</script>

<footer id="contact" bind:this={footerRef}>
  <div class="container footer-content">
    <div class="contact-cta">
      <h2 bind:this={textRef} class="cta-text">
        <a href="mailto:hello@aether.com" class="interactive">LET'S<br>COLLABORATE.</a>
      </h2>
    </div>

    <div class="footer-bottom">
      <div class="footer-col">
        <h4>AETHER</h4>
        <p>Ahmad Hutri Semendawai</p>
        <p>Software Engineer & Edge AI Specialist</p>
      </div>

      <div class="footer-col">
        <h4>Socials</h4>
        <ul>
          <li><a href="#/" class="interactive">LinkedIn</a></li>
          <li><a href="#/" class="interactive">GitHub</a></li>
          <li><a href="#/" class="interactive">Twitter</a></li>
        </ul>
      </div>

      <div class="footer-col right-align">
        <h4>Location</h4>
        <p>Global / Remote</p>
        <p>Earth</p>
        <p class="time">Local Time: 24:00:00</p>
      </div>
    </div>
  </div>
</footer>

<style>
  footer {
    background-color: var(--monopo-black);
    padding: 10rem 0 2rem 0;
    color: var(--text-main);
  }

  .footer-content {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    min-height: 50vh;
  }

  .contact-cta {
    margin-bottom: 15rem;
  }

  .cta-text {
    font-size: clamp(4rem, 12vw, 12rem);
    line-height: 0.9;
    font-family: 'Syne', sans-serif;
  }

  .cta-text a {
    color: var(--text-main);
    transition: color 0.3s ease;
  }

  .cta-text a:hover {
    color: var(--accent-color);
  }

  .footer-bottom {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 2rem;
    border-top: 1px solid var(--border-light);
    padding-top: 4rem;
  }

  h4 {
    font-size: 1rem;
    font-family: 'Inter', sans-serif;
    color: var(--text-muted);
    margin-bottom: 1.5rem;
  }

  .footer-col p, .footer-col li {
    font-family: 'Inter', sans-serif;
    font-size: 1rem;
    line-height: 1.6;
    color: var(--text-main);
  }

  .footer-col ul {
    list-style: none;
  }

  .footer-col a {
    transition: color 0.3s ease;
  }

  .footer-col a:hover {
    color: var(--accent-color);
  }

  .right-align {
    text-align: right;
  }

  .time {
    margin-top: 1rem;
    font-family: 'Space Mono', monospace;
    opacity: 0.5;
  }

  @media (max-width: 900px) {
    .contact-cta {
      margin-bottom: 8rem;
    }
    .footer-bottom {
      grid-template-columns: 1fr;
      gap: 3rem;
    }
    .right-align {
      text-align: left;
    }
  }
</style>
