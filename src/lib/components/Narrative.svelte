<script lang="ts">
  import Timeline from './Timeline.svelte';

  const stops = [
    { name: 'Thích Quảng Đức', date: '1963-06-11', year: 1963 },
    { name: 'Aaron Bushnell', date: '2024-02-25', year: 2024 },
    { name: 'Jan Palach', date: '1969-01-16', year: 1969 },
    { name: 'Mohammad Asif Javed Jutt', date: '2025-02-25', year: 2025 },
  ];

  let activeIndex = $state(0);
  let transitioning = $state(false);

  function next() {
    if (transitioning) return;
    if (activeIndex < stops.length - 1) {
      transitioning = true;
      activeIndex++;
      // Wait for the timeline pan to nearly finish before showing text
      setTimeout(() => { transitioning = false; }, 1400);
    }
  }

  function prev() {
    if (transitioning) return;
    if (activeIndex > 0) {
      transitioning = true;
      activeIndex--;
      setTimeout(() => { transitioning = false; }, 1400);
    }
  }

  function handleKeydown(e: KeyboardEvent) {
    if (e.key === 'ArrowRight') next();
    if (e.key === 'ArrowLeft') prev();
  }
</script>

<svelte:window onkeydown={handleKeydown} />

<!-- svelte-ignore a11y_click_events_have_key_events -->
<!-- svelte-ignore a11y_no_static_element_interactions -->
<section class="narrative" onclick={next}>
  {#each stops as stop, i}
    <div class="stop-content" class:active={i === activeIndex && !transitioning}>
      <p class="date">{stop.date}</p>
      <h2>{stop.name}</h2>
      <div class="prose">
        <p>Story content goes here.</p>
      </div>
    </div>
  {/each}
</section>

<Timeline {stops} {activeIndex} mode="narrative" />

<style>
  .narrative {
    position: relative;
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding-bottom: 300px;
    cursor: pointer;
    user-select: none;
  }

  .stop-content {
    position: absolute;
    max-width: 650px;
    padding: 2rem;
    text-align: center;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.5s ease, transform 0.5s ease;
    pointer-events: none;
  }

  .stop-content.active {
    opacity: 1;
    transform: translateY(0);
    pointer-events: auto;
  }

  .date {
    font-weight: var(--weight-medium);
    font-size: 0.85rem;
    color: var(--color-accent);
    letter-spacing: 0.1em;
    margin-bottom: 1rem;
  }

  h2 {
    font-weight: var(--weight-extrabold);
    font-size: 2.5rem;
    letter-spacing: -0.02em;
    margin-bottom: 1.5rem;
  }

  .prose {
    color: var(--color-muted);
    font-size: 1.1rem;
    line-height: 1.7;
  }
</style>
