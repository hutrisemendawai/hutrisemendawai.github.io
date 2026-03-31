<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import { ScrollTrigger } from "gsap/ScrollTrigger";

  gsap.registerPlugin(ScrollTrigger);

  let eduContainer;
  let eduCards;
  let certCards;
  let sectionTitle;

  const education = [
    {
      school: "Universitas Pelita Harapan (UPH)",
      degree: "Master's degree, Informatics",
      period: "Aug 2025 – Dec 2026",
      details: "Focus: Artificial Intelligence (AI)",
      highlight: true,
    },
    {
      school: "Universitas Sriwijaya",
      degree: "Bachelor's degree, Sistem Informasi",
      period: "Aug 2021 – Dec 2024",
      details: "GPA: 3.97 | Focus: Java, PHP, Software Engineering",
      highlight: false,
    },
  ];

  const certifications = [
    {
      name: "IBM Project Manager",
      issuer: "Coursera (IBM & SkillUp EdTech)",
      date: "Jul 2024",
      id: "FLC3RDKJHCVK",
    },
    {
      name: "Full-Stack Developer with Laravel",
      issuer: "BuildWithAngga",
      date: "Aug 2023",
      id: "5hwfFayaNh",
    },
  ];

  function handleCardMouseMove(e) {
    const card = e.currentTarget;
    const rect = card.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;
    const centerX = rect.width / 2;
    const centerY = rect.height / 2;

    const rotateX = ((y - centerY) / centerY) * -8;
    const rotateY = ((x - centerX) / centerX) * 8;

    // 3D tilt
    gsap.to(card, {
      rotateX,
      rotateY,
      duration: 0.3,
      ease: "power2.out",
      transformPerspective: 800,
    });

    // Spotlight cursor effect
    card.style.setProperty("--spotlight-x", `${x}px`);
    card.style.setProperty("--spotlight-y", `${y}px`);
  }

  function handleCardMouseLeave(e) {
    const card = e.currentTarget;
    gsap.to(card, {
      rotateX: 0,
      rotateY: 0,
      duration: 0.6,
      ease: "elastic.out(1, 0.5)",
    });
  }

  onMount(() => {
    // ===== SECTION TITLE =====
    gsap.from(sectionTitle, {
      scrollTrigger: {
        trigger: eduContainer,
        start: "top 75%",
        toggleActions: "play none none reverse",
      },
      y: 50,
      opacity: 0,
      duration: 1,
      ease: "power3.out",
    });

    // ===== EDUCATION CARDS - Bouncy Stagger =====
    const eduCardEls = eduCards.querySelectorAll(".edu-card");
    gsap.from(eduCardEls, {
      scrollTrigger: {
        trigger: eduContainer,
        start: "top 75%",
        toggleActions: "play none none reverse",
      },
      y: 80,
      opacity: 0,
      rotateX: -20,
      duration: 1,
      stagger: 0.25,
      ease: "back.out(1.7)",
      transformPerspective: 800,
    });

    // ===== FLOATING ICONS =====
    const icons = eduContainer.querySelectorAll(".icon");
    icons.forEach((icon, i) => {
      gsap.to(icon, {
        y: -8,
        rotation: 5,
        duration: 2 + Math.random(),
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut",
        delay: i * 0.3,
      });
    });

    // ===== CERT CARDS - Scale + Bounce =====
    const certCardEls = certCards.querySelectorAll(".cert-card");
    gsap.from(certCardEls, {
      scrollTrigger: {
        trigger: certCards,
        start: "top 85%",
        toggleActions: "play none none reverse",
      },
      scale: 0.8,
      y: 60,
      opacity: 0,
      rotateY: -10,
      duration: 0.9,
      stagger: 0.2,
      ease: "back.out(1.5)",
      transformPerspective: 800,
    });
  });
</script>

