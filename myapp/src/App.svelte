<script>
// Importation des composants

  import FeedbackForm from './lib/components/FeedbackForm.svelte'
  import ConfirmDelete from './lib/components/ConfirmDelete.svelte'
  import FeedbackRow from './lib/components/FeedbackRow.svelte'

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
    { id: 4, fullName: 'Orsolya Varga' },
    { id: 5, fullName: 'Christian Lucas Tenas' },
  ]

  let feedbacks = $state([
    {
      id: 1,
      carrierId: 1,
      authorId: 1,
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
      category: 'Documentation',
      mbrPeriod: '2026-09',
      description: 'Description du feedback à renseigner.',
      createdAt: '2026-09-09T10:25:00Z',
    },
    {
      id: 3,
      carrierId: 2,
      authorId: 3,
      category: 'Ponctualité',
      mbrPeriod: '2026-09',
      description: 'Description du feedback à renseigner.',
      createdAt: '2026-09-16T10:25:00Z',
    },
    {
      id: 4,
      carrierId: 3,
      authorId: 2,
      category: 'Communication',
      mbrPeriod: '2026-08',
      description: 'Description du feedback à renseigner.',
      createdAt: '2026-08-21T10:25:00Z',
    },
    {
      id: 5,
      carrierId: 2,
      authorId: 3,
      category: 'Ponctualité',
      mbrPeriod: '2026-08',
      description: 'Description du feedback à renseigner.',
      createdAt: '2026-08-12T10:25:00Z',
    },
  ])
  const categories = ['Communication', 'Ponctualité', 'Documentation', 'Suivi', 'Finance']
  const initialPeriods = ['2026-09', '2026-08', '2026-07', '2026-06']

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
  // Stocke l'id du feedback actuellement sélectionné (uniquement l'ID sans le contenu du feedback à savoir l'objet complet)
// null signifie qu'aucun feedback n'est sélectionné.
// Cette valeur est notamment utilisée pour retrouver le feedback
// à modifier ou à supprimer dans le tableau feedbacks
  let selectedFeedbackId = $state(null)
  // $derived permet de créer une valeur calculée automatiquement à partir d'autres données réactives.
  // Si une donnée utilisée dans le calcul change, Svelte recalcule automatiquement la valeur dérivée.
  // Recherche automatiquement dans feedbacks le feedback dont l'id correspond à selectedFeedbackId.
// Si selectedFeedbackId ou feedbacks change, Svelte recalcule automatiquement la variable feedback.

// Variable calculée automatiquement à partir de feedbacks et selectedFeedbackId. Cette variable sert principalement à la modification et à la suppression d'un feedback. Elle cherche d'abord l'id du feedback puis l'objet complet qu'elle stocke dans la variable "feedback"
// La méthode find() parcourt le tableau feedbacks et retourne le premier élément
// dont l'id est égal à selectedFeedbackId.
// "item" (variable temporaire) représente chaque feedback examiné successivement dans le tableau.
// Si selectedFeedbackId ou feedbacks change, $derived recalcule automatiquement feedback.
// Si aucun feedback ne correspond à l'id recherché, find() retourne undefined.
  let feedback = $derived(feedbacks.find((item) => item.id === selectedFeedbackId))
  
// Variable qui crée automatiquement la liste de toutes les périodes MBR disponibles dans l'application (sur les pages Home et History).
// Combine les périodes prévues dans initialPeriods avec celles présentes dans feedbacks (propriété mbrPeriod).
// map() crée un nouveau tableau en parcourant chaque feedback pour ne récupérer que sa période mbrPeriod.
// Le spread (...) extrait les éléments des deux tableaux afin de les réunir dans un seul tableau.
// Set supprime ensuite les périodes présentes en double.
// Le spread (...) placé devant le Set extrait ses valeurs afin de les replacer dans un nouveau tableau.
// Enfin, sort() classe les périodes de la plus récente à la plus ancienne.
// Cette variable permet ainsi d'ajouter automatiquement de nouvelles périodes à la liste lorsqu'un feedback est créé,
// sans devoir les ajouter manuellement dans initialPeriods.
  let availablePeriods = $derived(
    [...new Set([...initialPeriods, ...feedbacks.map((item) => item.mbrPeriod)])].sort(
      (a, b) => b.localeCompare(a),
    ),
  )

