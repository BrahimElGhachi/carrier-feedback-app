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
| US-05 | En tant qu'utilisateur, je souhaite filtrer l'historique des feedbacks. | Filtre par carrier, période MBR et catégorie, combinables. |

---

## Écrans du MVP

L'application compte **2 pages** et **3 pop-ups**.

### Pages

#### Préparer la MBR (page d'accueil, `/`)

- titre « Carrier Performance – Monthly Business Review » ;
- sélecteur de période MBR (avec passage au mois précédent ou suivant) ;
- un bloc par carrier avec ses feedbacks, l'auteur et les actions Modifier / Supprimer ;
- bouton « Nouveau feedback » et bouton « Ajouter » par carrier.

#### Historique (`/feedbacks`)

- historique complet des feedbacks ;
- filtres de recherche (carrier, période MBR, catégorie) ;
- affichage regroupé par période MBR ou en liste simple (optionnel).

### Pop-ups

#### Nouveau feedback

- carrier, période MBR, catégorie, description ;
- auteur choisi dans un menu déroulant.

#### Modification d'un feedback

- même formulaire que la création, prérempli ;
- date de création affichée, non modifiable.

#### Confirmation de suppression

- rappel du feedback concerné ;
- boutons « Annuler » et « Supprimer ».

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
│       └── feedbacks/
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
