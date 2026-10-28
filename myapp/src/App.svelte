<script>
// Importation des composants

  import FeedbackForm from './lib/components/FeedbackForm.svelte'
  import ConfirmDelete from './lib/components/ConfirmDelete.svelte'

  // Les données ci-dessous sont des tableaux des données fictives stockés dans des variables

  const carriers = [
    { id: 1, name: 'Maersk Line' },
    { id: 2, name: 'CMA CGM' },
    { id: 3, name: 'Hapag LLoyd' },
  ]

  const authors = [
    { id: 1, fullName: 'Brahim El Ghachi' },
    { id: 2, fullName: 'Franck Vossenat' },
    { id: 3, fullName: 'Yi-Shin Cai' },
  ]

  let feedbacks = $state([
    {
      id: 1,
      carrierId: 1,
      authorId: 1,
      carrierName: 'Carrier A',
      category: 'Communication',
      mbrPeriod: '2026-09',
      description:
        "Plusieurs relances ont été nécessaires avant d'obtenir une réponse.",
      createdAt: '2026-09-18T10:25:00Z',
    },
    {
      id: 2,
      carrierId: 1,
      authorId: 2,
      carrierName: 'Carrier A',
      category: 'Documentation',
      mbrPeriod: '2026-09',
      description: 'Description du feedback à renseigner.',
      createdAt: '2026-09-09T10:25:00Z',
    },
    {
      id: 3,
      carrierId: 2,
      authorId: 3,
      carrierName: 'Carrier B',
      category: 'Ponctualité',
      mbrPeriod: '2026-09',
      description: 'Description du feedback à renseigner.',
      createdAt: '2026-09-16T10:25:00Z',
    },
    {
      id: 4,
      carrierId: 3,
      authorId: 2,
      carrierName: 'Carrier C',
      category: 'Communication',
      mbrPeriod: '2026-08',
      description: 'Description du feedback à renseigner.',
      createdAt: '2026-08-21T10:25:00Z',
    },
    {
      id: 5,
      carrierId: 2,
      authorId: 3,
      carrierName: 'Carrier B',
      category: 'Ponctualité',
      mbrPeriod: '2026-08',
      description: 'Description du feedback à renseigner.',
      createdAt: '2026-08-12T10:25:00Z',
    },
  ])
  const categories = ['Communication', 'Ponctualité', 'Documentation', 'Suivi', 'Finance']
  const initialPeriods = ['2026-09', '2026-08', '2026-07']

  // VARIABLES D'ETAT //

  // // Gère l'état de la pop-up du formulaire : null = fermée, 'create' = création, 'edit' = modification. Le formulaire agit comme un interrupteur à 3 positions. Pour ce qui concerne l'affichage, il faut regarder dans le bloc HTML la partie #if qui gère l'affichage du formulaire en fonction de son état (ex : if formMode = null, pas de formulaire, or create = formulaire affichée etc...). Il affiche en gros le composant FeedbackForm)
  let formMode = $state(null)

  // Gère l'affichage de la pop-up de confirmation de suppression : false = cachée, true = affichée
  let showDeleteConfirmation = $state(false)

  // Contient le message de notification affiché à l'utilisateur : chaîne vide = aucun message
  let message = $state('')
  // Indique la vue actuellement affichée.
// 'home' = page d'accueil
// 'history' = page historique
  let currentPage = $state('home')
  // C'est la période MBR actuellement sélectionnée sur l'accueil.
  let selectedPeriod = $state('2026-09')
  // Contiennent les trois filtres que l'on peut sélectionner dans la page historique.
  // Une chaîne vide ('') signifie qu'aucun filtre n'est appliqué.
  let carrierFilter = $state('')
  let periodFilter = $state('')
  let categoryFilter = $state('')
  // Contrôle le mode d'affichage de l'historique.
// 'period' = feedbacks regroupés par période MBR
// 'list' = tous les feedbacks dans une liste simple
  let historyDisplayMode = $state('period')
  // Stocke l'id du feedback actuellement sélectionné.
