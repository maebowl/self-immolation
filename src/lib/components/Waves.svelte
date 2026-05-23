<script lang="ts">
  import { onMount, onDestroy } from 'svelte';

  let audioCtx: AudioContext | null = null;

  function createWaves() {
    audioCtx = new AudioContext();

    // White noise source — the raw material for our ocean sound
    const bufferSize = 2 * audioCtx.sampleRate;
    const buffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);
    const data = buffer.getChannelData(0);
    for (let i = 0; i < bufferSize; i++) {
      data[i] = Math.random() * 2 - 1;
    }

    const noise = audioCtx.createBufferSource();
    noise.buffer = buffer;
    noise.loop = true;

    // Low-pass filter — removes harsh high frequencies, makes it sound like water
    const filter = audioCtx.createBiquadFilter();
    filter.type = 'lowpass';
    filter.frequency.value = 500;

    // LFO (low frequency oscillator) — creates the slow "wave" rhythm
    // This modulates the filter frequency so it swells and fades
    const lfo = audioCtx.createOscillator();
    const lfoGain = audioCtx.createGain();
    lfo.frequency.value = 0.1; // Very slow — one "wave" every ~10 seconds
    lfoGain.gain.value = 300;
    lfo.connect(lfoGain);
    lfoGain.connect(filter.frequency);
    lfo.start();

    // Master volume — keep it gentle
    const masterGain = audioCtx.createGain();
    masterGain.gain.value = 0.15;

    // Connect the chain: noise → filter → volume → speakers
    noise.connect(filter);
    filter.connect(masterGain);
    masterGain.connect(audioCtx.destination);
    noise.start();
  }

  onMount(() => {
    createWaves();
  });

  onDestroy(() => {
    if (audioCtx) {
      audioCtx.close();
    }
  });
</script>

<div class="waves">
  <p class="message">that's ok.</p>
  <p class="submessage">here's some waves</p>
</div>

<style>
  .waves {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    padding: 2rem;
    text-align: center;
  }

  .message {
    font-weight: var(--weight-bold);
    font-size: 2rem;
    margin-bottom: 0.5rem;
  }

  .submessage {
    color: var(--color-muted);
    font-size: 1.1rem;
  }
</style>
