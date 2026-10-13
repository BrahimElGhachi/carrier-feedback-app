# Carrier Feedback App

**Carrier Performance – Monthly Business Review**

Application web permettant de centraliser les feedbacks concernant les compagnies maritimes (*carriers*) et de préparer les réunions mensuelles (MBR).

> **Statut :** MVP en préparation, maquettes réalisées  
> **Technologie cible :** SvelteKit  
> **Objectif :** projet d'apprentissage inspiré d'un besoin métier réel

---

## Contexte

Lors de la préparation des réunions mensuelles avec les carriers, les informations sont souvent dispersées entre plusieurs outils :

- Teams
- E-mails
- Conversations informelles
- Fichiers PowerPoint

Cette dispersion rend difficile :

- la collecte des retours ;
- la conservation de l'historique ;
- la recherche d'informations anciennes ;
- la préparation des réunions.

---

## Objectif

Carrier Feedback App vise à fournir un point d'entrée unique permettant :

- de saisir un feedback concernant un carrier ;
- de **choisir un mois (période MBR) et de voir les feedbacks regroupés par carrier**, pour préparer la réunion ;
- de retrouver rapidement l'historique d'un carrier ;
- de savoir qui a remonté chaque feedback ;
- de disposer d'informations structurées et facilement exploitables.

---

## Utilisateurs cibles

L'application est destinée aux équipes **Maersk LNS**. Au moins 15 personnes seront amenées à ajouter des feedbacks.

Chaque utilisateur peut :

- choisir son nom dans une liste d'auteurs ;
- sélectionner un carrier ;
- ajouter un feedback ;
- consulter les feedbacks existants ;
- modifier ou supprimer un feedback.

Pour le MVP, il n'y a pas d'authentification : l'auteur est choisi dans un menu déroulant (voir [Hors périmètre du MVP](#hors-périmètre-du-mvp)).

---

## Fonctionnalités du MVP

### Préparation de la MBR

- Choisir une période MBR (mois)
- Voir les feedbacks regroupés par carrier
- Afficher aussi les carriers sans feedback sur la période
- Afficher l'auteur de chaque feedback

### Gestion des feedbacks

- Créer un feedback
- Consulter les feedbacks
- Modifier un feedback
- Supprimer un feedback (avec confirmation)

### Recherche

- Filtrer par carrier
- Filtrer par période MBR
- Filtrer par catégorie

---

## Modèle de données

### Carrier

```text
id
name
```

### Author

```text
id
fullName
```

La liste des auteurs est renseignée à l'avance (noms et prénoms) et alimente le menu déroulant du formulaire.

### Feedback

```text
id
carrierId
authorId
mbrPeriod
category
description
createdAt
```

Les maquettes utilisent trois catégories : Communication, Ponctualité et Documentation.

---

## Relations

```text
Carrier (1)
    |
    +----< Feedback (N) >----+
                             |
                        Author (1)
```

Un carrier peut posséder plusieurs feedbacks.

Un auteur peut avoir écrit plusieurs feedbacks.

Chaque feedback est associé à un seul carrier et à un seul auteur.

---

## Backlog MVP

| ID | User Story | Priorité | Statut |
|----|-----------|-----------|---------|
| US-01 | Ajouter un feedback | Haute | 🔄 À faire |
| US-02 | Préparer la MBR (période + carriers) | Haute | 🔄 À faire |
| US-03 | Modifier un feedback | Haute | 🔄 À faire |
| US-04 | Supprimer un feedback | Haute | 🔄 À faire |
| US-05 | Filtrer l'historique des feedbacks | Moyenne | 🔄 À faire |

---

## User Stories

| ID | Description | Critères d'acceptation |
|----|-------------|------------------------|
| US-01 | En tant qu'utilisateur, je souhaite enregistrer un feedback afin de conserver l'information. | Carrier, catégorie, description et auteur obligatoires. Auteur choisi dans une liste. Période MBR préremplie. |
| US-02 | En tant qu'utilisateur, je souhaite choisir un mois et voir les feedbacks par carrier afin de préparer la MBR. | Sélecteur de période. Un bloc par carrier avec ses feedbacks et leur auteur. Carriers sans feedback affichés. |
| US-03 | En tant qu'utilisateur, je souhaite modifier un feedback. | Pop-up préremplie, données sauvegardées. |
| US-04 | En tant qu'utilisateur, je souhaite supprimer un feedback. | Pop-up de confirmation avant suppression. |
| US-05 | En tant qu'utilisateur, je souhaite filtrer l'historique des feedbacks. | Filtre par carrier, période MBR et catégorie, combinables. Bouton de réinitialisation des filtres. |

