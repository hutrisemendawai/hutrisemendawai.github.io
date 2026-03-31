<script>
  import { onMount, tick } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";
  import KineticText from "./KineticText.svelte";

  gsap.registerPlugin(ScrollTrigger);

  let aboutContainer;
  let sectionTitle;
  let bioContainer;
  let skillHeader;
  let skillsContainer;
  let glassPanel;

  const skills = [
    { name: "Java (Spring-Boot)", icon: "☕" },
    { name: "Dart (Flutter)", icon: "🎯" },
    { name: "Svelte", icon: "🔥" },
    { name: "Pocketbase", icon: "📦" },
    { name: "PostgreSQL", icon: "🐘" },
    { name: "MySQL", icon: "🗄️" },
  ];

  onMount(async () => {
    await tick();

    // ===== SECTION TITLE REVEAL =====
    gsap.from(sectionTitle, {
      scrollTrigger: {
        trigger: aboutContainer,
        start: "top 80%",
        toggleActions: "play none none reverse",
      },
      x: -80,
      opacity: 0,
      duration: 1,
      ease: "power3.out",
    });

    // ===== BIO TEXT REVEAL - Paragraph stagger =====
    const paragraphs = bioContainer.querySelectorAll("p");
    gsap.from(paragraphs, {
      scrollTrigger: {
        trigger: aboutContainer,
        start: "top 80%",
        end: "top 20%",
        toggleActions: "play none none reverse",
      },
      y: 40,
      opacity: 0,
      duration: 0.8,
      stagger: 0.25,
      ease: "power3.out",
    });

    // ===== GLASS PANEL NEON BORDER ANIMATION =====
    gsap.fromTo(glassPanel, {
      "--panel-glow": 0,
    }, {
      "--panel-glow": 1,
      duration: 1.5,
      scrollTrigger: {
        trigger: glassPanel,
        start: "top 75%",
        toggleActions: "play none none reverse",
      },
    });

    // ===== SKILLS HEADER =====
    gsap.from(skillHeader, {
      opacity: 0,
      y: 30,
      duration: 0.8,
      scrollTrigger: {
        trigger: skillsContainer,
        start: "top 85%",
        toggleActions: "play none none none",
      },
    });

    // ===== ANIMATED SKILL PILLS =====
    const pillElements = skillsContainer.querySelectorAll(".skill-pill");

    // Pre-compute random float values so they're stable across renders
    const floatParams = Array.from(pillElements).map((_, i) => ({
      y: -5 + Math.random() * 10,
      x: (Math.random() - 0.5) * 6,
      duration: 2 + Math.random() * 2,
      delay: i * 0.15,
    }));

    const startFloating = () => {
      pillElements.forEach((pill, i) => {
        gsap.to(pill, {
          y: floatParams[i].y,
          x: floatParams[i].x,
          duration: floatParams[i].duration,
          repeat: -1,
          yoyo: true,
          ease: "sine.inOut",
          delay: floatParams[i].delay,
        });
      });
    };

    gsap.fromTo(pillElements, {
      scale: 0,
      opacity: 0,
      rotation: -15,
    }, {
      scale: 1,
      opacity: 1,
      rotation: 0,
      duration: 0.6,
      stagger: 0.1,
      ease: "back.out(2)",
      onComplete: startFloating,
      scrollTrigger: {
        trigger: skillsContainer,
        start: "top 85%",
        toggleActions: "play none none none",
      },
    });

    // ===== SKILL PILL HOVER GLOW (via mouse events) =====
    pillElements.forEach(pill => {
      pill.addEventListener("mouseenter", () => {
        gsap.to(pill, {
          scale: 1.1,
          duration: 0.3,
          ease: "power2.out",
        });
      });
      pill.addEventListener("mouseleave", () => {
        gsap.to(pill, {
          scale: 1,
          duration: 0.3,
          ease: "power2.out",
        });
      });
    });
  });
</script>

<section bind:this={aboutContainer} id="about" class="about-section">
  <!-- Feature 5: Kinetic Typography (scatter variant) -->
  <div class="kinetic-header">
    <KineticText text="About Me" variant="scatter" />
  </div>

  <div class="neon-border-card content-container" bind:this={glassPanel}>
    <h2 bind:this={sectionTitle}><span class="neon-text">/</span> About Me</h2>

    <div bind:this={bioContainer} class="bio">
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
          <div class="skill-pill">
            <span class="skill-icon">{skill.icon}</span>
            <span class="skill-name">{skill.name}</span>
          </div>
        {/each}
      </div>
    </div>
  </div>
</section>

<style>
  .about-section {
    padding-top: 100px;
    align-items: center;
    opacity: 1;
    transform: none;
  }

  .kinetic-header {
    margin-bottom: 2rem;
    width: 100%;
    display: flex;
    justify-content: center;
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
    animation: glowPulse 3s ease-in-out infinite;
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

  .bio :global(.word-wrap) {
    display: inline-block;
    overflow: hidden;
  }

  .bio :global(.word) {
    display: inline-block;
    will-change: transform, opacity;
  }

  .bio :global(strong) {
    color: var(--starlight);
    font-weight: 600;
    text-shadow: 0 0 8px rgba(255, 255, 255, 0.15);
  }

  h3 {
    font-size: 1.5rem;
    margin-bottom: 1.5rem;
    color: var(--star-gold);
  }

  .skills-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 14px;
  }

  .skill-pill {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 10px 20px;
    background: rgba(157, 78, 221, 0.08);
    border: 1px solid var(--nebula-purple);
    border-radius: 50px;
    font-family: "Space Mono", monospace;
    font-size: 0.9rem;
    color: var(--starlight);
    cursor: default;
    transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    position: relative;
    overflow: hidden;
    will-change: transform;
  }

  .skill-pill::before {
    content: '';
    position: absolute;
    top: 0;
    left: -100%;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, transparent, rgba(0, 243, 255, 0.1), transparent);
    transition: left 0.5s ease;
  }

  .skill-pill:hover::before {
    left: 100%;
  }

  .skill-pill:hover {
    background: rgba(157, 78, 221, 0.25);
    border-color: var(--neon-blue);
    box-shadow:
      0 0 15px rgba(0, 243, 255, 0.4),
      0 0 30px rgba(0, 243, 255, 0.15),
      inset 0 0 10px rgba(0, 243, 255, 0.05);
    transform: translateY(-3px);
  }

  .skill-icon {
    font-size: 1.1rem;
    filter: drop-shadow(0 0 4px rgba(255, 255, 255, 0.3));
  }

  @media (max-width: 768px) {
    h2 {
      font-size: 2rem;
    }
    .skills-grid {
      gap: 10px;
    }
    .skill-pill {
      padding: 8px 14px;
      font-size: 0.8rem;
    }
  }
</style>
