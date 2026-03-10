<script>
  import { onMount } from 'svelte';
  import gsap from 'gsap';
  import { ScrollTrigger } from 'gsap/ScrollTrigger';

  gsap.registerPlugin(ScrollTrigger);

  let eduContainer;
  let eduCards;
  let certCards;

  const education = [
    {
      school: 'Universitas Pelita Harapan (UPH)',
      degree: "Master's degree, Informatics",
      period: 'Aug 2025 – Dec 2026',
      details: 'Focus: Artificial Intelligence (AI)',
      highlight: true
    },
    {
      school: 'Universitas Sriwijaya',
      degree: "Bachelor's degree, Sistem Informasi",
      period: 'Aug 2021 – Dec 2024',
      details: 'GPA: 3.97 | Focus: Java, PHP, Software Engineering',
      highlight: false
    }
  ];

  const certifications = [
    {
      name: 'IBM Project Manager',
      issuer: 'Coursera (IBM & SkillUp EdTech)',
      date: 'Jul 2024',
      id: 'FLC3RDKJHCVK'
    },
    {
      name: 'Full-Stack Developer with Laravel',
      issuer: 'BuildWithAngga',
      date: 'Aug 2023',
      id: '5hwfFayaNh'
    }
  ];

  onMount(() => {
    // Education cards stagger
    gsap.from(eduCards.children, {
      scrollTrigger: {
        trigger: eduContainer,
        start: "top 75%",
        toggleActions: "play none none reverse"
      },
      y: 60,
      opacity: 0,
      duration: 0.8,
      stagger: 0.2,
      ease: "power2.out"
    });

    // Cert cards stagger
    gsap.from(certCards.children, {
      scrollTrigger: {
        trigger: certCards,
        start: "top 85%",
        toggleActions: "play none none reverse"
      },
      scale: 0.9,
      y: 40,
      opacity: 0,
      duration: 0.8,
      stagger: 0.2,
      ease: "back.out(1.2)"
    });
  });
</script>

<section bind:this={eduContainer} id="education" class="edu-section">
  <div class="content-wrapper">
    <h2><span class="neon-text">/</span> Education & Certifications</h2>
    
    <div class="subtitle"><h3>Academic Journey</h3></div>
    <div bind:this={eduCards} class="cards-grid">
      {#each education as edu}
        <div class="glass-panel edu-card {edu.highlight ? 'highlight' : ''}">
          <div class="icon">🎓</div>
          <h3>{edu.school}</h3>
          <h4>{edu.degree}</h4>
          <span class="period">{edu.period}</span>
          <p>{edu.details}</p>
        </div>
      {/each}
    </div>

    <div class="subtitle cert-subtitle"><h3>Licenses & Certifications</h3></div>
    <div bind:this={certCards} class="cards-grid cert-grid">
      {#each certifications as cert}
        <div class="glass-panel cert-card">
          <div class="icon">📜</div>
          <h3>{cert.name}</h3>
          <h4>{cert.issuer}</h4>
          <span class="period">Issued {cert.date}</span>
          <p class="cert-id" title="Credential ID">ID: {cert.id}</p>
        </div>
      {/each}
    </div>
  </div>
</section>

<style>
  .edu-section {
    padding-top: 100px;
    opacity: 1; /* override global for specific ScrollTrigger */
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
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
  }

  .neon-text {
    color: var(--neon-blue);
    text-shadow: 0 0 10px rgba(0, 243, 255, 0.5);
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

  .edu-card, .cert-card {
    position: relative;
    overflow: hidden;
    transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275), box-shadow 0.4s ease;
  }

  .edu-card:hover, .cert-card:hover {
    transform: translateY(-10px) scale(1.02);
    box-shadow: 0 15px 35px rgba(0, 243, 255, 0.15);
    border-color: rgba(0, 243, 255, 0.3);
  }

  .edu-card.highlight {
    background: linear-gradient(135deg, rgba(157, 78, 221, 0.1), rgba(0, 243, 255, 0.05));
    border-color: rgba(157, 78, 221, 0.3);
  }

  .edu-card.highlight:hover {
    box-shadow: 0 15px 40px rgba(157, 78, 221, 0.25);
    border-color: rgba(157, 78, 221, 0.6);
  }

  .icon {
    font-size: 2.5rem;
    margin-bottom: 1rem;
    filter: drop-shadow(0 0 8px rgba(255,255,255,0.3));
  }

  .edu-card h3, .cert-card h3 {
    font-size: 1.2rem;
    margin-bottom: 0.5rem;
    line-height: 1.4;
  }

  .edu-card h4, .cert-card h4 {
    font-size: 1rem;
    color: var(--neon-blue);
    margin-bottom: 1rem;
    font-family: 'Outfit', sans-serif;
    font-weight: 400;
  }

  .period {
    display: block;
    font-family: 'Space Mono', monospace;
    font-size: 0.85rem;
    color: var(--star-gold);
    margin-bottom: 1rem;
  }

  p {
    color: var(--text-main);
    line-height: 1.5;
    font-size: 0.95rem;
  }

  .cert-id {
    font-family: 'Space Mono', monospace;
    font-size: 0.8rem;
    color: var(--text-muted);
    background: rgba(0,0,0,0.3);
    padding: 0.3rem 0.6rem;
    border-radius: 4px;
    display: inline-block;
    margin-top: 0.5rem;
    border: 1px solid var(--glass-border);
  }
</style>
