<script>
  import { onMount } from "svelte";

  let mascotEl;
  let leftPupil = { x: 0, y: 0 };
  let rightPupil = { x: 0, y: 0 };
  let mouthState = "idle"; // idle, happy, surprised
  let isWaving = false;
  let isBlinking = false;
  let blinkTimer;

  function handleMouseMove(e) {
    if (!mascotEl) return;
    const rect = mascotEl.getBoundingClientRect();
    const centerX = rect.left + rect.width / 2;
    const centerY = rect.top + rect.height / 2;

    const dx = e.clientX - centerX;
    const dy = e.clientY - centerY;
    const distance = Math.hypot(dx, dy);
    const maxMove = 4;
    const factor = Math.min(distance / 300, 1);

    const moveX = (dx / (distance || 1)) * maxMove * factor;
    const moveY = (dy / (distance || 1)) * maxMove * factor;

    // Slight offset between eyes for realism
    leftPupil = { x: moveX * 0.9, y: moveY * 0.9 };
    rightPupil = { x: moveX * 1.1, y: moveY * 1.1 };

    // Determine mouth state based on proximity
    if (distance < 120) {
      mouthState = "surprised";
    } else if (distance < 300) {
      mouthState = "happy";
    } else {
      mouthState = "idle";
    }
  }

  function handleClick() {
    isWaving = true;
    setTimeout(() => (isWaving = false), 800);
  }

  function startBlinking() {
    const scheduleBlink = () => {
      const delay = 2000 + Math.random() * 4000;
      blinkTimer = setTimeout(() => {
        isBlinking = true;
        setTimeout(() => {
          isBlinking = false;
          scheduleBlink();
        }, 150);
      }, delay);
    };
    scheduleBlink();
  }

  onMount(() => {
    window.addEventListener("mousemove", handleMouseMove);
    startBlinking();

    return () => {
      window.removeEventListener("mousemove", handleMouseMove);
      clearTimeout(blinkTimer);
    };
  });
</script>

<!-- svelte-ignore a11y_click_events_have_key_events -->
<!-- svelte-ignore a11y_no_static_element_interactions -->
<div
  class="mascot-container"
  bind:this={mascotEl}
  on:click={handleClick}
