<script lang="ts">
  import ContentWarning from '$lib/components/ContentWarning.svelte';
  import AreYouSure from '$lib/components/AreYouSure.svelte';
  import Waves from '$lib/components/Waves.svelte';
  import TitleScreen from '$lib/components/TitleScreen.svelte';
  import Narrative from '$lib/components/Narrative.svelte';

  type Screen = 'warning' | 'confirm' | 'waves' | 'essay';
  let screen = $state<Screen>('warning');
  let showNarrative = $state(false);
</script>

{#if screen === 'warning'}
  <ContentWarning onAccept={() => screen = 'confirm'} />
{:else if screen === 'confirm'}
  <AreYouSure onYes={() => screen = 'essay'} onNo={() => screen = 'waves'} />
{:else if screen === 'waves'}
  <Waves />
{:else}
  <TitleScreen
    active={!showNarrative}
    onComplete={() => showNarrative = true}
  />
  <Narrative active={showNarrative} onBack={() => showNarrative = false} />
{/if}
