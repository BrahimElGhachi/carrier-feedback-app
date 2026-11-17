<script>
  // Ce composant représente une ligne de feedback affichée sur l'accueil ou dans l'historique.
  // App.svelte lui transmet le feedback, les textes à afficher et le type de ligne via $props().
  // Les callbacks reçus servent à demander à App.svelte d'ouvrir la modification ou la suppression.
  let {
    feedback,
    display,
    carrierName,
    authorName,
    formattedDate,
    onEdit,
    onDelete,
  } = $props()
</script>

<div
  class="feedback-row"
  class:home-feedback-row={display === 'home'}
  class:history-feedback-row={display === 'history'}
>
  {#if display === 'history'}
    <strong class="history-carrier">{carrierName}</strong>
  {/if}
  <span
    class:communication={feedback.category === 'Communication'}
    class:punctuality={feedback.category === 'Ponctualité'}
    class:documentation={feedback.category === 'Documentation'}
    class="category-badge"
  >{feedback.category}</span>
  <div class="feedback-copy">
    <p>{feedback.description}</p>
    <span>{authorName}</span>
  </div>
  <time datetime={feedback.createdAt}>{formattedDate}</time>
  <div class="row-actions">
    <button
      class="icon-button"
      aria-label={`Modifier le feedback de ${carrierName}`}
      onclick={() => onEdit(feedback)}
    >
      <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m14 5 5 5M4 20l4.3-.9L19 8.4a2.1 2.1 0 0 0-3-3L5.3 16.1 4 20Z" /></svg>
    </button>
    <button
      class="icon-button delete-action"
      aria-label={`Supprimer le feedback de ${carrierName}`}
      onclick={() => onDelete(feedback)}
    >
      <svg viewBox="0 0 24 24" aria-hidden="true"><path d="M4 7h16M10 11v6m4-6v6M6 7l1 14h10l1-14M9 7V4h6v3" /></svg>
    </button>
  </div>
</div>

<style>
  .feedback-row {
    display: grid;
    min-width: 0;
    align-items: center;
    gap: 20px;
    padding: 16px 24px;
    border-bottom: 1px solid #e1e7ed;
  }

  .feedback-row:last-child {
    border-bottom: 0;
  }

  .home-feedback-row {
    grid-template-columns: 128px minmax(0, 1fr) 92px 68px;
  }

  .category-badge {
    justify-self: start;
    padding: 4px 12px;
    border-radius: 999px;
    font-size: 13px;
    font-weight: 600;
    white-space: nowrap;
  }

  .communication {
    color: #075e74;
    background: #e0f1f5;
  }

  .punctuality {
    color: #97500a;
    background: #ffead5;
  }

  .documentation {
    color: #5142a6;
    background: #e9e5fa;
  }

  .feedback-copy {
    min-width: 0;
  }

  .feedback-copy p {
    margin: 0;
    color: #4e6076;
    line-height: 1.4;
  }

  .feedback-copy span {
    display: block;
    margin-top: 2px;
    color: #5b6d82;
    font-size: 13px;
  }

  time {
    color: #5b6d82;
    font-size: 14px;
    white-space: nowrap;
  }

  .row-actions {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .icon-button {
    display: grid;
    width: 28px;
    height: 32px;
    place-items: center;
    color: #17283f;
    border: 0;
    border-radius: 6px;
    background: transparent;
    cursor: pointer;
  }

  .icon-button:hover {
    background: #f1f4f7;
  }

  .icon-button:focus-visible {
    outline: 3px solid rgb(13 112 139 / 30%);
    outline-offset: 2px;
  }

  .icon-button svg {
    width: 20px;
    fill: none;
    stroke: currentColor;
    stroke-linecap: round;
    stroke-linejoin: round;
    stroke-width: 1.7;
  }

  .delete-action {
    color: #bf271e;
  }

  .history-feedback-row {
    grid-template-columns: 110px 128px minmax(0, 1fr) 92px 68px;
    min-height: 76px;
  }

  .history-carrier {
    font-size: 15px;
  }

  @media (max-width: 760px) {
    .home-feedback-row,
    .history-feedback-row {
      grid-template-columns: minmax(0, 1fr) auto;
      gap: 10px 14px;
    }

    .category-badge {
      grid-column: 1;
    }

    .feedback-copy {
      grid-column: 1 / -1;
      grid-row: 2;
    }

    .feedback-row time {
      grid-column: 1;
      grid-row: 3;
    }

    .feedback-row .row-actions {
      grid-column: 2;
      grid-row: 1 / 4;
      gap: 8px;
    }

    .history-carrier {
      grid-column: 1;
    }
  }

  @media (max-width: 420px) {
    .feedback-row {
      padding: 14px 16px;
    }
  }
</style>
