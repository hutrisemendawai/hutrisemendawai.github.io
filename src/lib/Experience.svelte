<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  gsap.registerPlugin(ScrollTrigger);

  let expContainer;
  let timelineLine;
  let expItems;
  let sectionTitle;

  const experiences = [
    {
      role: "Java Programmer",
      company: "Charoen Pokphand Indonesia",
      date: "May 2025 - Present",
      location: "Jakarta, Indonesia",
      description:
        "Developing and optimizing enterprise ERP systems using Java and Spring Boot. Enhancing business logic and database performance.",
      skills: ["Java", "Spring Boot", "ERP", "PostgreSQL"],
    },
    {
      role: "Software Developer Internship",
      company: "Charoen Pokphand Indonesia",
      date: "Dec 2023 - Feb 2024",
      location: "Palembang, Indonesia",
      description:
        "Assisted in building scalable backend services. Collaborated with cross-functional teams to deliver software solutions.",
      skills: ["Java", "Software Development"],
    },
  ];

  onMount(() => {
    // ===== SECTION TITLE REVEAL =====
    gsap.from(sectionTitle, {
      scrollTrigger: {
        trigger: expContainer,
        start: "top 75%",
        toggleActions: "play none none reverse",
      },
      x: -60,
      opacity: 0,
      duration: 1,
      ease: "power3.out",
    });

    // ===== TIMELINE LINE DRAW WITH GLOW =====
    gsap.fromTo(timelineLine, {
      scaleY: 0,
      opacity: 0.5,
    }, {
      scaleY: 1,
      opacity: 1,
      transformOrigin: "top center",
      ease: "none",
      scrollTrigger: {
        trigger: expContainer,
        start: "top 60%",
        end: "bottom 80%",
        scrub: 1,
      },
    });

    // ===== EXPERIENCE ITEMS - 3D FLY-IN =====
    const items = gsap.utils.toArray(expItems.children);
    items.forEach((item, i) => {
      const card = item.querySelector(".exp-card");
      const node = item.querySelector(".timeline-node");
      const isLeft = i % 2 === 0;

      // Card fly-in with 3D rotation
      gsap.from(card, {
        scrollTrigger: {
          trigger: item,
          start: "top 85%",
          toggleActions: "play none none reverse",
        },
        x: isLeft ? -80 : 80,
        opacity: 0,
        rotateY: isLeft ? -15 : 15,
        duration: 1,
        ease: "power3.out",
      });

      // ===== NEON PULSE ON NODES =====
      gsap.fromTo(node, {
        scale: 0,
        boxShadow: "0 0 5px var(--neon-blue)",
      }, {
        scale: 1,
        boxShadow: "0 0 25px var(--neon-blue), 0 0 50px rgba(0, 243, 255, 0.3)",
        duration: 0.6,
        ease: "back.out(2)",
        scrollTrigger: {
          trigger: item,
          start: "top 85%",
          toggleActions: "play none none reverse",
        },
      });

      // Node continuous pulse after appearing
      gsap.to(node, {
        boxShadow: "0 0 35px var(--neon-blue), 0 0 60px rgba(0, 243, 255, 0.4)",
        duration: 1.5,
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut",
        delay: 1,
      });

      // ===== SKILL TAGS MICRO-STAGGER =====
      const skillTags = card.querySelectorAll(".skill-tag");
      gsap.from(skillTags, {
        scrollTrigger: {
          trigger: item,
          start: "top 80%",
          toggleActions: "play none none reverse",
        },
        y: 20,
        opacity: 0,
        scale: 0.8,
        stagger: 0.08,
        duration: 0.4,
        ease: "back.out(1.5)",
        delay: 0.5,
      });
    });
  });
</script>

