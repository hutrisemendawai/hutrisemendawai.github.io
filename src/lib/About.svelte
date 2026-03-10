<script>
  import { onMount, tick } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  gsap.registerPlugin(ScrollTrigger);

  let aboutContainer;
  let textElements;
  let skillHeader;
  let skillsContainer;

  const skills = [
    "Java (Spring-Boot)",
    "Dart (Flutter)",
    "Svelte",
    "Pocketbase",
    "PostgreSQL",
    "MySQL",
  ];

  onMount(async () => {
    await tick();

    // Text reveal
    gsap.from(textElements.children, {
      scrollTrigger: {
        trigger: aboutContainer,
        start: "top 80%",
        end: "top 20%",
        toggleActions: "play none none reverse",
      },
      y: 50,
      opacity: 0,
      duration: 1,
      stagger: 0.2,
      ease: "power3.out",
    });

    // Skills reveal
    gsap.fromTo(skillHeader, 
      { opacity: 0, y: 30 },
      {
        opacity: 1, 
        y: 0, 
        duration: 0.8,
        scrollTrigger: {
          trigger: skillsContainer,
          start: "top 85%",
          toggleActions: "play none none none",
        }
      }
    );

    gsap.fromTo(skillsContainer.children,
      { scale: 0, opacity: 0 },
      {
        scale: 1,
        opacity: 1,
        duration: 0.5,
        stagger: 0.1,
        ease: "back.out(1.7)",
        scrollTrigger: {
          trigger: skillsContainer,
          start: "top 85%",
          toggleActions: "play none none none",
        }
      }
    );
  });
</script>

<section bind:this={aboutContainer} id="about" class="about-section">
  <div class="glass-panel content-container">
    <h2><span class="neon-text">/</span> About Me</h2>

    <div bind:this={textElements} class="bio">
      <p>
        As a passionate <strong>Software Engineer</strong> and
        <strong>ERP Specialist</strong>, I thrive at the intersection of robust
        backend architecture and captivating user experiences. Currently
        developing scalable solutions at
        <strong>Charoen Pokphand Indonesia</strong>
        while pursuing my Master's in Informatics at <strong>UPH</strong>.
      </p>
      <p>
        I specialize in crafting comprehensive enterprise systems using Java,
        PostgreSQL, JasperReports, and iDempiere ERP. Beyond the backend, I
        bring ideas to life across platforms—building fluid, offline-first
        Android applications with Flutter and Dart, and engineering highly
        interactive, animated front-end web experiences using Svelte and GSAP.
      </p>
      <p>
        Whether I'm optimizing complex business logic or designing seamless,
        dynamic interfaces, I am driven by a commitment to continuous learning,
        collaborative problem-solving, and delivering impactful technological
        innovations.
      </p>
    </div>

    <div class="skills-section">
      <h3 bind:this={skillHeader}>Core Technologies</h3>
      <div bind:this={skillsContainer} class="skills-grid">
        {#each skills as skill}
          <div class="skill-pill">{skill}</div>
        {/each}
      </div>
    </div>
  </div>
</section>

<style>
  .about-section {
    padding-top: 100px;
    align-items: center;
    opacity: 1; /* override global for specific ScrollTrigger */
    transform: none;
  }

  .content-container {
    max-width: 900px;
    width: 100%;
    margin: 0 auto;
  }

  h2 {
    font-size: 2.5rem;
    margin-bottom: 2rem;
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .neon-text {
    color: var(--neon-blue);
    text-shadow: 0 0 10px rgba(0, 243, 255, 0.5);
  }

  .bio {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
    font-size: 1.1rem;
    line-height: 1.7;
    color: var(--text-main);
    margin-bottom: 3rem;
  }

  .bio strong {
    color: var(--starlight);
    font-weight: 600;
  }

  h3 {
    font-size: 1.5rem;
    margin-bottom: 1.5rem;
    color: var(--star-gold);
  }

  .skills-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
  }

  .skill-pill {
    padding: 8px 16px;
    background: rgba(157, 78, 221, 0.1);
    border: 1px solid var(--nebula-purple);
    border-radius: 20px;
    font-family: "Space Mono", monospace;
    font-size: 0.9rem;
    color: var(--starlight);
    transition: all 0.3s ease;
    cursor: default;
  }

  .skill-pill:hover {
    background: rgba(157, 78, 221, 0.3);
    border-color: var(--neon-blue);
    box-shadow: 0 0 15px rgba(0, 243, 255, 0.4);
    transform: translateY(-2px);
  }
</style>
