<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  gsap.registerPlugin(ScrollTrigger);

  let expContainer;
  let timelineLine;
  let expItems;

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
    // Animate the timeline line drawing down
    gsap.from(timelineLine, {
      scrollTrigger: {
        trigger: expContainer,
        start: "top 60%",
        end: "bottom 80%",
        scrub: 1,
      },
      scaleY: 0,
      transformOrigin: "top center",
      ease: "none",
    });

    // Animate each experience item popping in
    const items = gsap.utils.toArray(expItems.children);
    items.forEach((item, i) => {
      gsap.from(item, {
        scrollTrigger: {
          trigger: item,
          start: "top 85%",
          toggleActions: "play none none reverse",
        },
        x: i % 2 === 0 ? -50 : 50,
        opacity: 0,
        duration: 0.8,
        ease: "back.out(1.5)",
      });
    });
  });
</script>

<section bind:this={expContainer} id="experience" class="exp-section">
  <div class="header-container">
    <h2><span class="neon-text">/</span> Experience</h2>
  </div>

  <div class="timeline-container">
    <div bind:this={timelineLine} class="timeline-line"></div>

    <div bind:this={expItems} class="timeline-items">
      {#each experiences as exp, i}
        <div class="timeline-item {i % 2 === 0 ? 'left' : 'right'}">
          <div class="timeline-node"></div>
          <div class="glass-panel exp-card">
            <h3>{exp.role}</h3>
            <h4>{exp.company}</h4>
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
    opacity: 1; /* override global for specific ScrollTrigger */
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
      var(--nebula-purple)
    );
    transform: translateX(-50%);
    border-radius: 2px;
    box-shadow: 0 0 15px var(--neon-blue);
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
    background-color: var(--space-black);
    border: 4px solid var(--neon-blue);
    border-radius: 50%;
    box-shadow: 0 0 15px var(--neon-blue);
    z-index: 1;
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
    transition:
      transform 0.3s ease,
      box-shadow 0.3s ease;
  }

  .exp-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 12px 40px rgba(157, 78, 221, 0.3);
    border-color: var(--nebula-purple);
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
    padding: 4px 10px;
    border: 1px solid var(--glass-border);
    border-radius: 12px;
    background: rgba(255, 255, 255, 0.05);
    color: var(--text-main);
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