---

## Écrans du MVP

L'application compte **2 pages** et **3 pop-ups**. Toutes les pages partagent la même barre de navigation (logo « Carrier Feedback », onglets **Home** et **Historique**) et un pied de page rappelant que les données sont fictives et anonymisées.

### Pages

#### Home (page d'accueil, `/`)

Page de préparation de la MBR.

- sur-titre « Carrier Performance – Monthly Business Review » ;
- sélecteur de période MBR (liste déroulante, avec boutons mois précédent / mois suivant) ;
- bouton « + Nouveau feedback » ;
- résumé de la période (ex. « 3 feedbacks pour 3 carriers ce mois-ci ») ;
- un bloc par carrier, avec son nombre de feedbacks ;
- pour chaque feedback : catégorie (badge), description, auteur, date et actions Modifier / Supprimer (icônes) ;
- pour un carrier sans feedback : le message « Aucun feedback ce mois-ci pour ce carrier. »

#### Historique (`/feedbacks`)

- titre et sous-titre « Retrouvez un ancien retour, toutes périodes confondues. » ;
- bouton « + Nouveau feedback » ;
- filtres carrier, période MBR et catégorie (listes déroulantes, par défaut « Tous les carriers », « Toutes les périodes », « Toutes les catégories ») et bouton « Réinitialiser » ;
- nombre de feedbacks affichés ;
- bascule d'affichage : « Par période MBR » (par défaut, un groupe par mois) ou « Liste simple » ;
- pour chaque feedback : carrier, catégorie, description, auteur, date et actions Modifier / Supprimer (icônes).

### Pop-ups

#### Nouveau feedback

- carrier, période MBR (préremplie), catégorie, description ;
- auteur choisi dans un menu déroulant (« Sélectionner votre nom ») ;
- boutons « Annuler » et « Enregistrer », fermeture possible avec la croix ;
- le bouton « + Nouveau feedback » est le seul point d'entrée de la création (Home et Historique) : le carrier se choisit dans la pop-up.

#### Modification d'un feedback

- même formulaire que la création, prérempli ;
- date de création affichée (« Créé le 18/09/2026 »), non modifiable ;
- boutons « Annuler » et « Enregistrer les modifications ».

#### Confirmation de suppression

- titre « Supprimer ce feedback ? » et avertissement : l'action est définitive ;
- rappel du feedback concerné (carrier · catégorie · période MBR, puis description) ;
- boutons « Annuler » et « Supprimer ».

### Maquettes

Les captures sont rangées dans `docs/maquettes/`.

| Écran | Fichier |
|-------|---------|
| Home | `Home_page.png` |
| Historique | `History_of_feedbacks.png` |
| Nouveau feedback | `New_feedback_popup.png` |
| Modification d'un feedback | `Edit_a_feedback_popup.png` |
| Confirmation de suppression | `Delete_a_feedback.png` |

![Page Home](docs/maquettes/Home_page.png)

![Page Historique](docs/maquettes/History_of_feedbacks.png)

---

## Routes

### Routes front-end (pages)

| Route | Page | Rôle |
|-------|------|------|
| `/` | Home | Préparer la MBR : choisir une période et voir les feedbacks regroupés par carrier |
| `/feedbacks` | Historique | Consulter tous les feedbacks avec filtres |

Les trois pop-ups (création, modification, suppression) ne sont pas des routes : ce sont des composants qui s'ouvrent par-dessus ces deux pages.

Le mois sélectionné et les filtres sont conservés dans l'URL (paramètres de requête), ce qui permet de retrouver ou de partager une vue précise sans ajouter de route :

```text
/?mbr=2026-09
/feedbacks?carrier=1&mbr=2026-09&category=Communication
```

### Routes back-end (API)

Ces routes seront implémentées en **phase 3**, avec PostgreSQL. Pendant le prototype (phase 2), les pages lisent des données de démonstration via la couche d'accès `src/lib/data`, qui reprend la même logique que ces routes pour faciliter le passage à la base de données.

