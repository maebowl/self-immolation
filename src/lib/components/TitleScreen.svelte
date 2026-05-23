<script lang="ts">
  interface Props {
    onComplete: () => void;
    active: boolean;
  }
  let { onComplete, active }: Props = $props();

  type Phase = 'title' | 'definition' | 'focus' | 'bridge' | 'exit';
  let phase = $state<Phase>('title');
  let exitTimer: ReturnType<typeof setTimeout> | null = null;

  let prevActive = false;
  $effect(() => {
    // Only return to focus when active transitions from false → true
    if (active && !prevActive && phase === 'exit') {
      phase = 'focus';
    }
    prevActive = active;
  });

  function handleClick() {
    if (!active) return;
    if (phase === 'title') {
      phase = 'definition';
    } else if (phase === 'definition') {
      phase = 'focus';
    } else if (phase === 'focus') {
      phase = 'bridge';
    } else if (phase === 'bridge') {
      phase = 'exit';
      exitTimer = setTimeout(() => onComplete(), 50);
    }
  }

  function handleKeydown(e: KeyboardEvent) {
    if (!active) return;
    if (e.key === 'ArrowRight' || e.key === ' ') {
      handleClick();
    }
    if (e.key === 'ArrowLeft') {
      if (phase === 'definition') {
        phase = 'title';
      } else if (phase === 'focus') {
        phase = 'definition';
      } else if (phase === 'bridge') {
        phase = 'focus';
      } else if (phase === 'exit') {
        if (exitTimer) clearTimeout(exitTimer);
        phase = 'focus';
      }
    }
  }
</script>

<svelte:window onkeydown={handleKeydown} />

<!-- svelte-ignore a11y_click_events_have_key_events -->
<!-- svelte-ignore a11y_no_static_element_interactions -->
<div
  class="title-screen"
  class:exit={phase === 'exit' || !active}
  class:inactive={!active}
  onclick={handleClick}
>
  <div class="content">
    <h1 class:hidden={phase === 'focus' || phase === 'bridge' || phase === 'exit'} class:entering={phase === 'title'}>self-immolation</h1>
    <div class="definition" class:visible={phase !== 'title'} class:focused={phase === 'focus' || phase === 'bridge' || phase === 'exit'} class:raised={phase === 'bridge' || phase === 'exit'}>
      <p class="pronunciation" class:hidden={phase === 'focus' || phase === 'bridge' || phase === 'exit'}>/ ˌself ˌɪməˈleɪʃən /</p>
      <p class="part-of-speech" class:hidden={phase === 'focus' || phase === 'bridge' || phase === 'exit'}>noun</p>
      <p class="meaning">the act of setting oneself on fire.</p>
    </div>
    <p class="bridge" class:visible={phase === 'bridge' || phase === 'exit'}>
      self-immolation is the last resort.<br />
      when a death says more than a life can, something is broken.
    </p>
  </div>
</div>

<style>
  .title-screen {
    position: fixed;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 50;
    cursor: pointer;
    user-select: none;
    background: var(--color-bg);
  }

  .title-screen.inactive {
    pointer-events: none;
    z-index: -1;
  }

  .content {
    text-align: center;
    transition: transform 0.8s cubic-bezier(0.45, 0, 0.15, 1),
                opacity 0.6s ease;
  }

  .exit .content {
    transform: translateY(-100vh);
    opacity: 0;
  }

  h1 {
    font-weight: var(--weight-extrabold);
    font-size: 4rem;
    letter-spacing: -0.03em;
    text-transform: lowercase;
    transition: opacity 0.5s ease, max-height 0.5s ease, margin 0.5s ease;
    max-height: 5rem;
    overflow: hidden;
  }

  h1.hidden {
    opacity: 0;
    max-height: 0;
    margin: 0;
  }

  h1.entering {
    animation: titleFadeIn 1s ease both;
  }

  @keyframes titleFadeIn {
    from {
      opacity: 0;
      transform: translateY(15px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  .definition {
    margin-top: 0.75rem;
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.5s ease, transform 0.5s ease, margin 0.5s ease;
  }

  .definition.visible {
    opacity: 1;
    transform: translateY(0);
    transition: opacity 0.5s ease, transform 0.6s ease, margin 0.5s ease;
  }

  .definition.focused {
    margin-top: 0;
  }

  .pronunciation,
  .part-of-speech {
    transition: opacity 0.5s ease, max-height 0.5s ease, margin 0.5s ease;
    max-height: 3rem;
    overflow: hidden;
  }

  .pronunciation.hidden,
  .part-of-speech.hidden {
    opacity: 0;
    max-height: 0;
    margin: 0;
  }

  .pronunciation {
    color: var(--color-muted);
    font-size: 1rem;
    letter-spacing: 0.05em;
    margin-bottom: 0.5rem;
  }

  .part-of-speech {
    color: var(--color-accent);
    font-weight: var(--weight-semibold);
    font-size: 0.85rem;
    font-style: italic;
    letter-spacing: 0.05em;
    margin-bottom: 0.75rem;
  }

  .meaning {
    color: var(--color-text);
    font-weight: var(--weight-normal);
    font-size: 1.2rem;
  }

  .definition.raised {
    transform: translateY(-2rem);
  }

  .bridge {
    color: var(--color-muted);
    font-size: 1.1rem;
    line-height: 1.7;
    opacity: 0;
    transform: translateY(10px);
    transition: opacity 0.6s ease, transform 0.6s ease;
    text-wrap: pretty;
  }

  .bridge.visible {
    opacity: 1;
    transform: translateY(0);
  }

  @media (max-width: 768px) {
    h1 {
      font-size: 2.5rem;
      max-height: 3.5rem;
    }

    .meaning {
      font-size: 1rem;
    }

    .bridge {
      font-size: 0.95rem;
      padding: 0 1rem;
    }

    .definition.raised {
      transform: translateY(-1rem);
    }
  }
</style>
