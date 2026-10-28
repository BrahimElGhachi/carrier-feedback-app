<script>
  import { onMount } from 'svelte'

  /**
   * @typedef {{ id: string | number, name: string }} CarrierOption
   * @typedef {{ id: string | number, fullName: string }} AuthorOption
   * @typedef {{
   *   carrierId?: string | number,
   *   authorId?: string | number,
   *   mbrPeriod?: string,
   *   category?: string,
   *   description?: string,
   *   createdAt?: string
   * }} FeedbackValue
   * @typedef {{
   *   carrierId: string | number,
   *   authorId: string | number,
   *   mbrPeriod: string,
   *   category: string,
   *   description: string
   * }} FeedbackSubmission
   */

  /**
   * @type {{
   *   mode?: 'create' | 'edit',
   *   feedback?: FeedbackValue | null,
   *   carriers?: CarrierOption[],
   *   authors?: AuthorOption[],
   *   initialPeriod?: string,
   *   onSave: (feedback: FeedbackSubmission) => void,
   *   onCancel: () => void
   * }}
   */

// Récupère dans "props" toutes les props transmises par App.svelte
// via les attributs de la balise <FeedbackForm>.
let props = $props()

  const categories = ['Communication', 'Ponctualité', 'Documentation', 'Suivi', 'Finance']

// Retourne le mois actuel au format "AAAA-MM" (ex : "2026-10").
// Cette valeur sert de période MBR par défaut lorsqu'aucune période n'est déjà définie.
  function currentMonth() {
    const now = new Date()
    return `${now.getFullYear()}-${String(now.getMonth() + 1).padStart(2, '0')}`
  }

// Ces variables $state représentent l'état actuel des champs du formulaire.
// Grâce à bind:value, elles sont automatiquement mises à jour lorsque
// l'utilisateur sélectionne une valeur ou saisit du texte.
  let carrierId = $state('')
  let authorId = $state('')
  let mbrPeriod = $state('')
  let category = $state('')
  let description = $state('')
  /** @type {HTMLDialogElement | null} */
// Variable qui contiendra une référence vers l'élément HTML <dialog>.
// Au départ elle vaut null.
// Lorsque Svelte crée le <dialog>, bind:this={dialogElement}
// place automatiquement une référence vers ce <dialog> dans la variable.
//
// onMount() appelle ensuite openDialog(), qui peut utiliser
// dialogElement.showModal() pour afficher la fenêtre modale.  
  let dialogElement = null

// Ouvre la fenêtre <dialog> du formulaire.
// Si dialogElement n'existe pas encore, la fonction s'arrête.
// Si la méthode showModal() est disponible, elle ouvre le dialog en mode modal.
// Sinon, on utilise l'attribut HTML "open" comme solution de repli.
  function openDialog() {
    if (!dialogElement) return

    if (typeof dialogElement.showModal === 'function') {
      dialogElement.showModal()
      return
    }

    dialogElement.setAttribute('open', 'open')
  }

  // Ferme la fenêtre <dialog> du formulaire.
// Si dialogElement n'existe pas, la fonction s'arrête.
// Si la méthode close() est disponible, elle ferme le dialog.
// Sinon, on retire directement l'attribut HTML "open".
  function closeDialog() {
    if (!dialogElement) return

    if (typeof dialogElement.close === 'function') {
      dialogElement.close()
      return
    }

    dialogElement.removeAttribute('open')
  }
  // Quand FeedbackForm est ajouté à la page, onMount s'exécute une seule fois.
// Exemple : formMode passe à 'create' → le {#if formMode} devient vrai
// → FeedbackForm est créé → onMount s'exécute → openDialog() est appelée
// → dialogElement.showModal() ouvre la fenêtre modale et le formulaire devient visible.
  onMount(() => openDialog())

  
