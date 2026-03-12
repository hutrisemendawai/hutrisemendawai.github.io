<script>
  import { onMount } from 'svelte';

  let canvas;
  let ctx;
  let animationId;
  let particles = [];
  let w, h;
  let mouse = { x: -1000, y: -1000, radius: 150 };
  let lastScrollY;

  const colors = ['#ffffff', '#00f3ff', '#9d4edd', '#ffd60a', '#00ff88'];

  class Particle {
    constructor() {
      this.x = Math.random() * w;
      this.y = Math.random() * h;
      this.vx = 0;
      this.vy = 0;
      this.size = Math.random() * 2 + 0.5;
      this.color = colors[Math.floor(Math.random() * colors.length)];
      this.z = Math.random() * 0.8 + 0.2; // Depth multiplier for parallax and speed
      
      this.driftX = (Math.random() - 0.5) * 0.5 * this.z;
      this.driftY = (Math.random() - 0.5) * 0.5 * this.z;
      // Heavier particles have slightly more friction
      this.friction = 0.92 + Math.random() * 0.06;
      this.repelForce = (Math.random() * 0.5 + 0.5) * this.z;
    }

    draw() {
      ctx.fillStyle = this.color;
      ctx.globalAlpha = Math.min(1, this.size / 2.5 + 0.1);
      ctx.beginPath();
      ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
      ctx.closePath();
      ctx.fill();
    }

    update(scrollDelta) {
      // Apply momentum based on scroll
      this.vy -= scrollDelta * this.z * 0.8;

      let dx = mouse.x - this.x;
      let dy = mouse.y - this.y;
      let distance = Math.hypot(dx, dy);

      // Mouse repel logic
      if (distance < mouse.radius) {
        let force = (mouse.radius - distance) / mouse.radius;
        // Accelerate away from mouse
        this.vx -= (dx / distance) * force * this.repelForce;
        this.vy -= (dy / distance) * force * this.repelForce;
      }

      // Add simple continuous drift
      this.vx += (this.driftX - this.vx) * 0.05;
      this.vy += (this.driftY - this.vy) * 0.05;

      // Friction
      this.vx *= this.friction;
      this.vy *= this.friction;

      this.x += this.vx;
      this.y += this.vy;

      // Wrap around screen boundaries cleanly
      if (this.x > w + 20) this.x = -20;
      if (this.x < -20) this.x = w + 20;
      if (this.y > h + 20) this.y = -20;
      if (this.y < -20) this.y = h + 20;
    }
  }

  function init() {
    w = canvas.width = window.innerWidth;
    h = canvas.height = window.innerHeight;
    
    // Balanced density of particles
    let numberOfParticles = Math.floor((w * h) / 1800); 
    // Cap at memory/performance limit
    numberOfParticles = Math.min(numberOfParticles, 2500);

    particles = [];
    for (let i = 0; i < numberOfParticles; i++) {
        particles.push(new Particle());
    }
  }

  function animate() {
    ctx.clearRect(0, 0, w, h);
    ctx.globalCompositeOperation = 'screen';
    
    let scrollY = window.scrollY;
    let scrollDelta = 0;
    if (lastScrollY !== undefined) {
      scrollDelta = scrollY - lastScrollY;
    }
    lastScrollY = scrollY;

    for (let i = 0; i < particles.length; i++) {
      particles[i].update(scrollDelta);
      particles[i].draw();
    }
    animationId = requestAnimationFrame(animate);
  }

  function handleResize() {
    init();
  }

  function handleMouseMove(e) {
    mouse.x = e.clientX;
    mouse.y = e.clientY;
  }

  function handleMouseLeave() {
    mouse.x = -1000;
    mouse.y = -1000;
  }

  onMount(() => {
    ctx = canvas.getContext('2d');
    init();
    lastScrollY = window.scrollY;
    animate();

    window.addEventListener('resize', handleResize);
    window.addEventListener('mousemove', handleMouseMove);
    window.addEventListener('mouseout', handleMouseLeave);

    return () => {
      cancelAnimationFrame(animationId);
      window.removeEventListener('resize', handleResize);
      window.removeEventListener('mousemove', handleMouseMove);
      window.removeEventListener('mouseout', handleMouseLeave);
    };
  });
</script>

<canvas bind:this={canvas}></canvas>

<style>
  canvas {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    z-index: -1;
    pointer-events: none;
    background: radial-gradient(circle at center, #1b1b2f 0%, #050510 100%);
  }
</style>