// Cette variable retrouve la position de la période sélectionnée dans la liste disponible.
  let periodIndex = $derived(availablePeriods.indexOf(selectedPeriod))

// Variable qui contient uniquement les feedbacks de la période MBR sélectionnée.
// filter() parcourt feedbacks et conserve les feedbacks dont mbrPeriod correspond à selectedPeriod.
// Comme il s'agit d'un $derived, la liste est recalculée automatiquement lorsque feedbacks ou selectedPeriod change.
// Cette variable permet à la page Home d'afficher et de compter uniquement les feedbacks correspondant à la période MBR actuellement sélectionnée.
  let monthlyFeedbacks = $derived(
    feedbacks.filter((item) => item.mbrPeriod === selectedPeriod),
  )

// Cette variable calcule combien de carriers différents ont au moins un feedback pour la période MBR sélectionnée. Il s'agit du message sur la page "home" (ex : 3 feedbacks pour 2 carriers ce mois-ci)
// Variable qui calcule le nombre de carriers différents ayant au moins un feedback pour la période MBR sélectionnée.
// map() récupère le carrierId de chaque feedback présent dans monthlyFeedbacks.
// String() convertit les carrierId en chaînes de caractères afin d'uniformiser leur format (ex : ["1", "1", "2"])
// Set supprime les carrierId présents en double (ex : ["1", "2"])
// .size retourne ensuite le nombre de carrierId uniques (ex : 2).
// Comme il s'agit d'un $derived, le nombre est recalculé automatiquement lorsque monthlyFeedbacks change.
  let activeCarrierCount = $derived(
    new Set(monthlyFeedbacks.map((item) => String(item.carrierId))).size,
  )
// Variable qui contient les feedbacks à afficher dans la page Historique.
// Elle part du tableau feedbacks qui contient tous les feedbacks sous forme d'objets complets.
// filter() parcourt chaque feedback, représenté temporairement par "item",
// et conserve uniquement les objets qui correspondent aux filtres sélectionnés : carrier, période MBR et catégorie.
// Pour chaque filtre, s'il est vide (''), aucun filtrage n'est effectué sur ce critère (et par défaut affiche toutes les données des 3 filtres)
// String() permet de convertir les carrierId en chaînes de caractères afin de comparer des valeurs du même type.
// Les conditions && imposent que tous les filtres actifs soient respectés.
// filter() conserve l'objet feedback complet : les autres propriétés comme createdAt restent donc disponibles.
// sort() utilise ensuite createdAt pour classer les feedbacks du plus récent au plus ancien.
// Comme il s'agit d'un $derived, la liste est recalculée automatiquement lorsque feedbacks ou l'un des filtres change.
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

// Variable qui regroupe les feedbacks de historyFeedbacks par période MBR.
// $derived.by() permet d'effectuer ce calcul automatiquement chaque fois que historyFeedbacks change.
//
// groups est une Map qui sert à ranger les feedbacks par période.
// La période MBR est utilisée comme clé (ex : "2026-09") et la valeur associée
// est un tableau contenant tous les objets feedback de cette période.
//
// La boucle for...of parcourt historyFeedbacks.
// À chaque tour, "item" est une variable temporaire qui contient l'objet feedback complet actuellement parcouru.
//
// groups.get(item.mbrPeriod) cherche si un groupe existe déjà pour la période du feedback.
// Si aucun groupe n'existe encore, ?? [] crée un tableau vide.
//
// group.push(item) ajoute l'objet feedback complet dans le groupe correspondant à sa période.
//
// groups.set(item.mbrPeriod, group) enregistre ensuite le groupe dans la Map
// en utilisant la période MBR comme clé.
//
// Enfin, groups.entries() récupère chaque couple [période, tableau de feedbacks]
// et le spread (...) permet de placer ces couples dans un tableau.
//
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

  // Ouvre le formulaire de création ou de modification.