// Ce $effect initialise les champs du formulaire.
// Il récupère d'abord le feedback transmis par App.svelte via props.feedback.
//
// En mode "edit", props.feedback contient le feedback existant :
// chaque champ du formulaire récupère donc sa valeur actuelle
// (carrierId, authorId, période MBR, catégorie et description).
//
// En mode "create", props.feedback vaut null :
// les champs sont donc initialisés avec une valeur vide (''),
// sauf la période MBR qui utilise initialPeriod si elle existe,
// sinon le mois actuel grâce à currentMonth().
//
// ?. signifie : "utilise cette propriété seulement si feedback existe".
// ?? signifie : "si la valeur à gauche est null ou undefined, utilise la valeur à droite".
  $effect(() => {
    const feedback = props.feedback
    carrierId = feedback?.carrierId ?? ''
    authorId = feedback?.authorId ?? ''
    mbrPeriod = feedback?.mbrPeriod ?? props.initialPeriod ?? currentMonth()
    category = feedback?.category ?? ''
    description = feedback?.description ?? ''
  })


// Cette fonction est exécutée lorsque le formulaire est soumis.
// preventDefault() empêche le comportement par défaut du formulaire.
// Les valeurs actuelles des champs sont ensuite regroupées dans un objet et envoyées à App.svelte grâce à props.onSave.
// props.onSave correspond à la fonction saveFeedback transmise par App.svelte.
// En mode "create", saveFeedback pourra créer le feedback.
// En mode "edit", saveFeedback pourra mettre à jour le feedback existant.
  function handleSubmit(event) {
    event.preventDefault()
    props.onSave({
      carrierId,
      authorId,
      mbrPeriod,
      category,
      description: description.trim(),
    })
  }

// Ferme la fenêtre uniquement si l'utilisateur clique directement
// sur l'arrière-plan du <dialog>.
// event.target = élément réellement cliqué.
// event.currentTarget = élément qui possède l'événement, ici le <dialog>.
// Si les deux sont identiques, le clic a été fait sur le fond du dialog
// et non sur un élément du formulaire.
  function closeOnBackdrop(event) {
    if (event.target === event.currentTarget) cancel()
  }

  // Fonction centrale pour annuler et fermer le formulaire.
// 1. closeDialog() ferme la fenêtre <dialog>.
// 2. props.onCancel() appelle la fonction transmise par App.svelte,
//    qui remet formMode à null.
// Comme {#if formMode} devient faux dans App.svelte,
// le composant FeedbackForm est alors retiré de la page.
  function cancel() {
    closeDialog()
    props.onCancel()
  }

// Gère l'événement "cancel" du <dialog>, notamment lors d'un appui sur Échap.
// Empêche d'abord la fermeture par défaut du navigateur,
// puis appelle notre fonction cancel() qui ferme la modale
// et informe App.svelte via props.onCancel().
  function handleCancel(event) {
    event.preventDefault()
    cancel()
  }

// Cette fonction reçoit une date (createdAt) et la convertit
// en une date lisible au format français.
// Si la valeur reçue n'est pas une date valide,
// elle retourne simplement la valeur d'origine.
  function formatCreatedAt(value) {
    const date = new Date(value)
    return Number.isNaN(date.getTime())
      ? value
      : date.toLocaleDateString('fr-FR')
  }
</script>


<dialog
  bind:this={dialogElement}
  class="feedback-modal"
  aria-labelledby="feedback-form-title"
  onclick={closeOnBackdrop}
  oncancel={handleCancel}