<section bind:this={expContainer} id="experience" class="exp-section">
  <div class="header-container" bind:this={sectionTitle}>
    <h2><span class="neon-text">/</span> Experience</h2>
  </div>

  <div class="timeline-container">
    <div bind:this={timelineLine} class="timeline-line"></div>

    <div bind:this={expItems} class="timeline-items">
      {#each experiences as exp, i}
        <div class="timeline-item {i % 2 === 0 ? 'left' : 'right'}">
          <div class="timeline-node">
            <div class="node-ring"></div>
          </div>
          <div class="exp-card neon-border-card">
            <div class="card-header">
              <h3>{exp.role}</h3>
              <h4>{exp.company}</h4>
            </div>
            <div class="meta">
              <span class="date">{exp.date}</span>
              <span class="location"> • {exp.location}</span>
            </div>
            <p>{exp.description}</p>
            <div class="skills-list">
              {#each exp.skills as skill}
                <span class="skill-tag">{skill}</span>
              {/each}
            </div>
          </div>
        </div>
      {/each}
    </div>
  </div>
</section>

<style>
  .exp-section {
    padding-top: 100px;
    align-items: center;
    position: relative;
    opacity: 1;
    transform: none;
  }

  .header-container {
    max-width: 900px;
    width: 100%;
    margin-bottom: 4rem;
  }

  h2 {
    font-size: 2.5rem;
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .neon-text {
    color: var(--neon-blue);
    text-shadow: 0 0 10px rgba(0, 243, 255, 0.5);
    animation: glowPulse 3s ease-in-out infinite;
  }

  .timeline-container {
    position: relative;
    max-width: 1000px;
    width: 100%;
    margin: 0 auto;
  }

  .timeline-line {
    position: absolute;
    left: 50%;
    top: 0;
    bottom: 0;
    width: 4px;
    background: linear-gradient(
      to bottom,
      var(--neon-blue),
      var(--nebula-purple),
      var(--aurora-green)
    );
    transform: translateX(-50%);
    border-radius: 2px;
    box-shadow: 0 0 20px var(--neon-blue), 0 0 40px rgba(0, 243, 255, 0.2);
  }

  .timeline-items {
    display: flex;
    flex-direction: column;
    gap: 4rem;
  }

  .timeline-item {
    position: relative;
    width: 50%;
    display: flex;
    align-items: flex-start;
    perspective: 1000px;
  }

  .timeline-item.left {
    justify-content: flex-end;
    padding-right: 40px;
    text-align: right;
  }

  .timeline-item.right {
    margin-left: 50%;
    padding-left: 40px;
  }

  .timeline-node {
    position: absolute;
    top: 20px;
    width: 20px;
    height: 20px;
    background-color: var(--neon-blue);
    border: 4px solid var(--space-black);
    border-radius: 50%;
    box-shadow: 0 0 15px var(--neon-blue);
    z-index: 1;
    will-change: box-shadow;
  }

  .node-ring {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 30px;
    height: 30px;
    border: 1px solid rgba(0, 243, 255, 0.3);
    border-radius: 50%;
    animation: nodeRingPulse 2s ease-out infinite;
  }

  @keyframes nodeRingPulse {
    0% { transform: translate(-50%, -50%) scale(1); opacity: 0.6; }
    100% { transform: translate(-50%, -50%) scale(2.5); opacity: 0; }
  }

  .timeline-item.left .timeline-node {
    right: -10px;
  }

  .timeline-item.right .timeline-node {
    left: -10px;
  }

  .exp-card {
    width: 100%;
    padding: 2rem;
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    will-change: transform;
  }

  .exp-card:hover {
    transform: translateY(-8px) scale(1.02);
  }

  .card-header {
    margin-bottom: 0.5rem;
  }

  h3 {
    font-size: 1.5rem;
    color: var(--starlight);
    margin-bottom: 0.5rem;
  }

  h4 {
    font-size: 1.1rem;
    color: var(--neon-blue);
    margin-bottom: 1rem;
    font-family: "Outfit", sans-serif;
    font-weight: 600;
  }

  .meta {
    font-family: "Space Mono", monospace;
    font-size: 0.85rem;
    color: var(--text-muted);
    margin-bottom: 1.5rem;
  }

  .date {
    color: var(--star-gold);
    text-shadow: 0 0 8px rgba(255, 214, 10, 0.3);
  }

  p {
    font-size: 1rem;
    line-height: 1.6;
    color: var(--text-main);
    margin-bottom: 1.5rem;
    text-align: left;
  }

  .timeline-item.left p {
    text-align: right;
  }

  .skills-list {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    justify-content: flex-start;
  }

  .timeline-item.left .skills-list {
    justify-content: flex-end;
  }

  .skill-tag {
    font-family: "Space Mono", monospace;
    font-size: 0.75rem;
    padding: 5px 12px;
    border: 1px solid var(--glass-border);
    border-radius: 20px;
    background: rgba(255, 255, 255, 0.05);
    color: var(--text-main);
    transition: all 0.3s ease;
  }

  .skill-tag:hover {
    background: rgba(0, 243, 255, 0.1);
    border-color: var(--neon-blue);
    color: var(--neon-blue);
    box-shadow: 0 0 10px rgba(0, 243, 255, 0.2);
    transform: translateY(-2px);
  }

  @media (max-width: 768px) {
    .timeline-line {
      left: 20px;
    }
    .timeline-item.left,
    .timeline-item.right {
      width: 100%;
      margin-left: 0;
      padding-left: 60px;
      padding-right: 0;
      text-align: left;
    }
    .timeline-item.left .timeline-node,
    .timeline-item.right .timeline-node {
      left: 10px;
      right: auto;
    }
    .timeline-item.left p {
      text-align: left;
    }
    .timeline-item.left .skills-list {
      justify-content: flex-start;
    }
  }
</style>
