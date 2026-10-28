
Circuit d'ouverture du formulaire 

                  APP.SVELTE

formMode = 'create'
        │
        ↓
{#if formMode} → vrai
        │
        ↓
 <FeedbackForm
     onSave={saveFeedback}
 />
        │
        ↓
────────────────────────────────

             FEEDBACKFORM.SVELTE

L'utilisateur remplit :
Carrier
Auteur
Période
Catégorie
Description
        │
        ↓
    bind:value
        │
        ↓
carrierId, authorId,
mbrPeriod, category,
description
        │
        ↓
clic "Enregistrer"
        │
        ↓
handleSubmit()
        │
        ↓
props.onSave({...})
        │
        ↓
────────────────────────────────
        │
        ↓
                  APP.SVELTE

saveFeedback(values)