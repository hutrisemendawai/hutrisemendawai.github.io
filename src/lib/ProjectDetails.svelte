<script>
  import { onMount, createEventDispatcher } from "svelte";
  import gsap from "gsap";

  export let project;
  
  const dispatch = createEventDispatcher();
  
  let modalRef;
  let overlayRef;
  let contentRef;
  
  onMount(() => {
    // Disable body scroll
    document.body.style.overflow = 'hidden';

    const tl = gsap.timeline();
    
    tl.fromTo(overlayRef, 
      { opacity: 0 }, 
      { opacity: 1, duration: 0.4, ease: "power2.out" }
    )
    .fromTo(modalRef,
      { y: '100%', opacity: 0 },
      { y: '0%', opacity: 1, duration: 0.6, ease: "power4.out" },
      "<0.1"
    )
    .fromTo(contentRef.children,
      { y: 30, opacity: 0 },
      { y: 0, opacity: 1, duration: 0.4, stagger: 0.1, ease: "power3.out" },
      "<0.3"
    );

    return () => {
      document.body.style.overflow = '';
    };
  });

  function closeAndUnmount() {
    const tl = gsap.timeline({
      onComplete: () => {
        dispatch('close');
      }
    });

    tl.to(contentRef.children, { y: 20, opacity: 0, duration: 0.2, stagger: 0.05, ease: "power2.in" })
      .to(modalRef, { y: '100%', opacity: 0, duration: 0.4, ease: "power4.in" }, "<0.1")
      .to(overlayRef, { opacity: 0, duration: 0.3, ease: "power2.in" }, "<0.2");
  }
</script>

<div class="project-details-overlay" bind:this={overlayRef}>
  <div class="project-modal" bind:this={modalRef}>
    
    <div class="modal-header">
      <button class="close-btn interactive" on:click={closeAndUnmount}>
        <span class="close-icon">×</span>
        <span>CLOSE</span>
      </button>
    </div>

    <div class="modal-body container" bind:this={contentRef}>
      
      <div class="modal-title-wrapper">
        <h2 class="modal-title">{project.title}</h2>
        <div class="modal-category" style="color: {project.color}">{project.category}</div>
      </div>

      <div class="tech-stack">
        {#each project.tech as tech}
          <span class="tech-badge">{tech}</span>
        {/each}
      </div>

      <div class="modal-intro">
        {project.intro}
      </div>

      <div class="modal-content-list">
        {#each project.details as detail}
          {#if detail.type === 'text'}
            <p class="detail-text">{detail.content}</p>
          {:else if detail.type === 'image_placeholder'}
            <div class="placeholder-img-wrapper">
              <div class="placeholder-img">
                <span class="placeholder-label">{detail.content}</span>
                <span class="placeholder-sublabel">Image Placeholder</span>
              </div>
            </div>
          {/if}
        {/each}
      </div>
      
    </div>
  </div>
</div>

<style>
  .project-details-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background-color: rgba(10, 10, 10, 0.85);
    backdrop-filter: blur(10px);
    z-index: 9998; /* ensure it is under the custom cursor (9999) */
    display: flex;
    justify-content: center;
    align-items: flex-end;
  }

  .project-modal {
    width: 100%;
    height: 95vh;
    background-color: var(--monopo-black, #111);
    border-top-left-radius: 40px;
    border-top-right-radius: 40px;
    box-shadow: 0 -10px 50px rgba(0,0,0,0.5);
    overflow-y: auto;
    position: relative;
    border-top: 1px solid var(--border-light, #333);
  }

  .modal-header {
    position: sticky;
    top: 0;
    width: 100%;
    padding: 2rem 4rem;
    display: flex;
    justify-content: flex-end;
    background: linear-gradient(to bottom, rgba(17,17,17,1) 60%, rgba(17,17,17,0));
    z-index: 10;
  }

  .close-btn {
    background: none;
    border: 1px solid var(--border-light, #444);
    color: var(--text-main, #fff);
    border-radius: 50px;
    padding: 0.5rem 1.5rem 0.5rem 1rem;
    display: flex;
    align-items: center;
    gap: 0.5rem;
    cursor: pointer;
    font-family: 'Inter', sans-serif;
    font-size: 0.9rem;
    font-weight: 600;
    letter-spacing: 1px;
    transition: all 0.3s ease;
    background-color: var(--monopo-black, #111);
  }

  .close-btn:hover {
    background-color: var(--text-main, #fff);
    color: var(--monopo-black, #111);
  }

  .close-icon {
    font-size: 1.5rem;
    line-height: 1;
  }

  .modal-body {
    padding: 2rem 4rem 8rem 4rem;
    max-width: 1200px;
    margin: 0 auto;
  }

  .modal-title-wrapper {
    margin-bottom: 2rem;
  }

  .modal-title {
    font-family: 'Syne', sans-serif;
    font-size: clamp(3rem, 6vw, 6rem);
    line-height: 1.1;
    color: var(--text-main, #fff);
    margin-bottom: 0.5rem;
  }

  .modal-category {
    font-family: 'Inter', sans-serif;
    font-size: 1.2rem;
    text-transform: uppercase;
    letter-spacing: 2px;
    font-weight: 600;
  }

  .tech-stack {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
    margin-bottom: 3rem;
  }

  .tech-badge {
    font-family: 'Inter', sans-serif;
    font-size: 0.85rem;
    padding: 0.4rem 1rem;
    border-radius: 20px;
    border: 1px solid var(--border-light, #444);
    color: var(--text-muted, #aaa);
    text-transform: uppercase;
    letter-spacing: 1px;
  }

  .modal-intro {
    font-family: 'Inter', sans-serif;
    font-size: 1.5rem;
    line-height: 1.6;
    color: var(--text-main, #fff);
    margin-bottom: 4rem;
    max-width: 800px;
  }

  .modal-content-list {
    display: flex;
    flex-direction: column;
    gap: 3rem;
  }

  .detail-text {
    font-family: 'Inter', sans-serif;
    font-size: 1.1rem;
    line-height: 1.8;
    color: var(--text-muted, #aaa);
    max-width: 800px;
  }

  .placeholder-img-wrapper {
    width: 100%;
    margin-top: 1rem;
    margin-bottom: 2rem;
  }

  .placeholder-img {
    width: 100%;
    aspect-ratio: 16 / 9;
    background-color: #1a1a1a;
    border: 1px dashed #444;
    border-radius: 12px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 1rem;
    transition: background-color 0.4s ease;
  }

  .placeholder-img:hover {
    background-color: #222;
  }

  .placeholder-label {
    font-family: 'Syne', sans-serif;
    font-size: 2rem;
    color: #fff;
    font-weight: 600;
  }

  .placeholder-sublabel {
    font-family: 'Inter', sans-serif;
    font-size: 1rem;
    color: #666;
    text-transform: uppercase;
    letter-spacing: 2px;
  }

  @media (max-width: 900px) {
    .modal-body {
      padding: 0 2rem 6rem 2rem;
    }
    .modal-header {
      padding: 1.5rem 2rem;
    }
    .modal-title {
      font-size: 2.5rem;
    }
    .modal-intro {
      font-size: 1.2rem;
    }
  }
</style>
