# 🏆 Lootopia

**Plateforme de chasses au trésor numériques** - Projet Mastère Développement Full Stack

Lootopia est une application innovante mêlant géolocalisation, gamification et réalité augmentée pour créer des expériences de chasses au trésor interactives.

## 📋 Table des matières

- [Technologies](#-technologies)
- [Architecture](#-architecture)
- [Prérequis](#-prérequis)
- [Installation](#-installation)
- [Utilisation](#-utilisation)
- [Développement](#-développement)
- [Tests](#-tests)
- [Déploiement](#-déploiement)
- [Structure du projet](#-structure-du-projet)
- [Bonnes pratiques](#-bonnes-pratiques)

## 🚀 Technologies

### Frontend
- **Angular 21.x** - Framework web moderne
- **Angular Material 21.x** - Components UI Material Design
- **TypeScript 5.x** - Langage typé
- **SCSS** - Préprocesseur CSS
- **RxJS** - Programmation réactive

### Backend
- **NestJS 11.x** - Framework Node.js progressif
- **TypeORM** - ORM pour TypeScript
- **TypeScript 5.x** - Langage typé
- **Passport** - Authentification (JWT, Local)
- **Class Validator** - Validation des DTOs
- **bcrypt** - Hash des mots de passe
- **Winston** - Logging

### Base de données
- **MariaDB 11.6** - Base de données relationnelle

### DevOps & Outils
- **pnpm** - Gestionnaire de paquets performant
- **Docker & Docker Compose** - Conteneurisation
- **ESLint** - Linter JavaScript/TypeScript
- **Prettier** - Formatteur de code
- **Jest** - Tests unitaires
- **Vitest** - Tests pour Angular

## 🏗️ Architecture

Le projet est organisé en **monorepo** avec pnpm workspace :

```
lootopia/
├── apps/
│   ├── frontend/          # Application Angular
│   └── backend/           # API NestJS
├── packages/              # Packages partagés (futur)
├── scripts/               # Scripts utilitaires
└── docker-compose.yml     # Orchestration des services
```

### Architecture technique

**Frontend (Angular)**
- Architecture modulaire par fonctionnalités
- Services centralisés dans `core/`
- Components réutilisables dans `shared/`
- Features organisées par domaine métier
- Mobile-first responsive design

**Backend (NestJS)**
- Architecture modulaire
- RESTful API avec préfixe `/api`
- Authentification JWT
- Validation automatique des DTOs
- CORS configuré pour le frontend

## 📦 Prérequis

- **Node.js** >= 20.0.0
- **pnpm** >= 8.0.0
- **Docker** >= 20.10 (optionnel)
- **Docker Compose** >= 2.0 (optionnel)

### Installation de pnpm

```bash
npm install -g pnpm
# ou
corepack enable
```

## 🔧 Installation

### 1. Cloner le dépôt

```bash
git clone <repository-url>
cd Lootopia
```

### 2. Installer les dépendances

```bash
# Installer toutes les dépendances du monorepo
pnpm install
```

### 3. Configuration des variables d'environnement

```bash
# Copier les fichiers d'exemple
cp .env.example .env
cp apps/backend/.env.example apps/backend/.env
```

Modifier les fichiers `.env` selon vos besoins.

## 🎮 Utilisation

### Développement local (avec Docker)

```bash
# Démarrer tous les services (base de données, backend, frontend)
docker-compose up

# Ou en arrière-plan
docker-compose up -d
```

Accès aux services :
- **Frontend** : http://localhost:4200
- **Backend API** : http://localhost:3000/api
- **Base de données** : localhost:3306

### Développement local (sans Docker)

#### 1. Démarrer MariaDB localement

```bash
# Installer et démarrer MariaDB selon votre OS
# Créer la base de données 'lootopia'
```

#### 2. Démarrer le backend

```bash
cd apps/backend
pnpm run start:dev
```

#### 3. Démarrer le frontend

```bash
cd apps/frontend
pnpm run dev
```

### Scripts disponibles

#### Racine du projet

```bash
pnpm dev                    # Démarrer frontend + backend
pnpm build                  # Build tous les projets
pnpm test                   # Lancer tous les tests
pnpm lint                   # Linter tous les projets
pnpm format                 # Formater le code avec Prettier
```

#### Backend

```bash
pnpm --filter @lootopia/backend start:dev     # Mode développement
pnpm --filter @lootopia/backend start:debug   # Mode debug
pnpm --filter @lootopia/backend build         # Build production
pnpm --filter @lootopia/backend test          # Tests unitaires
pnpm --filter @lootopia/backend test:e2e      # Tests e2e
```

#### Frontend

```bash
pnpm --filter @lootopia/frontend dev          # Mode développement
pnpm --filter @lootopia/frontend build        # Build production
pnpm --filter @lootopia/frontend test         # Tests
pnpm --filter @lootopia/frontend lint         # Linter
```

## 👨‍💻 Développement

### Conventions de code

- **TypeScript strict mode** activé
- **ESLint** pour la qualité du code
- **Prettier** pour le formatage
- **Commits conventionnels** recommandés

### Bonnes pratiques

#### Sécurité (OWASP)
- ✅ Mots de passe hashés avec bcrypt
- ✅ Authentification JWT avec expiration
- ✅ Validation des entrées avec class-validator
- ✅ CORS configuré
- ✅ Variables sensibles dans .env (gitignored)

#### Clean Code
- ✅ Principes SOLID appliqués
- ✅ Code modulaire et réutilisable
- ✅ Typage fort avec TypeScript
- ✅ Documentation des fonctions complexes

#### Green IT
- ✅ Requêtes optimisées
- ✅ Lazy loading sur le frontend
- ✅ Compression gzip configurée (nginx)

### Structure recommandée pour les features

#### Backend (NestJS)

```
src/
├── auth/
│   ├── auth.controller.ts
│   ├── auth.service.ts
│   ├── auth.module.ts
│   ├── strategies/
│   └── dto/
├── users/
│   ├── user.entity.ts
│   ├── users.controller.ts
│   ├── users.service.ts
│   ├── users.module.ts
│   └── dto/
└── common/
    ├── decorators/
    ├── guards/
    ├── filters/
    └── interceptors/
```

#### Frontend (Angular)

```
src/app/
├── core/
│   ├── services/
│   ├── guards/
│   ├── interceptors/
│   └── models/
├── shared/
│   └── components/
└── features/
    ├── auth/
    ├── treasures/
    └── home/
```

## 🧪 Tests

### Tests unitaires

```bash
# Backend
pnpm --filter @lootopia/backend test
pnpm --filter @lootopia/backend test:watch
pnpm --filter @lootopia/backend test:cov

# Frontend
pnpm --filter @lootopia/frontend test
```

### Tests e2e

```bash
pnpm --filter @lootopia/backend test:e2e
```

### Couverture de code

```bash
pnpm test:cov
```

## 🚢 Déploiement

### Build de production

```bash
# Build tous les projets
pnpm build

# Ou individuellement
pnpm --filter @lootopia/backend build
pnpm --filter @lootopia/frontend build
```

### Docker Production

```bash
# Build des images
docker-compose -f docker-compose.prod.yml build

# Déployer
docker-compose -f docker-compose.prod.yml up -d
```

### Variables d'environnement production

⚠️ **Important** : Modifier les valeurs suivantes en production :
- `JWT_SECRET` : Clé secrète forte
- `DB_PASSWORD` : Mot de passe fort
- `CORS_ORIGIN` : Domaine du frontend

## 📁 Structure du projet

```
lootopia/
├── apps/
│   ├── backend/                    # API NestJS
│   │   ├── src/
│   │   │   ├── auth/              # Module authentification
│   │   │   ├── users/             # Module utilisateurs
│   │   │   ├── treasures/         # Module chasses au trésor
│   │   │   ├── common/            # Utilitaires partagés
│   │   │   ├── app.module.ts      # Module racine
│   │   │   └── main.ts            # Point d'entrée
│   │   ├── test/                  # Tests e2e
│   │   ├── Dockerfile
│   │   └── package.json
│   │
│   └── frontend/                   # Application Angular
│       ├── src/
│       │   ├── app/
│       │   │   ├── core/          # Services, guards, interceptors
│       │   │   ├── shared/        # Composants réutilisables
│       │   │   └── features/      # Modules par fonctionnalité
│       │   ├── environments/      # Configuration par environnement
│       │   └── styles.scss        # Styles globaux
│       ├── Dockerfile
│       ├── nginx.conf
│       └── package.json
│
├── packages/                       # Packages partagés (futur)
├── scripts/                        # Scripts utilitaires
│   └── init-db.sql                # Initialisation base de données
│
├── .env                           # Variables d'environnement
├── .gitignore
├── .prettierrc                    # Configuration Prettier
├── docker-compose.yml             # Orchestration Docker
├── eslint.config.mjs              # Configuration ESLint
├── pnpm-workspace.yaml            # Configuration workspace
├── package.json                   # Package racine
└── README.md
```

## 🎯 Fonctionnalités prévues

### Phase 1 - MVP (M1)
- [ ] Authentification JWT (inscription, connexion)
- [ ] Gestion des profils utilisateurs
- [ ] CRUD chasses au trésor
- [ ] Carte interactive (Google Maps / Leaflet)
- [ ] Action "Creuser" sur les points
- [ ] Réalité augmentée basique (AR.js)
- [ ] Système de points/progression

### Phase 2 - Extensions (M2)
- [ ] Monnaie virtuelle (Couronnes)
- [ ] Système de notifications
- [ ] Multi-Factor Authentication (MFA)
- [ ] Géolocalisation avancée (PostGIS)
- [ ] WebSocket pour interactions temps réel
- [ ] Analytics et monitoring (Prometheus + Grafana)

## 📚 Documentation technique

### API Backend

L'API backend est accessible sur `/api` et retourne du JSON.

**Endpoints principaux** :
- `GET /api` - Health check
- `POST /api/auth/register` - Inscription
- `POST /api/auth/login` - Connexion
- `GET /api/users/profile` - Profil utilisateur (protégé)

### Base de données

**Schéma initial** (géré par TypeORM) :
- `users` - Utilisateurs
- `treasures` - Chasses au trésor
- `waypoints` - Points de passage
- `user_progress` - Progression des utilisateurs

## 🤝 Contribution

1. Créer une branche (`git checkout -b feature/AmazingFeature`)
2. Commit les changements (`git commit -m 'Add AmazingFeature'`)
3. Push vers la branche (`git push origin feature/AmazingFeature`)
4. Ouvrir une Pull Request

## 📄 Licence

Ce projet est sous licence MIT.

## 👥 Équipe

**Projet Mastère Développement Full Stack**
Agence fictive : **Out of Cache**

---

**Stack complète** : Angular 21 + NestJS 11 + MariaDB 11 + TypeScript 5 + Docker
**Architecture** : Monorepo avec pnpm workspace
**Approche** : Mobile-first, API-first, Security by design