// null signifie qu'aucun feedback n'est sélectionné.
// Cette valeur est notamment utilisée pour retrouver le feedback
// à modifier ou à supprimer dans le tableau feedbacks.
  let selectedFeedbackId = $state(null)
  // $derived permet de créer une valeur calculée automatiquement
  // à partir d'autres données réactives.
  // Si une donnée utilisée dans le calcul change,
  // Svelte recalcule automatiquement la valeur dérivée.
  // Recherche automatiquement dans feedbacks le feedback dont l'id
// correspond à selectedFeedbackId.
// Si selectedFeedbackId ou feedbacks change,
// Svelte recalcule automatiquement la variable feedback.
  let feedback = $derived(feedbacks.find((item) => item.id === selectedFeedbackId))
  let availablePeriods = $derived(
    [...new Set([...initialPeriods, ...feedbacks.map((item) => item.mbrPeriod)])].sort(
      (a, b) => b.localeCompare(a),
    ),
  )
  let monthlyFeedbacks = $derived(
    feedbacks.filter((item) => item.mbrPeriod === selectedPeriod),
  )
  let activeCarrierCount = $derived(
    new Set(monthlyFeedbacks.map((item) => String(item.carrierId))).size,
  )
  let historyFeedbacks = $derived(
    feedbacks
      .filter(
        (item) =>
          (!carrierFilter || String(item.carrierId) === String(carrierFilter)) &&
          (!periodFilter || item.mbrPeriod === periodFilter) &&
          (!categoryFilter || item.category === categoryFilter),
      )
      .sort((a, b) => b.createdAt.localeCompare(a.createdAt)),
  )
  let historyGroups = $derived.by(() => {
    const groups = new Map()
    for (const item of historyFeedbacks) {
      const group = groups.get(item.mbrPeriod) ?? []
      group.push(item)
      groups.set(item.mbrPeriod, group)
    }
    return [...groups.entries()]
  })

  // FONCTIONS //

  // Fonctions pour ouvrir le formulaire. Le paramètre "mode" reçoit "create" ou "edit" suivant le click de l'utilisateur. Cf écouteur d'évènement sur le bouton "on-click" qui figure dans le HTML 
  function openForm(mode, item = null) {
    message = ''
    selectedFeedbackId = item?.id ?? null
    formMode = mode
  }

  //  Fonction qui enregistre un feedback. Le composant envoie qque chose dans le paramètre (values). FormMode = null (ferme le formulaire)
  function saveFeedback(values) {
    const carrier = carriers.find(
      (item) => String(item.id) === String(values.carrierId),
    )
    if (!carrier) {
      message = 'Veuillez sélectionner un carrier valide.'
      return
    }

    if (formMode === 'edit') {
      if (!feedback) {
        message = 'Le feedback à modifier est introuvable.'
        return
      }
      feedbacks = feedbacks.map((item) =>
        item.id === feedback.id
          ? { ...item, ...values, carrierId: carrier.id, carrierName: carrier.name }
          : item,
      )
      message = 'Le feedback a été modifié.'
    } else {
      const authorId = Number(values.authorId)
      feedbacks = [
        ...feedbacks,
        {
          id: Math.max(0, ...feedbacks.map((item) => item.id)) + 1,
          ...values,
          carrierId: carrier.id,
          authorId,
          carrierName: carrier.name,
          createdAt: new Date().toISOString(),
        },
      ]
      selectedPeriod = values.mbrPeriod
      message = 'Le feedback a été ajouté.'
    }
    formMode = null
  }

  // Fonction qui supprime un feedback et ferme le pop-up (showDeleteConfirmation = false) 
  function deleteFeedback() {
    if (!feedback) {
      message = 'Le feedback à supprimer est introuvable.'
      showDeleteConfirmation = false
      return
    }
    feedbacks = feedbacks.filter((item) => item.id !== feedback.id)
    message = 'Le feedback a été supprimé.'
    showDeleteConfirmation = false
  }

  function shiftPeriod(period, offset) {
    const [year, month] = period.split('-').map(Number)
    const date = new Date(year, month - 1 + offset, 1)
    selectedPeriod = `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, '0')}`
  }

  function formatDate(value) {
    const date = new Date(value)
    return Number.isNaN(date.getTime()) ? value : date.toLocaleDateString('fr-FR')
  }

  function authorName(authorId) {
    return authors.find((author) => String(author.id) === String(authorId))?.fullName ?? ''
  }

  function resetFilters() {
    carrierFilter = ''
    periodFilter = ''
    categoryFilter = ''
  }