>
  <section class="form-card">
    <header class="modal-header">
      <h2 id="feedback-form-title">
        {props.mode === 'edit' ? 'Modifier le feedback' : 'Nouveau feedback'}
      </h2>
      <button
        class="close-button"
        type="button"
        aria-label="Fermer"
        onclick={cancel}
      >
        <svg viewBox="0 0 24 24" aria-hidden="true">
          <path d="m6 6 12 12M18 6 6 18" />
        </svg>
      </button>
    </header>

    <form onsubmit={handleSubmit}>
      <label>
        <span>Carrier <span class="required-mark">*</span></span>
        <select bind:value={carrierId} required>
          <option value="" disabled>Sélectionner un carrier</option>
          {#each props.carriers ?? [] as carrier (carrier.id)}
            <option value={carrier.id}>{carrier.name}</option>
          {/each}
        </select>
      </label>

      <div class="form-row">
        <label>
          <span>Période MBR</span>
          <input type="month" bind:value={mbrPeriod} required />
        </label>

        <label>
          <span>Catégorie <span class="required-mark">*</span></span>
          <select bind:value={category} required>
            <option value="" disabled>Sélectionner</option>
            {#each categories as option}
              <option value={option}>{option}</option>
            {/each}
          </select>
        </label>
      </div>

      <label>
        <span>Description <span class="required-mark">*</span></span>
        <textarea
          bind:value={description}
          rows="4"
          required
          placeholder="Décrivez le retour en quelques lignes..."
        ></textarea>
      </label>

      <label>
        <span>Auteur <span class="required-mark">*</span></span>
        <select bind:value={authorId} required>
          <option value="" disabled>Sélectionner votre nom</option>
          {#each props.authors ?? [] as author (author.id)}
            <option value={author.id}>{author.fullName}</option>
          {/each}
        </select>
      </label>

      {#if props.mode === 'edit' && props.feedback?.createdAt}
        <p class="created-date">
          Créé le {formatCreatedAt(props.feedback.createdAt)}
        </p>
      {/if}
<!--
  Bouton permettant de valider et d'enregistrer le formulaire.
  type="submit" indique que ce bouton sert à soumettre le <form>.
  Lorsqu'on clique dessus, le formulaire déclenche son événement "submit".  Comme le <form> contient :
  onsubmit={handleSubmit}
  la fonction handleSubmit est alors exécutée.
  Le fonctionnement est donc :
  clic sur le bouton → submit du formulaire → handleSubmit()
  Le texte affiché sur le bouton dépend du mode du formulaire :
  - mode "create" → "Enregistrer"
  - mode "edit"   → "Enregistrer les modifications"
-->
      <footer class="form-actions">
        <button class="button button-secondary" type="button" onclick={cancel}>
          Annuler
        </button>
        <button class="button button-primary" type="submit">
          {props.mode === 'edit' ? 'Enregistrer les modifications' : 'Enregistrer'}
        </button>
      </footer>
    </form>
  </section>
</dialog>

<style>
  .feedback-modal {
    width: min(560px, calc(100% - 32px));
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

  .feedback-modal::backdrop {
    background: rgb(24 39 57 / 58%);
    backdrop-filter: blur(2px);
  }

  .form-card {
    padding: 24px 28px;
  }

  .modal-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 16px;
    margin-bottom: 16px;
  }

  h2 {
    margin: 0;
    font-size: 22px;
    line-height: 1.3;
  }

  .close-button {
    display: grid;
    width: 44px;
    height: 44px;
    flex: 0 0 auto;
    place-items: center;
    color: #17283f;
    border: 0;
    border-radius: 10px;
    background: #f3f5f7;
    cursor: pointer;
  }

  .close-button svg {
    width: 18px;
    fill: none;
    stroke: currentColor;
    stroke-linecap: round;
    stroke-width: 2;
  }

  form,
  label {
    display: grid;
  }

  form {
    gap: 16px;
  }

  label {
    min-width: 0;
    gap: 6px;
    font-size: 14px;
    font-weight: 600;
  }

  .required-mark {
    color: #bd2920;
  }

  select,
  input,
  textarea {
    width: 100%;
    min-height: 44px;
    padding: 10px 12px;
    color: #18283d;
    border: 1px solid #a9b8c9;
    border-radius: 10px;
    background: #fff;
    font: inherit;
    font-size: 15px;
  }

  textarea {
    min-height: 102px;
    resize: vertical;
  }

  select:focus-visible,
  input:focus-visible,
  textarea:focus-visible,
  button:focus-visible {
    outline: 3px solid rgb(13 112 139 / 30%);
    outline-offset: 2px;
  }

  .form-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 18px;
  }

  .created-date {
    margin: -4px 0 0;
    color: #586a7f;
    font-size: 14px;
  }

  .form-actions {
    display: flex;
    justify-content: flex-end;
    gap: 12px;
    margin-top: 8px;
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

  .button-secondary {
    color: #17283f;
    border-color: #bdc9d6;
    background: #fff;
  }

  .button-primary {
    color: #fff;
    background: #08728c;
  }

  .button-primary:hover {
    background: #075e74;
  }

  @media (max-width: 520px) {
    .form-card {
      padding: 20px;
    }

    .form-row {
      grid-template-columns: 1fr;
      gap: 16px;
    }

    .form-actions {
      flex-direction: column-reverse;
    }

    .button {
      width: 100%;
    }
  }
</style>