>
  <svg
    viewBox="0 0 120 160"
    class="mascot-svg"
    xmlns="http://www.w3.org/2000/svg"
  >
    <!-- Helmet glow -->
    <defs>
      <radialGradient id="helmetGlow" cx="50%" cy="30%" r="60%">
        <stop offset="0%" stop-color="rgba(0, 212, 255, 0.15)" />
        <stop offset="100%" stop-color="transparent" />
      </radialGradient>
      <radialGradient id="visorGrad" cx="40%" cy="35%" r="55%">
        <stop offset="0%" stop-color="#1a3a5c" />
        <stop offset="60%" stop-color="#0a1628" />
        <stop offset="100%" stop-color="#050510" />
      </radialGradient>
      <filter id="neonGlow">
        <feGaussianBlur stdDeviation="2" result="blur" />
        <feComposite in="SourceGraphic" in2="blur" operator="over" />
      </filter>
    </defs>

    <!-- Body -->
    <g class="body-group {isWaving ? 'waving' : ''}">
      <!-- Backpack -->
      <rect x="32" y="82" width="12" height="30" rx="5" fill="#2a2a4a" stroke="#3a3a5a" stroke-width="1" />
      
      <!-- Body suit -->
      <path
        d="M40 78 Q40 72 48 70 L72 70 Q80 72 80 78 L82 120 Q82 128 75 130 L45 130 Q38 128 38 120 Z"
        fill="#e8e8f0"
        stroke="#c8c8d8"
        stroke-width="1"
      />
      
      <!-- Chest panel -->
      <rect x="50" y="85" width="20" height="15" rx="3" fill="#1a1a3a" stroke="var(--neon-blue, #00d4ff)" stroke-width="0.8" opacity="0.8" />
      <circle cx="55" cy="90" r="2" fill="var(--aurora-green, #00ff88)" class="blink-light" />
      <circle cx="62" cy="90" r="2" fill="var(--star-gold, #ffd60a)" class="blink-light-2" />
      <rect x="53" y="94" width="14" height="3" rx="1" fill="var(--neon-blue, #00d4ff)" opacity="0.4" />

      <!-- Arms -->
      <g class="left-arm {isWaving ? 'wave-anim' : ''}">
        <path
          d="M40 82 Q32 88 28 100 Q26 108 30 110"
          fill="none"
          stroke="#e8e8f0"
          stroke-width="10"
          stroke-linecap="round"
        />
        <!-- Glove -->
        <circle cx="30" cy="110" r="6" fill="#c8c8d8" stroke="#a8a8b8" stroke-width="1" />
      </g>

      <g class="right-arm">
        <path
          d="M80 82 Q88 88 92 100 Q94 108 90 110"
          fill="none"
          stroke="#e8e8f0"
          stroke-width="10"
          stroke-linecap="round"
        />
        <circle cx="90" cy="110" r="6" fill="#c8c8d8" stroke="#a8a8b8" stroke-width="1" />
      </g>

      <!-- Legs -->
      <path
        d="M48 128 Q46 140 44 148 Q42 152 46 154"
        fill="none"
        stroke="#e8e8f0"
        stroke-width="9"
        stroke-linecap="round"
      />
      <path
        d="M72 128 Q74 140 76 148 Q78 152 74 154"
        fill="none"
        stroke="#e8e8f0"
        stroke-width="9"
        stroke-linecap="round"
      />
      <!-- Boots -->
      <ellipse cx="46" cy="155" rx="8" ry="4" fill="#3a3a5a" />
      <ellipse cx="74" cy="155" rx="8" ry="4" fill="#3a3a5a" />
    </g>

    <!-- Helmet -->
    <g class="helmet-group">
      <!-- Helmet shell -->
      <ellipse cx="60" cy="48" rx="26" ry="28" fill="#d8d8e8" stroke="#b8b8c8" stroke-width="1.5" />
      
      <!-- Visor -->
      <ellipse cx="60" cy="48" rx="20" ry="22" fill="url(#visorGrad)" stroke="var(--neon-blue, #00d4ff)" stroke-width="1" filter="url(#neonGlow)" />
      
      <!-- Helmet reflection -->
      <ellipse cx="52" cy="38" rx="8" ry="4" fill="rgba(255,255,255,0.08)" transform="rotate(-15 52 38)" />

      <!-- Eyes -->
      <g class="eyes {isBlinking ? 'blink' : ''}">
        <!-- Left eye -->
        <ellipse cx="52" cy="46" rx="6" ry="7" fill="#0d1b2a" />
        <ellipse cx="52" cy="46" rx="5" ry="6" fill="#1b2838" />
        <!-- Left pupil -->
        <circle
          cx={52 + leftPupil.x}
          cy={46 + leftPupil.y}
          r="3"
          fill="var(--neon-blue, #00d4ff)"
          filter="url(#neonGlow)"
        />
        <circle
          cx={51 + leftPupil.x * 0.5}
          cy={44 + leftPupil.y * 0.5}
          r="1.2"
          fill="white"
          opacity="0.9"
        />

        <!-- Right eye -->
        <ellipse cx="68" cy="46" rx="6" ry="7" fill="#0d1b2a" />
        <ellipse cx="68" cy="46" rx="5" ry="6" fill="#1b2838" />
        <!-- Right pupil -->
        <circle
          cx={68 + rightPupil.x}
          cy={46 + rightPupil.y}
          r="3"
          fill="var(--neon-blue, #00d4ff)"
          filter="url(#neonGlow)"
        />
        <circle
          cx={67 + rightPupil.x * 0.5}
          cy={44 + rightPupil.y * 0.5}
          r="1.2"
          fill="white"
          opacity="0.9"
        />
      </g>

      <!-- Mouth -->
      {#if mouthState === "idle"}
        <line x1="55" y1="58" x2="65" y2="58" stroke="var(--neon-blue, #00d4ff)" stroke-width="1.5" stroke-linecap="round" opacity="0.6" />
      {:else if mouthState === "happy"}
        <path d="M54 56 Q60 62 66 56" fill="none" stroke="var(--aurora-green, #00ff88)" stroke-width="1.5" stroke-linecap="round" />
      {:else if mouthState === "surprised"}
        <ellipse cx="60" cy="58" rx="4" ry="5" fill="#0d1b2a" stroke="var(--neon-blue, #00d4ff)" stroke-width="1" />
      {/if}

      <!-- Antenna -->
      <line x1="60" y1="20" x2="60" y2="10" stroke="#b8b8c8" stroke-width="1.5" />
      <circle cx="60" cy="8" r="3" fill="var(--neon-blue, #00d4ff)" class="antenna-glow" filter="url(#neonGlow)" />
    </g>

    <!-- Helmet glow overlay -->
    <ellipse cx="60" cy="48" rx="20" ry="22" fill="url(#helmetGlow)" />
  </svg>

  <p class="mascot-label" aria-live="polite">
    {#if mouthState === "surprised"}
      Whoa! 👋
    {:else if mouthState === "happy"}
      Hello! ✨
    {:else}
      I see you~ 👀
    {/if}
  </p>
</div>

<style>
  .mascot-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.5rem;
    cursor: pointer;
    user-select: none;
    transition: transform 0.3s ease;
  }

  .mascot-container:hover {
    transform: scale(1.05);
  }

  .mascot-svg {
    width: 100px;
    height: 130px;
    filter: drop-shadow(0 0 15px rgba(0, 212, 255, 0.2));
  }

  .mascot-label {
    font-family: 'Space Mono', monospace;
    font-size: 0.75rem;
    color: var(--neon-blue, #00d4ff);
    text-shadow: 0 0 8px rgba(0, 212, 255, 0.4);
    text-align: center;
    opacity: 0.8;
    transition: opacity 0.3s ease;
    min-height: 1.2em;
  }

  .mascot-container:hover .mascot-label {
    opacity: 1;
  }

  /* Antenna glow pulse */
  .antenna-glow {
    animation: antennaPulse 2s ease-in-out infinite;
  }

  @keyframes antennaPulse {
    0%, 100% { opacity: 0.6; transform: scale(1); }
    50% { opacity: 1; transform: scale(1.33); }
  }

  /* Chest panel lights */
  .blink-light {
    animation: lightBlink 1.5s ease-in-out infinite;
  }

  .blink-light-2 {
    animation: lightBlink 2s ease-in-out infinite 0.5s;
  }

  @keyframes lightBlink {
    0%, 100% { opacity: 0.4; }
    50% { opacity: 1; }
  }

  /* Eye blink */
  .eyes.blink ellipse,
  .eyes.blink circle {
    transform: scaleY(0.1);
    transform-origin: center;
    transition: transform 0.1s ease;
  }

  .eyes:not(.blink) ellipse,
  .eyes:not(.blink) circle {
    transition: transform 0.1s ease;
  }

  /* Wave animation */
  .left-arm {
    transform-origin: 40px 82px;
    transition: transform 0.15s ease;
  }

  .left-arm.wave-anim {
    animation: waveHand 0.8s ease-in-out;
  }

  @keyframes waveHand {
    0%, 100% { transform: rotate(0deg); }
    25% { transform: rotate(-30deg); }
    50% { transform: rotate(-15deg); }
    75% { transform: rotate(-35deg); }
  }

  /* Idle floating */
  .body-group {
    animation: mascotFloat 4s ease-in-out infinite;
    transform-origin: center;
  }

  @keyframes mascotFloat {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-4px); }
  }

  .helmet-group {
    animation: mascotFloat 4s ease-in-out infinite;
    transform-origin: center;
  }
</style>
