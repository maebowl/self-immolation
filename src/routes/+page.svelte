<script lang="ts">
  import ContentWarning from '$lib/components/ContentWarning.svelte';
  import AreYouSure from '$lib/components/AreYouSure.svelte';
  import Waves from '$lib/components/Waves.svelte';
  import Narrative from '$lib/components/Narrative.svelte';
  import Explorer from '$lib/components/Explorer.svelte';

  type Screen = 'warning' | 'confirm' | 'waves' | 'content';
  let screen = $state<Screen>('warning');
</script>

{#if screen === 'warning'}
  <ContentWarning onAccept={() => screen = 'confirm'} />
{:else if screen === 'confirm'}
  <AreYouSure onYes={() => screen = 'content'} onNo={() => screen = 'waves'} />
{:else if screen === 'waves'}
  <Waves />
{:else}
  <main>
    <Narrative />
    <Explorer />
  </main>
{/if}
