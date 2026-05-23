<script lang="ts">
  interface Stop {
    name: string;
    date: string;
    year: number;
  }

  interface Props {
    stops: Stop[];
    activeIndex: number;
    mode: 'narrative' | 'explorer';
    active: boolean;
  }

  let { stops, activeIndex, mode, active }: Props = $props();

  // Timeline spans much wider than the viewport — the camera pans across it
  const startYear = 1960;
  const endYear = 2026;
  const range = endYear - startYear;

  // Each year takes up this many pixels — makes the timeline physically large
  const pxPerYear = 140;
  const totalWidth = range * pxPerYear;

  function yearToX(year: number): number {
    return ((year - startYear) / range) * totalWidth;
  }

  // The camera offset: translate the track so the active dot is centered on screen
  // This is the core "moving through time" effect
  function getOffset(): number {
    if (!stops[activeIndex]) return 0;
    const x = yearToX(stops[activeIndex].year);
    return -x;
  }

  // Decade markers for visual scale
  const decades = Array.from(
    { length: Math.floor(range / 10) + 1 },
    (_, i) => startYear + i * 10
  );
</script>

<div class="timeline" class:explorer={mode === 'explorer'} class:active>
  <div
    class="track"
    style="width: {totalWidth}px; transform: translateX(calc(50vw + {getOffset()}px))"
  >
    <!-- The main line -->
    <div class="line"></div>

    <!-- Decade tick marks for orientation -->
    {#each decades as decade}
      <div class="tick" style="left: {yearToX(decade)}px">
        <div class="tick-mark"></div>
        <span class="tick-label">{decade}</span>
      </div>
    {/each}

    <!-- Stop markers -->
    {#each stops as stop, i}
      <div
        class="stop"
        class:active={i === activeIndex}
        class:visited={i < activeIndex}
        style="left: {yearToX(stop.year)}px"
      >
        <div class="dot"></div>
        <span class="label">{stop.name}</span>
      </div>
    {/each}
  </div>
</div>

<style>
  .timeline {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100vw;
    height: 300px;
    z-index: 100;
    overflow: visible;
    display: flex;
    align-items: center;
    pointer-events: none;
    transform: translateY(100%);
    opacity: 0;
    transition: transform 1s cubic-bezier(0.25, 0.1, 0.25, 1),
                opacity 0.8s ease;
  }

  .timeline.active {
    transform: translateY(0);
    opacity: 1;
    transition: transform 1s cubic-bezier(0.25, 0.1, 0.25, 1) 0.6s,
                opacity 0.8s ease 0.6s;
  }

  .track {
    position: relative;
    height: 100%;
    display: flex;
    align-items: center;
    overflow: visible;
    transition: transform 1.8s cubic-bezier(0.45, 0, 0.15, 1);
  }

  .line {
    position: absolute;
    top: 50%;
    left: 0;
    width: 9999vw;
    margin-left: -4999vw;
    height: 3px;
    background: var(--color-muted);
    opacity: 0.2;
  }

  /* Decade ticks */
  .tick {
    position: absolute;
    top: 50%;
    transform: translateX(-50%);
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .tick-mark {
    width: 1px;
    height: 20px;
    background: var(--color-muted);
    opacity: 0.2;
    transform: translateY(-50%);
  }

  .tick-label {
    position: absolute;
    top: 30px;
    font-size: 0.85rem;
    font-weight: var(--weight-medium);
    color: var(--color-muted);
    opacity: 0.3;
    letter-spacing: 0.05em;
    white-space: nowrap;
  }

  /* Stop dots */
  .stop {
    position: absolute;
    top: 50%;
    transform: translateX(-50%);
  }

  .dot {
    width: 24px;
    height: 24px;
    border-radius: 50%;
    background: var(--color-muted);
    opacity: 0.4;
    transition: background 0.5s ease, transform 0.5s ease, opacity 0.5s ease;
    transform: translateY(-42%);
  }

  .stop.active .dot {
    background: var(--color-accent);
    transform: translateY(-42%) scale(1.6);
    opacity: 1;
  }

  .stop.visited .dot {
    background: var(--color-muted);
    opacity: 0.4;
  }

  .label {
    position: absolute;
    bottom: 50px;
    left: 50%;
    transform: translateX(-50%);
    font-size: 1.1rem;
    font-weight: var(--weight-semibold);
    color: var(--color-muted);
    letter-spacing: 0.03em;
    opacity: 0;
    transition: opacity 0.5s ease, color 0.5s ease;
    white-space: nowrap;
  }

  .stop.active .label {
    opacity: 1;
    color: var(--color-accent);
  }
</style>