// Appelée depuis le HTML par le bouton "Nouveau feedback" : openForm('create')
// et par le bouton "Modifier" (crayon) : openForm('edit', item).
// Le paramètre "mode" reçoit 'create' ou 'edit' selon le bouton cliqué.
// Le paramètre "item" reçoit l'objet feedback de la ligne cliquée (mode 'edit' uniquement).
// Il vaut null par défaut, ce qui est le cas en mode 'create'.
// message = '' efface l'ancienne notification.
// item?.id récupère l'id du feedback seulement si item existe (?. évite une erreur si item = null).
// ?? null met null si aucun id n'est trouvé : aucun feedback n'est alors sélectionné.
// formMode = mode change l'état du formulaire : le {#if formMode} en bas du HTML
// affiche alors le composant FeedbackForm.
  function openForm(mode, item = null) {
    message = ''
    selectedFeedbackId = item?.id ?? null
    formMode = mode
  }

  function editFeedback(item) {
    openForm('edit', item)
  }

  function requestDelete(item) {
    selectedFeedbackId = item.id
    showDeleteConfirmation = true
    message = ''
  }

//  Fonction qui enregistre un feedback. Le composant envoie qque chose dans le paramètre (values). FormMode = null (ferme le formulaire)
  
  // Enregistre un feedback (création ou modification). Appelée par FeedbackForm via la prop onSave
  // (onSave={saveFeedback} dans le HTML) : props.onSave(...) dans FeedbackForm appelle cette fonction.
  // Le paramètre "values" reçoit l'objet envoyé par le formulaire : carrierId, authorId, mbrPeriod,
  // category et description (sans id ni createdAt). Il recoit ces infos du composant enfant (props)
  // La fonction ne touche jamais au HTML : elle modifie des variables d'état
  // (feedbacks, selectedPeriod, message, formMode) et Svelte met automatiquement l'écran à jour.
  function saveFeedback(values) {
  // Recherche dans le tableau carriers l'objet carrier choisi dans le formulaire.
    // "item" est une variable temporaire : le carrier examiné à chaque tour.
    // String() convertit les deux ids en texte pour comparer des valeurs du même type.
    // Si aucun carrier n'est trouvé, find() retourne undefined.
    const carrier = carriers.find(
      (item) => String(item.id) === String(values.carrierId),
    )
    // Si le carrier n'existe pas, on écrit un message d'erreur (affiché par {#if message} dans le HTML)
    // et return arrête la fonction : rien n'est enregistré.
    if (!carrier) {
      message = 'Veuillez sélectionner un carrier valide.'
      return
    }
// Mode MODIFICATION : formMode vaut 'edit' si l'utilisateur a cliqué sur le crayon.
    // "feedback" est la variable $derived qui contient l'objet complet retrouvé grâce à selectedFeedbackId.
    // S'il est introuvable, on affiche une erreur et on arrête la fonction.
    if (formMode === 'edit') {
      if (!feedback) {
        message = 'Le feedback à modifier est introuvable.'
        return
      }
    // map() parcourt tous les feedbacks et crée un NOUVEAU tableau.
      // "item" représente chaque feedback parcouru.
      // Si item.id correspond au feedback sélectionné, on retourne une version modifiée :
      // - ...item conserve les anciennes propriétés (notamment id et createdAt) ;
      // - ...values écrase les propriétés modifiées dans le formulaire ;
      // - carrierId est remis à jour depuis le carrier validé.
      // Sinon (: item), le feedback est retourné sans modification.
      // Réaffecter feedbacks déclenche le recalcul de tous les $derived et la mise à jour du HTML.
      feedbacks = feedbacks.map((item) =>
        item.id === feedback.id
          ? { ...item, ...values, carrierId: carrier.id }
          : item,
      )
      message = 'Le feedback a été modifié.'
    } else {
      // Mode CRÉATION : exécuté quand formMode n'est pas 'edit' (clic sur "Nouveau feedback").
      // Number() convertit authorId en nombre pour garder le même type que le tableau authors.
      const authorId = Number(values.authorId)
      // On crée un nouveau tableau : les anciens feedbacks (...feedbacks) + un nouvel objet à la fin.
      // Propriétés du nouvel objet (si une propriété apparaît deux fois, la dernière l'emporte) :
      // - id : Math.max(0, ...ids) + 1 = le plus grand id existant + 1.
      //   Le spread (...) transforme le tableau d'ids en arguments pour Math.max.
      //   Le 0 évite -Infinity si le tableau est vide (le premier id sera alors 1) ;
      // - ...values : reprend les données saisies dans le formulaire ;
      // - carrierId : issu du carrier validé, il écrase / complète values ;
      // - authorId : version convertie en nombre, qui écrase celle de values ;
      // - createdAt : date de création générée automatiquement au format ISO.
      feedbacks = [
        ...feedbacks,
        {
          id: Math.max(0, ...feedbacks.map((item) => item.id)) + 1,
          ...values,
          carrierId: carrier.id,
          authorId,
          createdAt: new Date().toISOString(),
        },
      ]
       // La page Home bascule sur la période du feedback créé, pour qu'il soit visible immédiatement.
      selectedPeriod = values.mbrPeriod
      // Remplit message : le bloc {#if message} du HTML affiche alors la notification.
      message = 'Le feedback a été ajouté.'
    }
    // Cette ligne est en dehors du if/else : elle s'exécute après une modification OU une création.
    // formMode passe à null, donc {#if formMode} devient faux dans le HTML :
    // le composant FeedbackForm est retiré de la page et la fenêtre se ferme.
    formMode = null
  }

  // Fonction qui supprime un feedback et ferme le pop-up (showDeleteConfirmation = false) 
  // Supprime le feedback actuellement sélectionné.
