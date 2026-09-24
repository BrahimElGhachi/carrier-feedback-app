# Carrier Feedback App

Application interne de collecte, de centralisation et de suivi des feedbacks concernant les compagnies maritimes (*carriers*).

> **Statut du projet :** phase de cadrage / MVP  
> **Type de projet :** projet personnel d'apprentissage inspiré d'un besoin métier réel  
> **Technologie envisagée :** SvelteKit

---

## 1. Contexte

Chaque mois, notre Control Tower au NL envoie un e-mail demandant aux équipes CX de compléter les supports des réunions MBR (*Monthly Business Review*) avec les différents carriers.

Le processus actuel consiste à :

1. recevoir un lien vers un espace SharePoint interne ;
2. sélectionner une compagnie maritime ;
3. ouvrir le dossier correspondant au mois concerné ;
4. ouvrir le PowerPoint du MBR ;
5. compléter la zone correspondant à son équipe, par exemple **Maersk LNS Destination SWE** ;
6. laisser la Control Tower utiliser ces informations pendant le Carrier MBR.

La difficulté principale ne concerne pas le PowerPoint lui-même. Elle concerne la collecte préalable des informations auprès des collègues :

- les retours peuvent être dispersés entre Teams, les e-mails et les échanges oraux ;
- les collaborateurs ne contribuent pas toujours spontanément ;
- plusieurs relances peuvent être nécessaires ;
- les anciens feedbacks sont difficiles à retrouver ;
- les problèmes récurrents par carrier sont difficiles à identifier ;
- la compilation mensuelle reste manuelle.

---

## 2. Objectif du projet

L'objectif de **Carrier Feedback App** est de proposer un point d'entrée unique permettant aux collaborateurs de :

- saisir facilement un feedback concernant un carrier ;
- enregistrer les informations au fil de l'eau, sans attendre la demande mensuelle ;
- consulter les feedbacks déjà transmis ;
- retrouver les sujets des mois précédents ;
- identifier les problèmes récurrents ;
- préparer plus rapidement la synthèse à reporter dans le PowerPoint MBR.

Dans la première version, l'application **ne remplace pas** le PowerPoint et ne modifie pas le processus officiel de la Control Tower. Elle constitue un outil complémentaire de collecte et de préparation.

---

## 3. Utilisateurs concernés

### Contributeur

Un collègue qui souhaite enregistrer un retour concernant un carrier.

Le contributeur peut notamment :

- sélectionner un carrier ;
- décrire un problème, une amélioration ou un point positif ;
- préciser la catégorie et l'impact ;
- consulter les feedbacks précédents ;
- modifier ses propres contributions tant qu'elles ne sont pas clôturées.

### Coordinateur

La personne chargée de consolider les retours avant le MBR.

Le coordinateur peut notamment :

- consulter tous les feedbacks ;
- filtrer les informations par carrier, période, catégorie ou statut ;
- sélectionner les éléments à partager avec la Control Tower ;
- préparer une synthèse mensuelle ;
- marquer les feedbacks déjà reportés dans le PowerPoint.

> Pour le MVP personnel, les rôles pourront être simulés ou simplifiés. Une authentification d'entreprise ne sera envisagée qu'après validation technique et organisationnelle.

---

## 4. Proposition de valeur

L'application doit rendre la contribution plus simple que l'envoi d'un message libre sur Teams ou par e-mail.

Elle doit notamment permettre de :

- saisir un feedback en moins d'une minute ;
- conserver un historique structuré ;
- éviter les recherches dans plusieurs conversations ;
- afficher les anciens sujets lorsqu'un carrier est sélectionné ;
- distinguer un nouveau problème d'un sujet déjà remonté ;
- voir rapidement les contributions disponibles pour le prochain MBR ;
- préparer un contenu clair à copier dans le support de la Control Tower.

---

## 5. Périmètre du MVP

La première version doit rester volontairement simple.

### Inclus dans le MVP

- affichage de la liste des carriers ;
- création d'un feedback ;
- consultation de l'historique ;
- filtres par carrier, mois, catégorie et statut ;
- modification et suppression contrôlée d'un feedback ;
- vue de synthèse pour la préparation du MBR ;
- statut indiquant si un feedback a été reporté dans le PowerPoint.

### Hors périmètre initial

