<script lang="ts">
  interface Props {
    onYes: () => void;
    onNo: () => void;
  }
  let { onYes, onNo }: Props = $props();
</script>

<div class="confirm">
  <h1>are you sure?</h1>
  <p>some of the following imagery is inherently graphic.</p>
  <div class="buttons">
    <button class="no" onclick={onNo}>no</button>
    <button class="yes" onclick={onYes}>yes</button>
  </div>
</div>

<style>
  .confirm {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    padding: 2rem;
    text-align: center;
    max-width: 600px;
    margin: 0 auto;
  }

  h1 {
    font-weight: var(--weight-extrabold);
    font-size: 2.5rem;
    letter-spacing: -0.02em;
    margin-bottom: 1rem;
    text-transform: lowercase;
  }

  p {
    color: var(--color-muted);
    font-size: 1.1rem;
    line-height: 1.7;
    margin-bottom: 2.5rem;
  }

  .buttons {
    display: flex;
    gap: 2rem;
  }

  button {
    padding: 0.75rem 2rem;
    font-family: var(--font-body);
    font-weight: var(--weight-semibold);
    font-size: 0.9rem;
    letter-spacing: 0.05em;
    text-transform: lowercase;
    border: 1px solid var(--color-muted);
    color: var(--color-text);
    background: none;
    cursor: pointer;
    transition: border-color 0.2s, color 0.2s;
  }

  button.yes:hover {
    border-color: var(--color-accent);
    color: var(--color-accent);
  }

  button.no:hover {
    border-color: var(--color-text);
    color: var(--color-text);
  }
</style>
