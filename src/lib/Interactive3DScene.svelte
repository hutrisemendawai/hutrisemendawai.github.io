<script>
  import { onMount } from "svelte";

  let sceneEl;
  let cubeEl;
  let rotateX = -20;
  let rotateY = 30;
  let autoRotate = true;
  let animId;
  let autoTimer;

  function handleMouseMove(e) {
    if (!sceneEl) return;
    const rect = sceneEl.getBoundingClientRect();
    const centerX = rect.left + rect.width / 2;
    const centerY = rect.top + rect.height / 2;

    const dx = (e.clientX - centerX) / rect.width;
    const dy = (e.clientY - centerY) / rect.height;

    rotateX = -20 + dy * -40;
    rotateY = 30 + dx * 40;
    autoRotate = false;

    // Resume auto-rotate after inactivity
    clearTimeout(autoTimer);
    autoTimer = setTimeout(() => {
      autoRotate = true;
    }, 3000);
  }

  function handleMouseLeave() {
    autoRotate = true;
  }

  onMount(() => {
    let angle = 30;
    function tick() {
      if (autoRotate) {
        angle += 0.3;
        rotateY = angle;
        rotateX = -20 + Math.sin(angle * 0.02) * 10;
      }
      animId = requestAnimationFrame(tick);
    }
    tick();

    window.addEventListener("mousemove", handleMouseMove);

    return () => {
      cancelAnimationFrame(animId);
      window.removeEventListener("mousemove", handleMouseMove);
    };
  });
</script>

<div
  class="scene-3d"
  bind:this={sceneEl}
  on:mouseleave={handleMouseLeave}
  role="img"
  aria-label="Interactive 3D holographic cube"
>
  <div
    class="cube-wrapper"
    bind:this={cubeEl}
    style="transform: rotateX({rotateX}deg) rotateY({rotateY}deg);"
  >
    <!-- Cube faces -->
    <div class="cube-face front">
      <div class="face-content">
        <span class="face-icon">🚀</span>
        <span class="face-label">LAUNCH</span>
      </div>
    </div>
    <div class="cube-face back">
      <div class="face-content">
        <span class="face-icon">🌍</span>
        <span class="face-label">EARTH</span>
      </div>
    </div>
    <div class="cube-face right">
      <div class="face-content">
        <span class="face-icon">🛸</span>
        <span class="face-label">EXPLORE</span>
      </div>
    </div>
    <div class="cube-face left">
      <div class="face-content">
        <span class="face-icon">⭐</span>
        <span class="face-label">STARS</span>
      </div>
    </div>
    <div class="cube-face top">
      <div class="face-content">
        <span class="face-icon">🌙</span>
        <span class="face-label">MOON</span>
      </div>
    </div>
    <div class="cube-face bottom">
      <div class="face-content">
        <span class="face-icon">☄️</span>
        <span class="face-label">COMET</span>
      </div>
    </div>

    <!-- Inner glow core -->
    <div class="cube-core"></div>
  </div>

  <!-- Orbital rings -->
  <div class="orbit orbit-1"></div>
  <div class="orbit orbit-2"></div>

  <!-- Floating particles around cube -->
  <div class="scene-particle p1"></div>
  <div class="scene-particle p2"></div>
  <div class="scene-particle p3"></div>
  <div class="scene-particle p4"></div>
</div>

<style>
  .scene-3d {
    width: 200px;
    height: 200px;
    perspective: 600px;
    perspective-origin: 50% 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    cursor: grab;
  }

  .scene-3d:active {
    cursor: grabbing;
  }

  .cube-wrapper {
    width: 80px;
    height: 80px;
    position: relative;
    transform-style: preserve-3d;
    will-change: transform;
    transition: transform 0.1s linear;
  }

  .cube-face {
    position: absolute;
    width: 80px;
    height: 80px;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 1px solid rgba(0, 212, 255, 0.3);
    background: rgba(5, 5, 16, 0.7);
    backdrop-filter: blur(4px);
    -webkit-backdrop-filter: blur(4px);
    box-shadow: inset 0 0 20px rgba(0, 212, 255, 0.05);
  }

  .front  { transform: translateZ(40px); }
  .back   { transform: rotateY(180deg) translateZ(40px); }
  .right  { transform: rotateY(90deg) translateZ(40px); }
  .left   { transform: rotateY(-90deg) translateZ(40px); }
  .top    { transform: rotateX(90deg) translateZ(40px); }
  .bottom { transform: rotateX(-90deg) translateZ(40px); }

  .face-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
  }

  .face-icon {
    font-size: 1.5rem;
    filter: drop-shadow(0 0 8px rgba(255, 255, 255, 0.3));
  }

  .face-label {
    font-family: 'Space Mono', monospace;
    font-size: 0.55rem;
    color: var(--neon-blue, #00d4ff);
    letter-spacing: 2px;
    text-shadow: 0 0 6px rgba(0, 212, 255, 0.5);
  }

  /* Inner core glow */
  .cube-core {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 30px;
    height: 30px;
    transform: translate(-50%, -50%);
    background: radial-gradient(circle, rgba(0, 212, 255, 0.3) 0%, transparent 70%);
    border-radius: 50%;
    animation: coreGlow 2s ease-in-out infinite;
  }

  @keyframes coreGlow {
    0%, 100% { opacity: 0.5; transform: translate(-50%, -50%) scale(1); }
    50% { opacity: 1; transform: translate(-50%, -50%) scale(1.5); }
  }

  /* Orbital rings */
  .orbit {
    position: absolute;
    border: 1px solid rgba(0, 212, 255, 0.1);
    border-radius: 50%;
    pointer-events: none;
  }

  .orbit-1 {
    width: 160px;
    height: 160px;
    animation: orbitSpin 8s linear infinite;
    border-top-color: rgba(0, 212, 255, 0.3);
  }

  .orbit-2 {
    width: 180px;
    height: 180px;
    animation: orbitSpin 12s linear infinite reverse;
    border-right-color: rgba(139, 92, 246, 0.3);
    transform: rotateX(60deg);
  }

  @keyframes orbitSpin {
    from { transform: rotateZ(0deg); }
    to { transform: rotateZ(360deg); }
  }

  /* Floating particles */
  .scene-particle {
    position: absolute;
    width: 4px;
    height: 4px;
    border-radius: 50%;
    background: var(--neon-blue, #00d4ff);
    box-shadow: 0 0 8px var(--neon-blue, #00d4ff);
    animation: particleOrbit 6s linear infinite;
  }

  .p1 { animation-duration: 5s; animation-delay: 0s; }
  .p2 { animation-duration: 7s; animation-delay: -2s; }
  .p3 { animation-duration: 4s; animation-delay: -1s; background: var(--nebula-purple, #8b5cf6); box-shadow: 0 0 8px var(--nebula-purple, #8b5cf6); }
  .p4 { animation-duration: 6s; animation-delay: -3s; background: var(--star-gold, #ffd60a); box-shadow: 0 0 8px var(--star-gold, #ffd60a); }

  @keyframes particleOrbit {
    0%   { transform: rotate(0deg) translateX(90px) rotate(0deg); opacity: 0.8; }
    25%  { opacity: 1; }
    50%  { transform: rotate(180deg) translateX(90px) rotate(-180deg); opacity: 0.6; }
    75%  { opacity: 1; }
    100% { transform: rotate(360deg) translateX(90px) rotate(-360deg); opacity: 0.8; }
  }
</style>