//
// Le clic sur l’icône de suppression place d’abord l’id du feedback
// dans selectedFeedbackId et ouvre le composant ConfirmDelete.
//
// La variable dérivée "feedback" retrouve ensuite l’objet complet
// correspondant à selectedFeedbackId.
//
// Si ce feedback est introuvable, un message d’erreur est affiché,
// la fenêtre de confirmation est fermée et return arrête la fonction.
//
// Sinon, filter() crée un nouveau tableau contenant tous les feedbacks
// dont l’id est différent de celui du feedback sélectionné.
// Le feedback correspondant est donc exclu du nouveau tableau.
//
// Après la suppression, un message de confirmation est affiché
// et showDeleteConfirmation passe à false pour fermer la fenêtre.
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

// Cette fonction sélectionne la période disponible voisine selon le sens de la flèche.
  function shiftPeriod(offset) {
    const nextIndex = periodIndex - offset
    if (nextIndex < 0 || nextIndex >= availablePeriods.length) return
    selectedPeriod = availablePeriods[nextIndex]
  }

  function formatDate(value) {
    const date = new Date(value)
    return Number.isNaN(date.getTime()) ? value : date.toLocaleDateString('fr-FR')
  }

  // Retrouve le nom du carrier à partir de son identifiant.
  function carrierName(carrierId) {
    return carriers.find((carrier) => String(carrier.id) === String(carrierId))?.name ?? ''
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
   <!--
      LOGO CLIQUABLE
      onclick={() => (currentPage = 'home')} : un clic sur le logo ramène à l'accueil.
      Le "() =>" est indispensable : c'est une fonction fléchée, une action
      "à exécuter plus tard, au clic". Sans elle, l'affectation currentPage = 'home'
      s'exécuterait une seule fois au chargement de la page, pas au clic.
      Remarque : contrairement au lien "Home", le logo ne vide pas message.
      Si une notification est affichée, elle reste visible après le clic sur le logo.
    -->
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
      <!--
        LIEN "HOME"
        Quatre mécanismes sur la même balise :
        - href="#home" : change seulement la fin de l'URL. L'application ne la lit jamais,
          c'est currentPage qui décide de la page affichée (qui est une fonction)
        - class:active={currentPage === 'home'} : ajoute la classe CSS "active"
          si la condition est vraie, la retire sinon. C'est ce qui grise le lien
          de la page courante (voir "nav a.active" dans le CSS).
        - aria-current={... ? 'page' : undefined} : indique aux lecteurs d'écran
          quelle est la page courante. Si la valeur est undefined, Svelte n'écrit
          pas l'attribut du tout.
        - onclick={() => { ... }} : deux instructions, donc des accolades.
          On change de page ET on vide message, pour que la notification
          ("Le feedback a été ajouté") ne reste pas affichée sur l'autre page.
      -->
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
      <!-- LIEN "HISTORIQUE" : même mécanisme que "Home", avec la valeur 'history'. -->
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
  <!--
    BALISE <main>
    class:history-page={currentPage === 'history'} : même principe que class:active.
    La classe "history-page" est ajoutée uniquement sur la page Historique,
    ce qui permet d'ajuster son style (voir "main.history-page" dans le CSS).
  -->
  <main class:history-page={currentPage === 'history'}>
  <!--
      AIGUILLAGE PRINCIPAL : c'est ici que se fait le vrai "changement de chaîne".
      {#if currentPage === 'home'} : si currentPage vaut 'home', Svelte affiche
      le bloc de la page Home et retire l'autre du DOM.
      {:else} : sinon (currentPage vaut 'history'), il affiche la page Historique.
      Un seul des deux blocs existe dans la page à un instant donné.
    -->
    {#if currentPage === 'home'}
      <p class="eyebrow">CARRIER PERFORMANCE – MONTHLY BUSINESS REVIEW</p>
      <div class="home-toolbar">
        <div>
          <label class="period-label" for="home-period">Période MBR</label>
              <!--
        FLÈCHE "PÉRIODE PRÉCÉDENTE"
        shiftPeriod(-1) sélectionne la période disponible immédiatement plus ancienne.
      -->
          <div class="period-picker">
            <button class="period-arrow" aria-label="Période précédente" disabled={periodIndex < 0 || periodIndex >= availablePeriods.length - 1} onclick={() => shiftPeriod(-1)}>
              <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m15 18-6-6 6-6" /></svg>
            </button>
              <!--
        SÉLECTEUR DE PÉRIODE
        bind:value={selectedPeriod} : lien dans les deux sens.
        - Tu choisis une option → selectedPeriod change.
        - selectedPeriod change (via les flèches) → le <select> affiche la nouvelle valeur.
        {#each availablePeriods as period (period)} : une <option> par période
        disponible. availablePeriods est le $derived qui fusionne initialPeriods
        et les périodes des feedbacks. (period) est la clé : chaque période est unique.
      -->
            <select id="home-period" bind:value={selectedPeriod}>
              {#each availablePeriods as period (period)}
                <option value={period}>{period}</option>
              {/each}
            </select>
             <!-- FLÈCHE "PÉRIODE SUIVANTE" : sélectionne la période disponible immédiatement plus récente. -->
            <button class="period-arrow" aria-label="Période suivante" disabled={periodIndex <= 0} onclick={() => shiftPeriod(1)}>
              <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m9 18 6-6-6-6" /></svg>
            </button>
          </div>
        </div>
         <!--
    BOUTON "NOUVEAU FEEDBACK"
    openForm('create') met formMode à 'create'. Le {#if formMode} en bas du fichier
    affiche alors FeedbackForm. Aucun item n'est transmis : le formulaire sera vide.
  -->
        <button class="button button-primary new-feedback" onclick={() => openForm('create')}>
          <span aria-hidden="true">＋</span> Nouveau feedback
        </button>
      </div>
<!--
  NOTIFICATION
  {#if message} : une chaîne vide est "falsy", donc le bloc n'apparaît que si
  une fonction a rempli message (ex : "Le feedback a été ajouté.").
  role="status" : les lecteurs d'écran annoncent le message automatiquement.
-->
      {#if message}
        <p class="notice" role="status">{message}</p>
      {/if}
<!--
  COMPTEUR
  {monthlyFeedbacks.length} affiche le nombre de feedbacks de la période.
  Les ternaires gèrent le pluriel : si length === 1 on n'ajoute rien, sinon on ajoute "s".
  activeCarrierCount est le $derived qui compte les carriers distincts.
-->
      <p class="results-count">
        {monthlyFeedbacks.length} feedback{monthlyFeedbacks.length === 1 ? '' : 's'} pour {activeCarrierCount} carrier{activeCarrierCount === 1 ? '' : 's'} ce mois-ci.
      </p>

      <section class="carrier-list" aria-label="Feedbacks par carrier">
      <!--
    UNE CARTE PAR CARRIER
    {#each carriers as carrier (carrier.id)} : le bloc est répété pour chaque carrier
    (Maersk Line, CMA CGM, Hapag Lloyd).

    {@const carrierFeedbacks = ...} : calcul local, valable uniquement dans cette
    itération. Pour le carrier courant :
    1. monthlyFeedbacks.filter(...) garde les feedbacks de CE carrier
       (String() des deux côtés pour comparer des valeurs de même type) ;
    2. .sort(...) les classe du plus récent au plus ancien grâce à createdAt.
    Comme filter() crée un nouveau tableau, le sort() ne modifie pas monthlyFeedbacks.
  -->
        {#each carriers as carrier (carrier.id)}
          {@const carrierFeedbacks = monthlyFeedbacks.filter((item) => String(item.carrierId) === String(carrier.id)).sort((a, b) => b.createdAt.localeCompare(a.createdAt))}
          <article class="carrier-card">
            <header class="carrier-heading">
              <h2>{carrier.name}</h2>
                <!-- Ternaire à 3 cas : aucun feedback / 1 feedback / plusieurs feedbacks. -->
              <span>
                {carrierFeedbacks.length === 0
                  ? 'aucun feedback'
                  : `${carrierFeedbacks.length} feedback${carrierFeedbacks.length === 1 ? '' : 's'}`}
              </span>
            </header>
             <!--
        {#if carrierFeedbacks.length} : 0 est "falsy", donc le bloc ne s'affiche
        que s'il y a au moins un feedback. {:else} affiche sinon le message vide.
      -->
            {#if carrierFeedbacks.length}
              <div class="feedback-list">
                <!-- Une ligne par feedback du carrier. item = le feedback de la ligne. -->
                {#each carrierFeedbacks as item (item.id)}
                  <FeedbackRow
                    feedback={item}
                    display="home"
                    carrierName={carrier.name}
                    authorName={authorName(item.authorId)}
                    formattedDate={formatDate(item.createdAt)}
                    onEdit={editFeedback}
                    onDelete={requestDelete}
                  />
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
        <!-- Bouton de création : au clic, appelle openForm('create').
     () => ... est nécessaire pour que la fonction ne s'exécute qu'au clic et non au chargement de la page.
     Aucun feedback n'est envoyé : item vaut null dans openForm() et le formulaire s'ouvre vide. -->
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
                  <FeedbackRow
                    feedback={item}
                    display="history"
                    carrierName={carrier?.name}
                    authorName={authorName(item.authorId)}
                    formattedDate={formatDate(item.createdAt)}
                    onEdit={editFeedback}
                    onDelete={requestDelete}
                  />
                {/each}
              </div>
            </section>
          {/each}
        {:else}
          <div class="history-card simple-history">
            {#each historyFeedbacks as item (item.id)}
              {@const carrier = carriers.find((entry) => String(entry.id) === String(item.carrierId))}
              <FeedbackRow
                feedback={item}
                display="history"
                carrierName={carrier?.name}
                authorName={authorName(item.authorId)}
                formattedDate={formatDate(item.createdAt)}
                onEdit={editFeedback}
                onDelete={requestDelete}
              />
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

Ce bloc réagit à openForm() : dès que formMode passe de null à 'create' ou 'edit', le formulaire s'affiche.

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

{#if showDeleteConfirmation && feedback}
  <ConfirmDelete
    {feedback}
    carrierName={carrierName(feedback.carrierId)}
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

  .period-arrow:disabled {
    opacity: 0.5;
    cursor: not-allowed;
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

  }
</style>