</script>

<svelte:head>
  <title>{currentPage === 'home' ? 'Accueil' : 'Historique'} · Carrier Feedback</title>
  <meta
    name="description"
    content="Suivez et gérez les retours fictifs sur la performance des carriers."
  />
</svelte:head>

<div class="app-shell">
  <header class="topbar">
    <a class="brand" href="#home" aria-label="Carrier Feedback, accueil" onclick={() => (currentPage = 'home')}>
      <span class="brand-icon" aria-hidden="true">
        <svg viewBox="0 0 24 24">
          <path d="M5 5.75h14v10.5H10l-5 3v-13.5Z" />
        </svg>
      </span>
      <span>Carrier Feedback</span>
    </a>
    <nav aria-label="Navigation principale">
      <a
        href="#home"
        class:active={currentPage === 'home'}
        aria-current={currentPage === 'home' ? 'page' : undefined}
        onclick={() => {
          currentPage = 'home'
          message = ''
        }}
      >Home</a>
      <a
        href="#history"
        class:active={currentPage === 'history'}
        aria-current={currentPage === 'history' ? 'page' : undefined}
        onclick={() => {
          currentPage = 'history'
          message = ''
        }}
      >Historique</a>
    </nav>
  </header>

  <main class:history-page={currentPage === 'history'}>
    {#if currentPage === 'home'}
      <p class="eyebrow">CARRIER PERFORMANCE – MONTHLY BUSINESS REVIEW</p>
      <div class="home-toolbar">
        <div>
          <label class="period-label" for="home-period">Période MBR</label>
          <div class="period-picker">
            <button class="period-arrow" aria-label="Période précédente" onclick={() => shiftPeriod(selectedPeriod, -1)}>
              <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m15 18-6-6 6-6" /></svg>
            </button>
            <select id="home-period" bind:value={selectedPeriod}>
              {#each availablePeriods as period (period)}
                <option value={period}>{period}</option>
              {/each}
            </select>
            <button class="period-arrow" aria-label="Période suivante" onclick={() => shiftPeriod(selectedPeriod, 1)}>
              <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m9 18 6-6-6-6" /></svg>
            </button>
          </div>
        </div>
        <button class="button button-primary new-feedback" onclick={() => openForm('create')}>
          <span aria-hidden="true">＋</span> Nouveau feedback
        </button>
      </div>

      {#if message}
        <p class="notice" role="status">{message}</p>
      {/if}

      <p class="results-count">
        {monthlyFeedbacks.length} feedback{monthlyFeedbacks.length === 1 ? '' : 's'} pour {activeCarrierCount} carrier{activeCarrierCount === 1 ? '' : 's'} ce mois-ci.
      </p>

      <section class="carrier-list" aria-label="Feedbacks par carrier">
        {#each carriers as carrier (carrier.id)}
          {@const carrierFeedbacks = monthlyFeedbacks.filter((item) => String(item.carrierId) === String(carrier.id)).sort((a, b) => b.createdAt.localeCompare(a.createdAt))}
          <article class="carrier-card">
            <header class="carrier-heading">
              <h2>{carrier.name}</h2>
              <span>
                {carrierFeedbacks.length === 0
                  ? 'aucun feedback'
                  : `${carrierFeedbacks.length} feedback${carrierFeedbacks.length === 1 ? '' : 's'}`}
              </span>
            </header>
            {#if carrierFeedbacks.length}
              <div class="feedback-list">
                {#each carrierFeedbacks as item (item.id)}
                  <div class="feedback-row home-feedback-row">
                    <span class:communication={item.category === 'Communication'} class:punctuality={item.category === 'Ponctualité'} class:documentation={item.category === 'Documentation'} class="category-badge">{item.category}</span>
                    <div class="feedback-copy">
                      <p>{item.description}</p>
                      <span>{authorName(item.authorId)}</span>
                    </div>
                    <time datetime={item.createdAt}>{formatDate(item.createdAt)}</time>
                    <div class="row-actions">
                      <button class="icon-button" aria-label={`Modifier le feedback de ${carrier.name}`} onclick={() => openForm('edit', item)}>
                        <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m14 5 5 5M4 20l4.3-.9L19 8.4a2.1 2.1 0 0 0-3-3L5.3 16.1 4 20Z" /></svg>
                      </button>
                      <button class="icon-button delete-action" aria-label={`Supprimer le feedback de ${carrier.name}`} onclick={() => { selectedFeedbackId = item.id; showDeleteConfirmation = true; message = '' }}>
                        <svg viewBox="0 0 24 24" aria-hidden="true"><path d="M4 7h16M10 11v6m4-6v6M6 7l1 14h10l1-14M9 7V4h6v3" /></svg>
                      </button>
                    </div>
                  </div>
                {/each}
              </div>
            {:else}
              <p class="empty-carrier">Aucun feedback ce mois-ci pour ce carrier.</p>
            {/if}
          </article>
        {/each}
      </section>
    {:else}
      <div class="history-heading">
        <div>
          <h1>Historique</h1>
          <p>Retrouvez un ancien retour, toutes périodes confondues.</p>
        </div>
        <button class="button button-primary new-feedback" onclick={() => openForm('create')}>
          <span aria-hidden="true">＋</span> Nouveau feedback
        </button>
      </div>

      {#if message}
        <p class="notice" role="status">{message}</p>
      {/if}

      <section class="filters" aria-label="Filtres de l’historique">
        <label>
          <span>Carrier</span>
          <select bind:value={carrierFilter}>
            <option value="">Tous les carriers</option>
            {#each carriers as carrier (carrier.id)}
              <option value={carrier.id}>{carrier.name}</option>
            {/each}
          </select>
        </label>
        <label>
          <span>Période MBR</span>
          <select bind:value={periodFilter}>
            <option value="">Toutes les périodes</option>
            {#each availablePeriods as period (period)}
              <option value={period}>{period}</option>
            {/each}
          </select>
        </label>
        <label>
          <span>Catégorie</span>
          <select bind:value={categoryFilter}>
            <option value="">Toutes les catégories</option>
            {#each categories as category (category)}
              <option value={category}>{category}</option>
            {/each}
          </select>
        </label>
        <button class="reset-button" onclick={resetFilters}>Réinitialiser</button>
      </section>

      <div class="history-controls">
        <p class="results-count">{historyFeedbacks.length} feedback{historyFeedbacks.length === 1 ? '' : 's'}</p>
        <div class="view-toggle" aria-label="Présentation de l’historique">
          <button class:active={historyDisplayMode === 'period'} aria-pressed={historyDisplayMode === 'period'} onclick={() => (historyDisplayMode = 'period')}>Par période MBR</button>
          <button class:active={historyDisplayMode === 'list'} aria-pressed={historyDisplayMode === 'list'} onclick={() => (historyDisplayMode = 'list')}>Liste simple</button>
        </div>
      </div>

      {#if historyFeedbacks.length}
        {#if historyDisplayMode === 'period'}
          {#each historyGroups as [period, items] (period)}
            <section class="history-group">
              <h2>MBR {period}</h2>
              <div class="history-card">
                {#each items as item (item.id)}
                  {@const carrier = carriers.find((entry) => String(entry.id) === String(item.carrierId))}
                  <div class="feedback-row history-feedback-row">
                    <strong class="history-carrier">{carrier?.name}</strong>
                    <span class:communication={item.category === 'Communication'} class:punctuality={item.category === 'Ponctualité'} class:documentation={item.category === 'Documentation'} class="category-badge">{item.category}</span>
                    <div class="feedback-copy">
                      <p>{item.description}</p>
                      <span>{authorName(item.authorId)}</span>
                    </div>
                    <time datetime={item.createdAt}>{formatDate(item.createdAt)}</time>
                    <div class="row-actions">
                      <button class="icon-button" aria-label={`Modifier le feedback de ${carrier?.name}`} onclick={() => openForm('edit', item)}>
                        <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m14 5 5 5M4 20l4.3-.9L19 8.4a2.1 2.1 0 0 0-3-3L5.3 16.1 4 20Z" /></svg>
                      </button>
                      <button class="icon-button delete-action" aria-label={`Supprimer le feedback de ${carrier?.name}`} onclick={() => { selectedFeedbackId = item.id; showDeleteConfirmation = true; message = '' }}>
                        <svg viewBox="0 0 24 24" aria-hidden="true"><path d="M4 7h16M10 11v6m4-6v6M6 7l1 14h10l1-14M9 7V4h6v3" /></svg>
                      </button>
                    </div>
                  </div>
                {/each}
              </div>
            </section>
          {/each}
        {:else}
          <div class="history-card simple-history">
            {#each historyFeedbacks as item (item.id)}
              {@const carrier = carriers.find((entry) => String(entry.id) === String(item.carrierId))}
              <div class="feedback-row history-feedback-row">
                <strong class="history-carrier">{carrier?.name}</strong>
                <span class:communication={item.category === 'Communication'} class:punctuality={item.category === 'Ponctualité'} class:documentation={item.category === 'Documentation'} class="category-badge">{item.category}</span>
                <div class="feedback-copy">
                  <p>{item.description}</p>
                  <span>{authorName(item.authorId)}</span>
                </div>
                <time datetime={item.createdAt}>{formatDate(item.createdAt)}</time>
                <div class="row-actions">
                  <button class="icon-button" aria-label={`Modifier le feedback de ${carrier?.name}`} onclick={() => openForm('edit', item)}>
                    <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m14 5 5 5M4 20l4.3-.9L19 8.4a2.1 2.1 0 0 0-3-3L5.3 16.1 4 20Z" /></svg>
                  </button>
                  <button class="icon-button delete-action" aria-label={`Supprimer le feedback de ${carrier?.name}`} onclick={() => { selectedFeedbackId = item.id; showDeleteConfirmation = true; message = '' }}>
                    <svg viewBox="0 0 24 24" aria-hidden="true"><path d="M4 7h16M10 11v6m4-6v6M6 7l1 14h10l1-14M9 7V4h6v3" /></svg>
                  </button>
                </div>
              </div>
            {/each}
          </div>
        {/if}
      {:else}
        <p class="empty-history">Aucun feedback ne correspond à ces filtres.</p>
      {/if}
    {/if}
  </main>

  <footer>
    Projet personnel d’apprentissage · données fictives et anonymisées
  </footer>
</div>
 
 <!--
Ce bloc affiche le composant FeedbackForm uniquement lorsque formMode contient
une valeur ('create' ou 'edit'). Si formMode = null, le formulaire n'est pas affiché.

mode={formMode}
→ Passe au composant FeedbackForm la valeur de formMode ('create' ou 'edit').
  Cela permet au formulaire de savoir s'il doit fonctionner en mode création ou en mode modification.

feedback={formMode === 'edit' ? feedback : null}
→ Si formMode vaut 'edit', on transmet le feedback existant au formulaire afin de préremplir les champs avec ses données. Sinon, en mode 'create', on transmet null car on crée un nouveau feedback.

{carriers} et {authors}
→ Transmettent au composant FeedbackForm les listes carriers et authors, afin qu'elles puissent être utilisées dans le formulaire.

onSave={saveFeedback}
→ Transmet la fonction saveFeedback au composant via la prop onSave. FeedbackForm pourra appeler cette fonction lorsqu'un feedback doit être enregistré.
  La fonction saveFeedback est définie dans App.svelte.

onCancel={() => (formMode = null)}
→ Transmet au composant une fonction qui remet formMode à null. Lorsque FeedbackForm appelle onCancel, le bloc {#if formMode} devient faux
  et le formulaire disparaît.

Remark : tout ce qui est écrit comme attribut dans la balise du composant devient une prop transmise au compo enfant (ci-dessous 6 props sont transmises)
-->
{#if formMode}
  <FeedbackForm
    mode={formMode}
    feedback={formMode === 'edit' ? feedback : null}
    initialPeriod={selectedPeriod}
    {carriers}
    {authors}
    onSave={saveFeedback}
    onCancel={() => (formMode = null)}
  />
{/if}

{#if showDeleteConfirmation}
  <ConfirmDelete
    feedback={feedback}
    onConfirm={deleteFeedback}
    onCancel={() => (showDeleteConfirmation = false)}
  />
{/if}


<style>
  :global(*) {
    box-sizing: border-box;
  }

  :global(body) {
    margin: 0;
    color: #17283f;
    background: #f3f5f7;
    font-family:
      Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont,
      "Segoe UI", sans-serif;
    -webkit-font-smoothing: antialiased;
  }

  :global(button) {
    font: inherit;
  }

  .app-shell {
    display: flex;
    min-height: 100vh;
    flex-direction: column;
  }

  .topbar {
    display: flex;
    min-height: 70px;
    align-items: center;
    justify-content: space-between;
    padding: 0 max(24px, calc((100% - 1120px) / 2));
    border-bottom: 1px solid #e0e6ec;
    background: #fff;
  }

  .brand {
    display: inline-flex;
    align-items: center;
    gap: 12px;
    color: inherit;
    font-size: 18px;
    font-weight: 750;
    text-decoration: none;
  }

  .brand-icon {
    display: grid;
    width: 38px;
    height: 38px;
    place-items: center;
    border-radius: 10px;
    background: #08728c;
  }

  .brand-icon svg {
    width: 20px;
    fill: none;
    stroke: #fff;
    stroke-linejoin: round;
    stroke-width: 1.7;
  }

  nav {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  nav a {
    padding: 10px 16px;
    color: #17283f;
    border-radius: 10px;
    font-size: 14px;
    font-weight: 600;
    text-decoration: none;
  }

  nav a:hover,
  nav a.active {
    background: #e8edf2;
  }

  main {
    width: min(100% - 48px, 1056px);
    flex: 1;
    align-self: center;
    padding: 38px 0 48px;
  }

  main.history-page {
    width: min(100% - 48px, 1056px);
    padding-top: 40px;
  }

  .eyebrow {
    margin: 0 0 22px;
    color: #08728c;
    font-size: 14px;
    font-weight: 750;
    letter-spacing: 0.09em;
  }

  h1 {
    margin: 0;
    font-size: 32px;
    letter-spacing: -0.035em;
  }

  .home-toolbar,
  .history-heading {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    gap: 24px;
  }

  .period-label {
    display: block;
    margin-bottom: 4px;
    font-size: 14px;
    font-weight: 600;
  }

  .period-picker {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .period-picker select,
  .filters select {
    min-height: 44px;
    padding: 0 12px;
    color: #17283f;
    border: 1px solid #a9b8c9;
    border-radius: 10px;
    background: #fff;
    font: inherit;
  }

  .period-picker select {
    min-width: 180px;
  }

  .period-arrow {
    display: grid;
    width: 44px;
    height: 44px;
    place-items: center;
    color: #17283f;
    border: 1px solid #bdc9d6;
    border-radius: 10px;
    background: #fff;
    cursor: pointer;
  }

  .period-arrow svg {
    width: 18px;
    fill: none;
    stroke: currentColor;
    stroke-linecap: round;
    stroke-linejoin: round;
    stroke-width: 1.8;
  }

  .button {
    display: inline-flex;
    min-height: 48px;
    align-items: center;
    justify-content: center;
    gap: 10px;
    padding: 0 22px;
    border: 1px solid transparent;
    border-radius: 10px;
    font-size: 15px;
    font-weight: 700;
    cursor: pointer;
  }

  .button:focus-visible,
  nav a:focus-visible,
  .period-arrow:focus-visible,
  .icon-button:focus-visible,
  .reset-button:focus-visible,
  .view-toggle button:focus-visible {
    outline: 3px solid rgb(13 112 139 / 30%);
    outline-offset: 2px;
  }

  .button-primary {
    color: #fff;
    background: #08728c;
  }

  .button-primary:hover {
    background: #075e74;
  }

  .new-feedback {
    min-width: 218px;
    min-height: 48px;
  }

  .new-feedback span {
    font-size: 21px;
    font-weight: 400;
    line-height: 1;
  }

  .results-count {
    margin: 22px 0;
    color: #53657a;
    font-size: 16px;
  }

  .notice {
    margin: 18px 0;
    padding: 12px 16px;
    color: #075e74;
    border: 1px solid #b8dce4;
    border-radius: 10px;
    background: #eaf6f8;
  }

  .carrier-list {
    display: grid;
    gap: 24px;
  }

  .carrier-card,
  .history-card {
    overflow: hidden;
    border: 1px solid #d9e1e9;
    border-radius: 15px;
    background: #fff;
  }

  .carrier-heading {
    display: flex;
    min-height: 54px;
    align-items: center;
    gap: 14px;
    padding: 0 24px;
    border-bottom: 1px solid #dfe5eb;
    background: #f9fafb;
  }

  .carrier-heading h2 {
    margin: 0;
    font-size: 17px;
  }

  .carrier-heading span {
    color: #5b6d82;
    font-size: 14px;
  }

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

  .empty-carrier {
    margin: 0;
    padding: 20px 24px;
    color: #4e6076;
  }

  .history-heading {
    align-items: center;
    margin-bottom: 22px;
  }

  .history-heading p {
    margin: 4px 0 0;
    color: #4e6076;
  }

  .filters {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr)) auto;
    align-items: end;
    gap: 16px;
    padding: 20px 24px;
    border: 1px solid #d9e1e9;
    border-radius: 12px;
    background: #fff;
  }

  .filters label {
    display: grid;
    min-width: 0;
    gap: 6px;
    font-size: 14px;
    font-weight: 600;
  }

  .filters select {
    width: 100%;
    font-size: 15px;
  }

  .reset-button {
    min-height: 44px;
    padding: 0 4px;
    color: #08728c;
    border: 0;
    background: transparent;
    font: inherit;
    font-weight: 700;
    white-space: nowrap;
    cursor: pointer;
  }

  .history-controls {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin: 24px 0 20px;
  }

  .history-controls .results-count {
    margin: 0;
  }

  .view-toggle {
    display: flex;
    padding: 4px;
    border-radius: 11px;
    background: #e5eaf0;
  }

  .view-toggle button {
    min-height: 40px;
    padding: 0 16px;
    border: 0;
    border-radius: 8px;
    background: transparent;
    font: inherit;
    font-size: 14px;
    cursor: pointer;
  }

  .view-toggle button.active {
    background: #fff;
  }

  .history-group {
    margin-bottom: 22px;
  }

  .history-group h2 {
    margin: 0 0 10px;
    font-size: 19px;
  }

  .history-feedback-row {
    grid-template-columns: 110px 128px minmax(0, 1fr) 92px 68px;
    min-height: 76px;
  }

  .history-carrier {
    font-size: 15px;
  }

  .simple-history {
    margin-top: 20px;
  }

  .empty-history {
    padding: 24px;
    color: #53657a;
    border: 1px solid #d9e1e9;
    border-radius: 12px;
    background: #fff;
  }

  footer {
    padding: 24px;
    color: #65758a;
    border-top: 1px solid #e0e6ec;
    background: #f3f5f7;
    font-size: 13px;
    text-align: center;
  }

  @media (max-width: 760px) {
    .topbar {
      min-height: 64px;
      padding: 0 20px;
    }

    main {
      width: min(100% - 40px, 520px);
      padding: 36px 0;
    }

    main.history-page {
      width: min(100% - 40px, 520px);
    }

    .home-toolbar,
    .history-heading {
      align-items: stretch;
      flex-direction: column;
    }

    .new-feedback {
      width: 100%;
    }

    .filters {
      grid-template-columns: 1fr 1fr;
    }

    .home-feedback-row,
    .history-feedback-row {
      grid-template-columns: minmax(0, 1fr) auto;
      gap: 10px 14px;
    }

    .home-feedback-row .category-badge,
    .history-feedback-row .category-badge {
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
    .brand {
      gap: 8px;
      font-size: 15px;
    }

    .topbar {
      padding: 0 12px;
    }

    nav {
      gap: 0;
    }

    nav a {
      padding: 9px 10px;
      font-size: 13px;
    }

    main,
    main.history-page {
      width: min(100% - 28px, 520px);
    }

    .filters {
      grid-template-columns: 1fr;
      padding: 16px;
    }

    .history-controls {
      align-items: flex-start;
      flex-direction: column;
      gap: 12px;
    }

    .view-toggle {
      width: 100%;
    }

    .view-toggle button {
      flex: 1;
      padding: 0 8px;
      font-size: 12px;
    }

    .carrier-heading {
      padding: 0 16px;
    }

    .feedback-row {
      padding: 14px 16px;
    }
  }
</style>