- génération automatique du PowerPoint ;
- modification directe des fichiers SharePoint ;
- notifications Teams automatiques ;
- intégration à Outlook ;
- connexion automatique avec un compte Microsoft professionnel ;
- statistiques avancées ou analyse par intelligence artificielle ;
- classement ou évaluation individuelle des collaborateurs.

Ces fonctionnalités pourront être étudiées après validation du MVP et des règles internes de sécurité.

---

## 6. Parcours utilisateur principal

### Ajouter un feedback

1. Le collaborateur ouvre l'application.
2. Le collaborateur clique sur **Ajouter un feedback**.
3. Le collaborateur sélectionne le carrier concerné.
4. L'application affiche les feedbacks récents associés à ce carrier.
5. Le collaborateur choisit une catégorie.
6. Le collaborateur décrit le fait observé et son impact opérationnel.
7. Le collaborateur indique éventuellement une action attendue.
8. Le collaborateur enregistre le feedback.
9. Le feedback apparaît dans l'historique et dans la synthèse du mois.

### Préparer le MBR

1. Le coordinateur sélectionne le mois et le carrier.
2. L'application affiche les feedbacks disponibles.
3. Le coordinateur identifie les sujets à remonter.
4. Le coordinateur copie une synthèse dans le PowerPoint officiel.
5. Le coordinateur marque les feedbacks concernés comme **Partagés avec la Control Tower**.

---

## 7. Données d'un feedback

Chaque feedback pourra contenir les champs suivants :

| Champ | Description | Obligatoire dans le MVP |
|---|---|---:|
| Carrier | Compagnie maritime concernée | Oui |
| Date de l'observation | Date du fait ou du problème | Oui |
| Auteur | Personne ayant saisi le feedback | Oui |
| Équipe / périmètre | Par exemple LNS Destination SWE | Oui |
| Catégorie | Communication, délai, capacité, documentation, process, facturation, autre | Oui |
| Type | Point négatif, point positif, suggestion | Oui |
| Description | Faits observés, formulés de manière précise | Oui |
| Impact | Faible, moyen ou élevé | Oui |
| Action attendue | Amélioration ou réponse souhaitée | Non |
| Sujet récurrent | Indique si le problème a déjà été remonté | Non |
| Statut | Brouillon, à revoir, prêt pour MBR, partagé, clôturé | Oui |
| Période MBR | Mois auquel rattacher le feedback | Oui |
| Date de création | Date technique d'enregistrement | Automatique |
| Date de modification | Dernière mise à jour | Automatique |

---

## 8. User stories du MVP

### US-01 : Ajouter un feedback

> En tant que contributeur, je veux enregistrer un feedback concernant un carrier afin que l'information soit conservée et disponible pour le prochain MBR.

**Critères d'acceptation :**

- un carrier doit être sélectionné ;
- une catégorie, un type, une description et un impact doivent être renseignés ;
- le feedback doit être enregistré avec sa date de création ;
- un message doit confirmer l'enregistrement.

### US-02 : Consulter l'historique d'un carrier

> En tant que contributeur, je veux consulter les feedbacks précédents d'un carrier afin d'éviter les doublons et d'identifier les sujets récurrents.

**Critères d'acceptation :**

- l'historique peut être filtré par carrier ;
- les résultats les plus récents apparaissent en premier ;
- le mois, la catégorie, le statut et la description sont visibles.

### US-03 : Modifier un feedback

> En tant que contributeur, je veux corriger ou compléter mon feedback afin de maintenir une information fiable.

**Critères d'acceptation :**

- les données existantes sont préremplies ;
- la date de modification est mise à jour ;
- un feedback clôturé ne peut pas être modifié sans droit adapté.

### US-04 : Filtrer les feedbacks

> En tant que coordinateur, je veux filtrer les feedbacks afin de préparer rapidement la synthèse d'un carrier pour une période donnée.

**Critères d'acceptation :**

- filtres disponibles : carrier, période MBR, catégorie et statut ;
- plusieurs filtres peuvent être combinés ;
- les filtres peuvent être réinitialisés.

### US-05 : Préparer la synthèse mensuelle

> En tant que coordinateur, je veux afficher les feedbacks prêts pour le MBR afin de reporter les informations pertinentes dans le PowerPoint de la Control Tower.

**Critères d'acceptation :**

- la vue est organisée par carrier ;
- seuls les feedbacks pertinents pour la période choisie sont affichés ;
- le contenu peut être facilement sélectionné et copié ;
- le feedback peut être marqué comme partagé.

