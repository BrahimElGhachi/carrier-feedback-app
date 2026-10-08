# Carrier Feedback App

Application web permettant de centraliser les feedbacks concernant les compagnies maritimes (*carriers*).

> **Statut :** MVP en préparation  
> **Technologie cible :** SvelteKit  
> **Objectif :** projet personnel d'apprentissage inspiré d'un besoin métier réel

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
- de consulter les feedbacks existants ;
- de retrouver rapidement l'historique d'un carrier ;
- de disposer d'informations structurées et facilement exploitables.

---

## Fonctionnalités du MVP

### Gestion des feedbacks

- Créer un feedback
- Consulter les feedbacks
- Modifier un feedback
- Supprimer un feedback

### Recherche

- Filtrer par carrier
- Filtrer par période MBR
- Filtrer par catégorie

---

## Utilisateur cible

Le MVP est conçu pour un utilisateur unique.

L'utilisateur peut :

- sélectionner un carrier ;
- ajouter un feedback ;
- consulter les feedbacks existants ;
- modifier ses données ;
- supprimer un feedback.

---

## Modèle de données

### Carrier

```text
id
name
```

### Feedback

```text
id
carrierId
authorName
team
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
    +----< Feedback (N)
```

Un carrier peut posséder plusieurs feedbacks.

Chaque feedback est associé à un seul carrier.

---

## Backlog MVP

| ID | User Story | Priorité | Statut |
|----|-----------|-----------|---------|
| US-01 | Ajouter un feedback | Haute | 🔄 À faire |
| US-02 | Consulter les feedbacks | Haute | 🔄 À faire |
| US-03 | Modifier un feedback | Haute | 🔄 À faire |
| US-04 | Supprimer un feedback | Haute | 🔄 À faire |
| US-05 | Filtrer les feedbacks | Moyenne | 🔄 À faire |

---

## User Stories

| ID | Description | Critères d'acceptation |
|----|-------------|------------------------|
| US-01 | En tant qu'utilisateur, je souhaite enregistrer un feedback afin de conserver l'information. | Carrier, catégorie et description obligatoires. |
| US-02 | En tant qu'utilisateur, je souhaite consulter les feedbacks existants. | Liste des feedbacks visible. |
| US-03 | En tant qu'utilisateur, je souhaite modifier un feedback. | Données préremplies et sauvegardées. |
| US-04 | En tant qu'utilisateur, je souhaite supprimer un feedback. | Confirmation avant suppression. |
| US-05 | En tant qu'utilisateur, je souhaite filtrer les feedbacks. | Filtre par carrier, période MBR et catégorie. |

---

## Écrans du MVP

### Accueil

- accès rapide aux feedbacks ;
- accès au formulaire de création.

### Liste des feedbacks

- historique complet ;
- filtres de recherche.

### Nouveau feedback

- formulaire de création.

### Modification d'un feedback

- formulaire prérempli.

### Liste des carriers

- consultation des carriers disponibles.

---

## Exemple de donnée

```json
{
  "carrierId": 1,
  "authorName": "Brahim",
  "team": "LNS Destination SWE",
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

### Stockage initial

- JSON

### Stockage futur

- PostgreSQL

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
│   │   ├── data/
│   │   └── types/
│   └── routes/
│       ├── +page.svelte
│       ├── feedbacks/
│       └── carriers/
├── static/
├── package.json
└── README.md
```

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
- [ ] Réaliser les maquettes

### Phase 2 - Prototype Front-End

- [ ] Initialiser le projet SvelteKit
- [ ] Créer les composants
- [ ] Créer les pages principales
- [ ] Utiliser des données de démonstration

### Phase 3 - Persistance des données

- [ ] Mettre en place PostgreSQL
- [ ] Créer le schéma de données
- [ ] Sauvegarder les feedbacks

### Phase 4 - Améliorations

- [ ] Authentification
- [ ] Gestion des rôles
- [ ] Déploiement

---

## Hors périmètre du MVP

- Authentification Microsoft
- Notifications Teams
- Intégration Outlook
- Intégration SharePoint
- Génération PowerPoint
- Intelligence artificielle
- Statistiques avancées

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

---

## Licence

Projet personnel destiné à l'apprentissage et au prototypage.
``