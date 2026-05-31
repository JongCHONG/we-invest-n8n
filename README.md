# Test technique n8n — Lead intake workflow

Ce dépôt contient un workflow n8n exécuté en local via Docker. Le workflow simule la réception d’un lead immobilier via un webhook, valide les données essentielles, enrichit les informations, applique une condition métier sur la priorité, puis envoie un email final avec le sujet **Hello there !** [1]

## Objectif

L’objectif est de démontrer un workflow simple, clair et exécutable localement, conforme aux attentes du test technique :

- déclenchement via webhook en `POST` ;
- réception des données au format JSON ;
- validation de champs essentiels ;
- enrichissement des données reçues ;
- condition métier sur la priorité ;
- envoi d’un email final ;
- export possible du workflow au format JSON ;
- exécution locale via Docker. [1]

## Stack

- Docker / Docker Compose
- n8n
- Gmail pour l’envoi d’email
- Postman pour les tests manuels

## Structure du workflow

Le workflow suit la structure suivante :

1. **Webhook** : réception d’un lead sur un endpoint `POST`.
2. **Validation** : vérification des champs obligatoires (`firstName`, `email`, `projectType`, `city`).
3. **Enrichissement** : ajout de `leadId`, `receivedAt`, `priority`, `summary`.
4. **Condition métier** : distinction entre lead prioritaire et lead standard.
5. **Préparation du message** : ajout du statut et construction du corps de mail.
6. **Envoi d’email** : envoi d’un message avec le sujet `Hello there !`. [1]

## Lancer le projet

### 1. Prérequis

- Docker installé
- Docker Compose disponible

### 2. Démarrage

```bash
docker compose up -d
```

### 3. Accès à n8n

Une fois le conteneur démarré, l’interface n8n est accessible à l’adresse suivante :

```text
http://localhost:5678
```

Le test technique attend un webhook local de type `http://localhost:5678/webhook/lead-intake`. [1]

## Test du workflow

### Mode test

Pendant le développement, le workflow peut être testé avec l’URL de test du webhook (`/webhook-test/...`) depuis Postman. Cela permet de visualiser directement l’exécution dans n8n.

### Exemple de requête

```bash
curl -X POST http://localhost:5678/webhook/lead-intake \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "John",
    "lastName": "Doe",
    "email": "john.doe@example.com",
    "source": "website",
    "projectType": "seller",
    "city": "Paris",
    "budget": 450000
  }'
```

Cet exemple reprend le format demandé dans l’énoncé. [1]

## Données traitées

### Champs attendus

Les champs principaux du lead sont les suivants :

- `firstName`
- `lastName`
- `email`
- `source`
- `projectType`
- `city`
- `budget` [1]

### Champs obligatoires

Les validations portent sur :

- `firstName`
- `email`
- `projectType`
- `city` [1]

### Données enrichies

Le workflow ajoute les informations suivantes :

- `leadId`
- `receivedAt`
- `priority`
- `summary` [1]

## Logique métier

La priorité est calculée avec une règle simple :

- si `projectType = seller` → priorité `high`
- sinon → priorité `normal` [1]

Ensuite, une condition métier permet de produire l’un des deux statuts suivants :

- `Lead prioritaire détecté`
- `Lead standard détecté` [1]

## Email final

Le workflow envoie un email contenant :

- le sujet `Hello there !`
- un résumé des informations du lead
- le statut calculé
- la date de réception
- l’identifiant du lead [1]

L’envoi réel de l’email fait partie des contraintes du test. [1]

## Credentials

Aucun credential sensible ne doit être commité dans ce dépôt ni exposé dans le JSON exporté du workflow. Le sujet précise explicitement que les credentials ne doivent pas être écrits en dur dans le workflow exporté. [1]

## Fichiers attendus dans le dépôt

Exemple de structure recommandée :

```text
.
├── README.md
├── docker-compose.yml
└── workflow/
    └── lead-intake-workflow.json
```

## Démonstration en entretien

Pendant l’entretien, la présentation peut suivre ce plan :

1. lancement de n8n en local ;
2. explication du webhook ;
3. test du workflow ;
4. validation des données ;
5. enrichissement ;
6. calcul de la priorité ;
7. configuration de l’email ;
8. gestion des erreurs ;
9. pistes d’amélioration en production. [1]

## Améliorations possibles

Si ce workflow devait être utilisé en production, plusieurs améliorations seraient pertinentes :

- validation plus robuste du schéma d’entrée ;
- meilleure gestion des erreurs et des retries ;
- journalisation plus détaillée ;
- gestion sécurisée des secrets ;
- persistance des leads dans une base de données ;
- monitoring et alerting. [1]