### US-06 : Identifier les sujets récurrents

> En tant que coordinateur, je veux consulter les retours précédents d'un carrier afin de repérer les problèmes déjà remontés.

**Critères d'acceptation :**

- les anciens feedbacks restent accessibles ;
- un feedback peut être relié à un sujet précédent ;
- le statut du sujet précédent est visible.

---

## 9. Écrans envisagés

### 1. Tableau de bord

- feedbacks du mois ;
- feedbacks prêts pour le MBR ;
- carriers ayant des contributions ;
- accès rapide au formulaire de saisie.

### 2. Nouveau feedback

Formulaire court et guidé avec affichage des sujets récents du carrier sélectionné.

### 3. Historique

Liste filtrable de tous les feedbacks autorisés, avec accès au détail.

### 4. Détail d'un carrier

- informations du carrier ;
- feedbacks récents ;
- sujets récurrents ;
- filtres par période et statut.

### 5. Préparation MBR

Vue de synthèse permettant de sélectionner les commentaires à reporter dans le PowerPoint.

### 6. Administration

Gestion simple des carriers, catégories et statuts. Cet écran peut être reporté après le MVP.

---

## 10. Modèle de données initial

### Entité `Carrier`

```text
id
name
active
createdAt
updatedAt
```

### Entité `Feedback`

```text
id
carrierId
authorName
team
observationDate
mbrPeriod
category
type
description
impact
expectedAction
isRecurring
relatedFeedbackId
status
createdAt
updatedAt
```

### Entité `User` à prévoir ultérieurement

```text
id
name
email
role
team
active
createdAt
updatedAt
```

---

## 11. Architecture technique envisagée

### Première étape : prototype local

- **Framework :** SvelteKit
- **Langage :** JavaScript ou TypeScript
- **Style :** CSS natif dans un premier temps
- **Données :** fichier JSON ou stockage local pour valider les écrans
- **Gestion du code :** Git

### Étape suivante : application avec persistance

- **Front-end et serveur :** SvelteKit
- **Base de données envisagée :** PostgreSQL, sous réserve de disponibilité et d'autorisation
- **ORM éventuel :** Prisma ou Drizzle, à choisir après le prototype
- **Validation des formulaires :** à définir
- **Authentification :** à définir selon l'environnement autorisé

> Les choix liés à l'hébergement, à l'authentification professionnelle, à SharePoint, à Microsoft Graph et au stockage de données internes devront être validés avant toute utilisation réelle en entreprise.

---

## 12. Structure envisagée du projet