<section bind:this={eduContainer} id="education" class="edu-section">
  <div class="content-wrapper">
    <span class="section-label">Academic background</span>
    <h2 bind:this={sectionTitle}>Education & Certifications</h2>

    <div class="subtitle"><h3>Academic Journey</h3></div>
    <div bind:this={eduCards} class="cards-grid">
      {#each education as edu}
        <div
          class="edu-card {edu.highlight ? 'highlight' : ''}"
          on:mousemove={handleCardMouseMove}
          on:mouseleave={handleCardMouseLeave}
          role="article"
        >
          <div class="card-spotlight"></div>
          <div class="card-inner">
            <div class="icon">🎓</div>
            <h3>{edu.school}</h3>
            <h4>{edu.degree}</h4>
            <span class="period">{edu.period}</span>
            <p>{edu.details}</p>
          </div>
        </div>
      {/each}
    </div>

    <div class="subtitle cert-subtitle"><h3>Licenses & Certifications</h3></div>
    <div bind:this={certCards} class="cards-grid cert-grid">
      {#each certifications as cert}
        <div
          class="cert-card"
          on:mousemove={handleCardMouseMove}
          on:mouseleave={handleCardMouseLeave}
          role="article"
        >
          <div class="card-spotlight"></div>
          <div class="card-inner">
            <div class="icon">📜</div>
            <h3>{cert.name}</h3>
            <h4>{cert.issuer}</h4>
            <span class="period">Issued {cert.date}</span>
            <p class="cert-id" title="Credential ID">ID: {cert.id}</p>
          </div>
        </div>
      {/each}
    </div>
  </div>
</section>

<style>
  .edu-section {
    padding-top: 100px;
    opacity: 1;
    transform: none;
  }

  .content-wrapper {
    max-width: 1000px;
    width: 100%;
    margin: 0 auto;
  }

  h2 {
    font-size: 2.5rem;
    margin-bottom: 3rem;
    text-align: center;
    letter-spacing: -1px;
  }

  .subtitle {
    margin-bottom: 2rem;
    padding-bottom: 0.5rem;
    border-bottom: 1px solid var(--glass-border);
  }

  .cert-subtitle {
    margin-top: 4rem;
  }

  h3 {
    font-size: 1.5rem;
    color: var(--starlight);
  }

  .cards-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 2rem;
  }

  /* ===== 3D TILT CARDS WITH SPOTLIGHT ===== */
  .edu-card,
  .cert-card {
    position: relative;
    overflow: hidden;
    background: rgba(15, 15, 35, 0.6);
    backdrop-filter: blur(16px);
    -webkit-backdrop-filter: blur(16px);
    border: 1px solid var(--glass-border);
    border-radius: 20px;
    padding: 2rem;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
    will-change: transform;
    transform-style: preserve-3d;
    cursor: default;
    transition: border-color 0.4s ease, box-shadow 0.4s ease;
  }

  .edu-card:hover,
  .cert-card:hover {
    border-color: rgba(0, 212, 255, 0.2);
    box-shadow:
      0 15px 35px rgba(0, 212, 255, 0.08),
      0 0 20px rgba(0, 212, 255, 0.03);
  }

  /* Spotlight cursor follower on card surface */
  .card-spotlight {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: radial-gradient(
      300px circle at var(--spotlight-x, -100px) var(--spotlight-y, -100px),
      rgba(0, 212, 255, 0.08),
      transparent 60%
    );
    pointer-events: none;
    z-index: 1;
  }

  .card-inner {
    position: relative;
    z-index: 2;
  }

  .edu-card.highlight {
    background: linear-gradient(
      135deg,
      rgba(139, 92, 246, 0.08),
      rgba(0, 212, 255, 0.04)
    );
    border-color: rgba(139, 92, 246, 0.2);
  }

  .edu-card.highlight:hover {
    box-shadow:
      0 15px 40px rgba(139, 92, 246, 0.12),
      0 0 30px rgba(139, 92, 246, 0.05);
    border-color: rgba(139, 92, 246, 0.35);
  }

  .icon {
    font-size: 2.5rem;
    margin-bottom: 1rem;
    filter: drop-shadow(0 0 8px rgba(255, 255, 255, 0.3));
    display: inline-block;
    will-change: transform;
  }

  .edu-card h3,
  .cert-card h3 {
    font-size: 1.2rem;
    margin-bottom: 0.5rem;
    line-height: 1.4;
  }

  .edu-card h4,
  .cert-card h4 {
    font-size: 1rem;
    color: var(--neon-blue);
    margin-bottom: 1rem;
    font-family: "Outfit", sans-serif;
    font-weight: 400;
  }

  .period {
    display: inline-block;
    font-family: "Space Mono", monospace;
    font-size: 0.85rem;
    color: var(--star-gold);
    margin-bottom: 1rem;
    padding: 3px 10px;
    background: rgba(255, 214, 10, 0.08);
    border-radius: 12px;
    text-shadow: 0 0 6px rgba(255, 214, 10, 0.3);
  }

  p {
    color: var(--text-main);
    line-height: 1.5;
    font-size: 0.95rem;
  }

  .cert-id {
    font-family: "Space Mono", monospace;
    font-size: 0.8rem;
    color: var(--text-muted);
    background: rgba(0, 0, 0, 0.3);
    padding: 0.3rem 0.6rem;
    border-radius: 4px;
    display: inline-block;
    margin-top: 0.5rem;
    border: 1px solid var(--glass-border);
    transition: all 0.3s ease;
  }

  .cert-id:hover {
    border-color: var(--neon-blue);
    color: var(--neon-blue);
  }

  @media (max-width: 768px) {
    h2 {
      font-size: 2rem;
    }
    .cards-grid {
      grid-template-columns: 1fr;
    }
  }

  /* ===== Scroll-Driven: Education card 3D entrance ===== */
  @supports (animation-timeline: scroll()) {
    .edu-card,
    .cert-card {
      animation: sda-edu-card-enter linear both;
      animation-timeline: view();
      animation-range: entry 0% entry 80%;
    }

    @keyframes sda-edu-card-enter {
      from {
        opacity: 0;
        transform: perspective(800px) rotateY(-15deg) translateY(40px);
      }
      to {
        opacity: 1;
        transform: perspective(800px) rotateY(0deg) translateY(0);
      }
    }
  }
</style>
