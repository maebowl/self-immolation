<script lang="ts">
  import Timeline from './Timeline.svelte';

  interface Step {
    type: 'text' | 'image';
    content: string;
  }

  interface Stop {
    name: string;
    date: string;
    year: number;
    steps: Step[];
  }

  interface Props {
    onBack?: () => void;
    active: boolean;
  }
  let { onBack, active }: Props = $props();

  const stops: Stop[] = [
    {
      name: 'Thích Quảng Đức',
      date: '1963-06-11',
      year: 1963,
      steps: [
        {
          type: 'text',
          content: "we'll start with the most famous act of self-immolation, ever.",
        },
        {
          type: 'text',
          content: "Thích Quảng Đức was a Buddhist monk living in South Vietnam. South Vietnam was not a good place to be a Buddhist, as the area was under the rule of Ngô Đình Diệm, a Catholic man with extremely anti-Buddhist values. Buddhist discontent rose sharply after the Buddhist flag was banned from being flown on Vesak, the birthday of Gautama Buddha.",
        },
        {
          type: 'text',
          content: "The Buddhists were at a loss for what to do about the situation. When a large group attempted a peaceful protest by flying the flag against the ban and marching on the broadcasting station, government forces fired into the crowd, killing nine people. The US government continued to sponsor the Diệm regime.",
        },
        {
          type: 'text',
          content: "Having exhausted all other options, on the morning of June 11th, 1963, Quảng Đức followed through with his last resort, lighting himself on fire and ultimately creating quite possibly the most important, and emotional, photo of all time.",
        },
        {
          type: 'image',
          content: '/thich-quang-duc.jpg',
        },
      ],
    },
    {
      name: 'Aaron Bushnell',
      date: '2024-02-25',
      year: 2024,
      steps: [
        { type: 'text', content: 'Story content goes here.' },
      ],
    },
    {
      name: 'Jan Palach',
      date: '1969-01-16',
      year: 1969,
      steps: [
        { type: 'text', content: 'Story content goes here.' },
      ],
    },
    {
      name: 'Mohammad Asif Javed Jutt',
      date: '2025-02-25',
      year: 2025,
      steps: [
        { type: 'text', content: 'Story content goes here.' },
      ],
    },
  ];

  let activeIndex = $state(0);
  let stepIndex = $state(0);
  let transitioning = $state(false);
  let entered = $state(false);
  let exiting = $state(false);

  $effect(() => {
    if (active && !entered) {
      setTimeout(() => { entered = true; }, 1000);
    }
    if (!active) {
      entered = false;
    }
  });

  function next() {
    if (!active || transitioning || exiting) return;
    const currentStop = stops[activeIndex];

    // If there are more steps in this stop, advance the step
    if (stepIndex < currentStop.steps.length - 1) {
      stepIndex++;
      return;
    }

    // Otherwise move to the next stop
    if (activeIndex < stops.length - 1) {
      transitioning = true;
      activeIndex++;
      stepIndex = 0;
      setTimeout(() => { transitioning = false; }, 1400);
    }
  }

  function prev() {
    if (!active || transitioning || exiting) return;

    // If we can go back a step within this stop
    if (stepIndex > 0) {
      stepIndex--;
      return;
    }

    // If we're at the first stop, exit back to title
    if (activeIndex === 0) {
      exiting = true;
      setTimeout(() => {
        exiting = false;
        onBack?.();
      }, 1100);
      return;
    }

    // Go to previous stop, last step
    transitioning = true;
    activeIndex--;
    stepIndex = stops[activeIndex].steps.length - 1;
    setTimeout(() => { transitioning = false; }, 1400);
  }

  function handleKeydown(e: KeyboardEvent) {
    if (!active) return;
    if (e.key === 'ArrowRight') next();
    if (e.key === 'ArrowLeft') prev();
  }
</script>

<svelte:window onkeydown={handleKeydown} />

<!-- svelte-ignore a11y_click_events_have_key_events -->
<!-- svelte-ignore a11y_no_static_element_interactions -->
<section class="narrative" class:active onclick={next}>
  {#each stops as stop, i}
    <div
      class="stop-header"
      class:visible={i === activeIndex && !transitioning && active && !exiting}
      class:raised={i === activeIndex && stepIndex > 0}
      class:entering={!entered || exiting}
    >
      <p class="date">{stop.date}</p>
      <h2>{stop.name}</h2>
    </div>
    {#each stop.steps as step, j}
      <div
        class="stop-content"
        class:visible={i === activeIndex && j === stepIndex && !transitioning && active && !exiting}
        class:entering={!entered || exiting}
        class:has-header={true}
        class:header-raised={stepIndex > 0}
        class:image-step={step.type === 'image'}
      >
        {#if step.type === 'text'}
          <p class="prose">{step.content}</p>
        {:else if step.type === 'image'}
          <img src={step.content} alt={stop.name} />
        {/if}
      </div>
    {/each}
  {/each}
</section>

<Timeline stops={stops.map(s => ({ name: s.name, date: s.date, year: s.year }))} {activeIndex} mode="narrative" active={active && !exiting} />

<style>
  .narrative {
    position: fixed;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    padding-bottom: 300px;
    cursor: pointer;
    user-select: none;
    pointer-events: none;
    z-index: 40;
  }

  .narrative.active {
    pointer-events: auto;
  }

  .stop-header {
    position: absolute;
    text-align: center;
    opacity: 0;
    transform: translateY(0);
    transition: opacity 0.5s ease, transform 0.6s ease;
    pointer-events: none;
  }

  .stop-header.visible {
    opacity: 1;
  }

  .stop-header.raised {
    transform: translateY(-25vh);
  }

  .stop-content {
    position: absolute;
    max-width: 650px;
    padding: 2rem;
    text-align: center;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.2s ease, transform 0.3s ease;
    pointer-events: none;
  }

  .stop-content.visible {
    opacity: 1;
    transform: translateY(5rem);
    pointer-events: auto;
    transition: opacity 0.5s ease 0.3s, transform 0.5s ease 0.3s;
  }

  .stop-content.has-header.header-raised.visible {
    transform: translateY(2rem);
  }

  /* First entrance: fly in from bottom */
  .stop-header.entering:not(.visible) {
    transform: translateY(100vh);
    transition: opacity 1s ease 0.6s, transform 1s cubic-bezier(0.25, 0.1, 0.25, 1) 0.6s;
  }

  .stop-header.entering.visible {
    transition: opacity 1s ease 0.6s, transform 1s cubic-bezier(0.25, 0.1, 0.25, 1) 0.6s;
  }

  .stop-content.entering:not(.visible) {
    transform: translateY(100vh);
    transition: opacity 1s ease 0.6s, transform 1s cubic-bezier(0.25, 0.1, 0.25, 1) 0.6s;
  }

  .stop-content.entering.visible {
    transition: opacity 1s ease 0.6s, transform 1s cubic-bezier(0.25, 0.1, 0.25, 1) 0.6s;
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
    text-wrap: pretty;
  }

  .image-step {
    max-width: 800px;
  }

  .image-step img {
    width: 100%;
    height: auto;
    object-fit: contain;
    max-height: 60vh;
  }
</style>