```text
carrier-feedback-app/
├── src/
│   ├── lib/
│   │   ├── components/
│   │   ├── data/
│   │   ├── services/
│   │   └── types/
│   └── routes/
│       ├── +page.svelte
│       ├── feedbacks/
│       │   ├── +page.svelte
│       │   ├── new/
│       │   │   └── +page.svelte
│       │   └── [id]/
│       │       └── +page.svelte
│       ├── carriers/
│       │   ├── +page.svelte
│       │   └── [id]/
│       │       └── +page.svelte
│       └── mbr/
│           └── +page.svelte
├── static/
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

Cette arborescence est indicative et pourra évoluer pendant le développement.

---

## 13. Installation envisagée

Une fois le projet SvelteKit créé :

```bash
npm install
npm run dev
```

L'application de développement sera accessible à l'adresse indiquée dans le terminal.

### Commandes utiles

```bash
npm run dev
npm run build
npm run preview
npm run check
```

---

## 14. Roadmap

### Phase 1 : cadrage

- [x] Définir le problème métier
- [x] Identifier le rôle de l'application par rapport au PowerPoint
- [x] Définir les utilisateurs principaux
- [x] Rédiger les premières user stories
- [ ] Valider les champs du formulaire
- [ ] Réaliser les premières maquettes

### Phase 2 : prototype front-end

- [ ] Initialiser le projet SvelteKit
- [ ] Créer la navigation
- [ ] Créer la liste des carriers
- [ ] Créer le formulaire de feedback
- [ ] Utiliser des données de démonstration non confidentielles
- [ ] Créer l'historique et les filtres
- [ ] Créer la vue de préparation MBR

### Phase 3 : persistance des données

- [ ] Choisir la base de données
- [ ] Créer le schéma de données
- [ ] Enregistrer les feedbacks
- [ ] Gérer la modification et la suppression
- [ ] Ajouter les contrôles de validation

### Phase 4 : sécurité et usage réel

- [ ] Vérifier les règles internes applicables
- [ ] Définir les droits d'accès
- [ ] Choisir une méthode d'authentification autorisée
- [ ] Déterminer une solution d'hébergement autorisée
- [ ] Tester avec un petit groupe de volontaires

### Phase 5 : améliorations possibles

- [ ] rappels automatiques ;
- [ ] notifications Teams ;
- [ ] export de synthèse ;
- [ ] détection manuelle ou assistée des sujets récurrents ;
- [ ] préparation semi-automatique du contenu MBR ;
- [ ] intégration SharePoint, uniquement si elle est autorisée.

---

## 15. Principes de conception

L'application devra respecter les principes suivants :

1. **Simplicité** : le formulaire doit être rapide à comprendre et à remplir.
2. **Utilité immédiate** : les anciens feedbacks doivent aider à produire le nouveau retour.
3. **Traçabilité** : la date, l'auteur et le statut doivent être conservés.
4. **Neutralité** : les feedbacks doivent décrire des faits et des impacts opérationnels.
5. **Confidentialité** : aucune donnée professionnelle réelle ne doit être utilisée dans un prototype non validé.
6. **Complémentarité** : le MVP aide à préparer le MBR sans remplacer le support officiel.
7. **Évolutivité** : l'architecture doit permettre d'ajouter ultérieurement des notifications et des exports.

---

## 16. Exemple de feedback

> Les exemples utilisés pendant le développement doivent être fictifs ou anonymisés.

```json
{
  "carrier": "Carrier Demo",
  "observationDate": "2026-09-15",
  "mbrPeriod": "2026-09",
  "authorName": "Utilisateur Demo",
  "team": "LNS Destination SWE",
  "category": "Communication",
  "type": "Point négatif",
  "description": "Plusieurs relances ont été nécessaires pour obtenir une confirmation opérationnelle.",
  "impact": "Moyen",
  "expectedAction": "Améliorer le délai de réponse et clarifier le point de contact.",
  "isRecurring": true,
  "status": "Prêt pour MBR"
}
```

---

## 17. Indicateurs de réussite du MVP

Le MVP sera considéré comme utile s'il permet de :

- saisir un feedback sans passer par un message libre ;
- retrouver facilement les retours d'un carrier ;
- préparer une synthèse mensuelle à partir des données enregistrées ;
- distinguer les sujets nouveaux des sujets récurrents ;
- voir quels feedbacks ont déjà été partagés avec la Control Tower ;
- réduire les informations perdues ou dispersées.

Aucun indicateur ne doit être utilisé pour noter, classer ou évaluer la performance individuelle des collaborateurs.

---

## 18. Sécurité et confidentialité

Ce projet étant inspiré d'un processus professionnel :

- ne pas copier de données confidentielles dans un dépôt personnel ou public ;
- ne pas utiliser de noms, adresses e-mail ou commentaires opérationnels réels dans les données de test ;
- conserver le dépôt en privé tant que le cadre d'utilisation n'est pas validé ;
- ne pas connecter l'application à SharePoint ou à un service Microsoft sans autorisation ;
- ne pas stocker de données professionnelles sur un service externe non approuvé ;
- valider les exigences de sécurité, d'accès et de conservation avant tout déploiement interne.

---

## 19. Questions encore ouvertes

- Qui pourra saisir un feedback ?
- Qui pourra voir l'ensemble des feedbacks ?
- Une contribution devra-t-elle pouvoir rester en brouillon ?
- Un collègue pourra-t-il modifier uniquement ses propres contributions ?
- Quelles catégories sont réellement utiles au métier ?
- Comment définir la période MBR d'un feedback saisi en avance ?
- Faut-il permettre de signaler qu'il n'y a aucun feedback pour un carrier ?
- Qui validera un feedback avant son partage avec la Control Tower ?
- Quelle solution de stockage et d'hébergement est autorisée dans l'environnement de travail ?

---

## 20. Nom de travail

**Carrier Feedback App** est le nom provisoire du projet.

Autres noms possibles :

- Carrier Voice
- Carrier Feedback Hub
- MBR Input Hub
- Carrier Insights
- Carrier Call-Outs

---

## Licence

Aucune licence publique n'est définie à ce stade. Le projet est destiné à l'apprentissage et au prototypage privé. Les éléments provenant de processus, documents ou environnements professionnels ne doivent pas être publiés.
