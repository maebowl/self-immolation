<script lang="ts">
  interface Props {
    onAccept: () => void;
  }
  let { onAccept }: Props = $props();
</script>

<div class="warning">
  <h1>Content Warning</h1>
  <p>
    This essay contains detailed accounts of self-immolation as political protest.
    The content is presented respectfully, but it is distressing by nature.
  </p>
  <button onclick={onAccept}>I understand, continue</button>
</div>

<style>
  .warning {
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
    margin-bottom: 1.5rem;
  }

  p {
    color: var(--color-muted);
    font-size: 1.1rem;
    line-height: 1.7;
    margin-bottom: 2.5rem;
    text-wrap: pretty;
  }

  button {
    padding: 0.75rem 2rem;
    font-family: var(--font-body);
    font-weight: var(--weight-semibold);
    font-size: 0.9rem;
    letter-spacing: 0.05em;
    text-transform: uppercase;
    border: 1px solid var(--color-muted);
    color: var(--color-text);
    background: none;
    cursor: pointer;
    transition: border-color 0.2s, color 0.2s;
  }

  button:hover {
    border-color: var(--color-accent);
    color: var(--color-accent);
  }

  @media (max-width: 768px) {
    h1 {
      font-size: 1.8rem;
    }

    button {
      padding: 0.9rem 2rem;
      font-size: 0.85rem;
    }
  }
</style>