| Méthode | Route | Rôle |
|---------|-------|------|
| `GET` | `/api/carriers` | Lister les carriers |
| `GET` | `/api/authors` | Lister les auteurs (alimente le menu déroulant) |
| `GET` | `/api/feedbacks` | Lister les feedbacks, avec filtres optionnels `carrierId`, `mbrPeriod`, `category` |
| `POST` | `/api/feedbacks` | Créer un feedback |
| `GET` | `/api/feedbacks/:id` | Récupérer un feedback |
| `PUT` | `/api/feedbacks/:id` | Modifier un feedback |
| `DELETE` | `/api/feedbacks/:id` | Supprimer un feedback |

Exemple de requête pour la page d'accueil (feedbacks de septembre 2026) :

```text
GET /api/feedbacks?mbrPeriod=2026-09
```

Le regroupement par carrier est fait côté front-end, à partir des résultats de `/api/carriers` et `/api/feedbacks`. Cela permet d'afficher aussi les carriers sans feedback sur la période.

---

## Exemple de donnée

```json
{
  "carrierId": 1,
  "authorId": 1,
  "mbrPeriod": "2026-09",
  "category": "Communication",
  "description": "Plusieurs relances ont été nécessaires avant d'obtenir une réponse.",
  "createdAt": "2026-09-18T10:25:00Z"
}
```

---

## Architecture technique

### Front-end

- SvelteKit
- TypeScript

### Stockage

- **Prototype :** JSON local, pour développer les écrans
- **Utilisation réelle :** PostgreSQL, car plusieurs personnes doivent alimenter les mêmes données

### Gestion du code

- Git
- GitHub

---

## Structure du projet

```text
carrier-feedback-app/
├── src/
│   ├── lib/
│   │   ├── components/
│   │   │   ├── FeedbackForm.svelte
│   │   │   └── ConfirmDelete.svelte
│   │   ├── data/
│   │   └── types/
│   └── routes/
│       ├── +page.svelte
│       ├── feedbacks/
│       │   └── +page.svelte
│       └── api/                      (phase 3)
│           ├── carriers/
│           │   └── +server.ts
│           ├── authors/
│           │   └── +server.ts
│           └── feedbacks/
│               ├── +server.ts
│               └── [id]/
│                   └── +server.ts
├── docs/
│   └── maquettes/                (captures des maquettes)
├── static/
├── package.json
└── README.md
```

Le composant `FeedbackForm.svelte` sert à la fois à la création et à la modification.

---

## Installation

```bash
npm install
npm run dev
```

---

## Commandes utiles

```bash
npm run dev
npm run build
npm run preview
npm run check
```

---

## Roadmap

### Phase 1 - Cadrage

- [x] Définir le besoin
- [x] Définir le MVP
- [x] Réaliser les maquettes

### Phase 2 - Prototype Front-End

- [ ] Initialiser le projet SvelteKit
- [ ] Créer les composants (formulaire, confirmation de suppression)
- [ ] Créer les 2 pages principales
- [ ] Utiliser des données de démonstration

### Phase 3 - Persistance partagée

- [ ] Mettre en place PostgreSQL
- [ ] Créer le schéma de données (Carrier, Author, Feedback)
- [ ] Créer les routes API (carriers, authors, feedbacks)
- [ ] Sauvegarder les feedbacks
- [ ] Héberger l'application pour que les équipes y accèdent

### Phase 4 - Améliorations

- [ ] Authentification (renseigner l'auteur automatiquement)
- [ ] Gestion des rôles
- [ ] Filtre par auteur

---

## Hors périmètre du MVP

- Authentification Microsoft
- Notifications Teams
- Intégration Outlook
- Intégration SharePoint
- Génération PowerPoint
- Intelligence artificielle
- Statistiques avancées
- Page dédiée à la liste des carriers
- Champ « équipe » (seules les personnes de Maersk LNS utilisent l'application)

---

## Principes de conception

1. Simplicité
2. Rapidité de saisie
3. Traçabilité
4. Évolutivité
5. Confidentialité

---

## Sécurité et confidentialité

Les données utilisées pendant le développement doivent :

- être fictives ;
- être anonymisées ;
- ne contenir aucune donnée métier confidentielle.

Sans authentification, l'auteur est choisi par la personne qui saisit le feedback : le nom affiché n'est donc pas vérifié. Ce choix est acceptable pour un outil interne au MVP, et l'authentification pourra le verrouiller ensuite.

---

## Licence

Projet personnel destiné à l'apprentissage et au prototypage.