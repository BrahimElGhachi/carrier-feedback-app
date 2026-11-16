<script>
  import { onMount } from 'svelte'

  /**
   * @typedef {{
   *   carrierName: string,
   *   category: string,
   *   mbrPeriod: string,
   *   description: string
   * }} FeedbackSummary
   */

  /**
   * @type {{
   *   feedback: FeedbackSummary,
   *   onConfirm: () => void,
   *   onCancel: () => void
   * }}
   */
  let { feedback, onConfirm, onCancel } = $props()
  /** @type {HTMLDialogElement | null} */
  let dialogElement = null

  function openDialog() {
    if (!dialogElement) return

    if (typeof dialogElement.showModal === 'function') {
      dialogElement.showModal()
      return
    }

    dialogElement.setAttribute('open', 'open')
  }

  function closeDialog() {
    if (!dialogElement) return

    if (typeof dialogElement.close === 'function') {
      dialogElement.close()
      return
    }

    dialogElement.removeAttribute('open')
  }

  onMount(() => openDialog())

  function closeOnBackdrop(event) {
    if (event.target === event.currentTarget) cancel()
  }

  function cancel() {
    closeDialog()
    onCancel()
  }

  function handleCancel(event) {
    event.preventDefault()
    cancel()
  }

  function confirm() {
    closeDialog()
    onConfirm()
  }
</script>

<dialog
  bind:this={dialogElement}
  class="delete-modal"
  aria-labelledby="delete-feedback-title"
  aria-describedby="delete-feedback-warning"
  onclick={closeOnBackdrop}
  oncancel={handleCancel}
>
  <section class="confirmation-card">
    <div class="confirmation-heading">
      <span class="delete-icon" aria-hidden="true">
        <svg viewBox="0 0 24 24">
          <path d="M4 7h16M10 11v6m4-6v6M6 7l1 14h10l1-14M9 7V4h6v3" />
        </svg>
      </span>
      <div>
        <h2 id="delete-feedback-title">Supprimer ce feedback ?</h2>
        <p id="delete-feedback-warning">
          Cette action est définitive : le feedback ne pourra pas être récupéré.
        </p>
      </div>
    </div>

    <div class="feedback-summary">
      <p class="summary-title">
        {feedback.carrierName} · {feedback.category} · MBR {feedback.mbrPeriod}
      </p>
      <p class="summary-description">{feedback.description}</p>
    </div>

    <footer class="confirmation-actions">
      <button class="button button-secondary" type="button" onclick={cancel}>
        Annuler
      </button>
      <button class="button button-danger" type="button" onclick={confirm}>
        Supprimer
      </button>
    </footer>
  </section>
</dialog>

<style>
  .delete-modal {
    width: min(440px, calc(100% - 32px));
    max-width: none;
    max-height: calc(100% - 32px);
    padding: 0;
    overflow: auto;
    color: #17283f;
    border: 0;
    border-radius: 16px;
    background: #fff;
    box-shadow: 0 24px 64px rgb(12 30 48 / 24%);
  }

  .delete-modal::backdrop {
    background: rgb(24 39 57 / 58%);
    backdrop-filter: blur(2px);
  }

  .confirmation-card {
    padding: 28px;
  }

  .confirmation-heading {
    display: grid;
    grid-template-columns: 44px 1fr;
    align-items: start;
    gap: 16px;
  }

  .delete-icon {
    display: grid;
    width: 44px;
    height: 44px;
    place-items: center;
    color: #b9271e;
    border-radius: 50%;
    background: #fde9e7;
  }

  .delete-icon svg {
    width: 22px;
    fill: none;
    stroke: currentColor;
    stroke-linecap: round;
    stroke-linejoin: round;
    stroke-width: 1.8;
  }

  h2 {
    margin: 0 0 8px;
    font-size: 20px;
    line-height: 1.35;
  }

  .confirmation-heading p,
  .summary-description {
    margin: 0;
    color: #4e6076;
    font-size: 15px;
    line-height: 1.5;
  }

  .feedback-summary {
    margin-top: 20px;
    padding: 14px 16px;
    border-radius: 12px;
    background: #f3f5f7;
  }

  .summary-title {
    margin: 0 0 6px;
    font-size: 15px;
    font-weight: 700;
  }

  .summary-description {
    overflow-wrap: anywhere;
  }

  .confirmation-actions {
    display: flex;
    justify-content: flex-end;
    gap: 12px;
    margin-top: 20px;
  }

  .button {
    min-height: 48px;
    padding: 0 20px;
    border: 1px solid transparent;
    border-radius: 10px;
    font: inherit;
    font-weight: 700;
    cursor: pointer;
  }

  .button:focus-visible {
    outline: 3px solid rgb(13 112 139 / 30%);
    outline-offset: 2px;
  }

  .button-secondary {
    color: #17283f;
    border-color: #bdc9d6;
    background: #fff;
  }

  .button-danger {
    color: #fff;
    background: #bf271e;
  }

  .button-danger:hover {
    background: #9f2019;
  }

  @media (max-width: 440px) {
    .confirmation-card {
      padding: 22px;
    }

    .confirmation-actions {
      flex-direction: column-reverse;
    }

    .button {
      width: 100%;
    }
  }
</style>
