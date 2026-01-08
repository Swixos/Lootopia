# CI/CD Documentation - GitLab

Ce document décrit les pipelines d'intégration continue et de déploiement continu du projet Lootopia sur GitLab.

## 📋 Vue d'ensemble

Le projet utilise **GitLab CI/CD** pour automatiser :
- ✅ Tests et validation du code
- ✅ Build des applications
- ✅ Analyses de sécurité
- ✅ Déploiement en staging et production
- ✅ Génération de rapports de couverture

## 🔄 Stages du pipeline

Le pipeline GitLab est composé de 6 stages :

```
install → lint → test → build → security → deploy
```

### 1. **Install** - Installation des dépendances

- Installation via pnpm
- Cache intelligent basé sur `pnpm-lock.yaml`
- Artifacts : `node_modules/` pour les jobs suivants

### 2. **Lint** - Vérification du code

**Jobs** :
- `lint:eslint` - Vérification ESLint
- `lint:prettier` - Vérification du formatage

**Déclenchement** : MR, main, develop, claude/**

### 3. **Test** - Tests unitaires et e2e

**Jobs** :
- `test:backend:unit` - Tests unitaires backend (avec MariaDB)
- `test:backend:e2e` - Tests e2e backend
- `test:frontend:unit` - Tests unitaires frontend

**Features** :
- Service MariaDB 11.6 pour les tests backend
- Rapports de couverture (Cobertura + JUnit)
- Badge de couverture automatique
- Artifacts conservés 30 jours

### 4. **Build** - Compilation

**Jobs** :
- `build:backend` - Build NestJS (TypeScript → JavaScript)
- `build:frontend` - Build Angular (production optimisé)

**Artifacts** : Conservés 7 jours

### 5. **Security** - Analyses de sécurité

**Jobs** :
- `security:dependency-scan` - pnpm audit + outdated
- `security:secret-detection` - Détection de secrets dans l'historique git

**Optionnel (GitLab Ultimate)** :
- SAST (Static Application Security Testing)
- Secret Detection avancé
- Dependency Scanning
- License Scanning

### 6. **Deploy** - Déploiement

**Environnements** :
- `deploy:staging` - Déploiement staging (manuel sur develop)
- `deploy:production` - Déploiement production (manuel sur main ou tags)

**Features** :
- Déploiement via SSH
- Pull des images Docker depuis GitLab Container Registry
- Exécution des migrations automatique
- Environnements GitLab avec URLs

## 🐳 Images Docker

Les images Docker sont construites et poussées vers le GitLab Container Registry :

- `registry.gitlab.com/votre-groupe/lootopia/backend:latest`
- `registry.gitlab.com/votre-groupe/lootopia/backend:<commit-sha>`
- `registry.gitlab.com/votre-groupe/lootopia/frontend:latest`
- `registry.gitlab.com/votre-groupe/lootopia/frontend:<commit-sha>`

## 🎯 Règles de déclenchement

### Déclenchement automatique

Le pipeline s'exécute automatiquement sur :

- **Merge Requests** : Tous les stages sauf deploy
- **main branch** : Tous les stages + build Docker
- **develop branch** : Tous les stages
- **claude/** branches** : install, lint, test, build

### Déclenchement manuel

Les déploiements sont **toujours manuels** :
- Staging : Bouton manuel dans le pipeline
- Production : Bouton manuel dans le pipeline

## 🔧 Configuration requise

### Variables CI/CD GitLab

Allez dans **Settings > CI/CD > Variables** et ajoutez :

#### Variables de déploiement

```bash
# SSH
SSH_PRIVATE_KEY       # Clé SSH privée (type: File, Protected, Masked)
SSH_KNOWN_HOSTS       # Contenu du known_hosts
DEPLOY_USER           # Utilisateur SSH (ex: deploy)

# Staging
DEPLOY_HOST_STAGING   # Serveur staging (ex: staging.lootopia.com)

# Production
DEPLOY_HOST_PRODUCTION # Serveur production (ex: lootopia.com)
```

#### Variables automatiques (fournies par GitLab)

- `CI_REGISTRY` - Registry GitLab
- `CI_REGISTRY_USER` - Username registry
- `CI_REGISTRY_PASSWORD` - Password registry
- `CI_COMMIT_SHORT_SHA` - SHA court du commit
- `CI_DEFAULT_BRANCH` - Branche principale

### Générer les clés SSH

```bash
# Sur votre machine locale
ssh-keygen -t ed25519 -C "gitlab-ci@lootopia" -f gitlab-ci-key

# Copier la clé publique sur le serveur
ssh-copy-id -i gitlab-ci-key.pub deploy@your-server.com

# Ajouter la clé privée dans GitLab CI/CD Variables
cat gitlab-ci-key  # Copier le contenu complet

# Générer SSH_KNOWN_HOSTS
ssh-keyscan your-server.com > known_hosts
cat known_hosts  # Copier le contenu
```

## 📊 Rapports et Artifacts

### Couverture de code

Les rapports de couverture sont automatiquement affichés dans les MR :

- Badge de couverture dans le pipeline
- Diff de couverture dans les MR
- Rapports Cobertura et JUnit

### Artifacts disponibles

| Artifact | Durée | Description |
|----------|-------|-------------|
| `node_modules/` | 1 heure | Dépendances installées |
| `coverage/` | 30 jours | Rapports de couverture |
| `dist/` | 7 jours | Builds compilés |
| `test-results/` | 7 days | Résultats des tests e2e |

### Télécharger les artifacts

```bash
# Via l'interface GitLab
Pipeline > Jobs > Browse artifacts

# Via l'API
curl --header "PRIVATE-TOKEN: <your_access_token>" \
  "https://gitlab.com/api/v4/projects/:id/jobs/:job_id/artifacts"
```

## 🚀 Utilisation

### Lancer un pipeline manuellement

1. Aller sur **CI/CD > Pipelines**
2. Cliquer sur **Run pipeline**
3. Sélectionner la branche
4. Cliquer sur **Run pipeline**

### Déployer en staging

1. Aller sur **CI/CD > Pipelines**
2. Sélectionner le pipeline de la branche `develop`
3. Dans le stage **deploy**, cliquer sur le bouton ▶️ de `deploy:staging`
4. Confirmer le déploiement

### Déployer en production

**Méthode 1 : Depuis main**

1. Merger votre MR dans `main`
2. Aller sur le pipeline de `main`
3. Cliquer sur ▶️ `deploy:production`

**Méthode 2 : Via tag de version**

```bash
# Créer un tag de version
git tag v1.0.0
git push origin v1.0.0

# Un pipeline se déclenche automatiquement
# Cliquer sur ▶️ deploy:production dans le pipeline
```

## 🔍 Debugging des pipelines

### Pipeline échoue

**1. Consulter les logs**
```
CI/CD > Pipelines > Cliquer sur le pipeline > Cliquer sur le job
```

**2. Reproduire localement**
```bash
# Installer les dépendances
pnpm install

# Tests
pnpm test
pnpm --filter @lootopia/backend test:e2e

# Lint
pnpm lint
pnpm format:check

# Build
pnpm build
```

**3. Vider le cache**
```
CI/CD > Pipelines > Clear runner caches
```

### Job timeout

Si un job prend trop de temps :

1. **Settings > CI/CD > General pipelines**
2. Augmenter **Timeout** (défaut: 1h)

### Problèmes de cache

```bash
# Forcer la regénération du cache
# Modifier pnpm-lock.yaml ou ajouter une variable
CI_CACHE_BUST="$(date +%s)"
```

## 📈 Optimisations

### Cache pnpm

Le cache est basé sur `pnpm-lock.yaml` :

```yaml
cache:
  key:
    files:
      - pnpm-lock.yaml
  paths:
    - .pnpm-store/
    - node_modules/
```

**Gains** : 50-80% de réduction du temps d'installation

### Parallélisation

Les jobs indépendants s'exécutent en parallèle :

```
lint:eslint ─┐
lint:prettier ┼─> test:backend:unit ─┐
              │   test:backend:e2e   ├─> build:backend
              └─> test:frontend:unit ─┘   build:frontend
```

### Runners partagés vs dédiés

- **Runners partagés** : Gratuits, limités
- **Runners dédiés** : Plus rapides, illimités

Pour ajouter un runner dédié :
```
Settings > CI/CD > Runners > New project runner
```

## 🔐 Sécurité

### GitLab Ultimate (optionnel)

Si vous avez GitLab Ultimate, décommentez dans `.gitlab-ci.yml` :

```yaml
include:
  - template: Security/SAST.gitlab-ci.yml
  - template: Security/Secret-Detection.gitlab-ci.yml
  - template: Security/Dependency-Scanning.gitlab-ci.yml
  - template: Security/License-Scanning.gitlab-ci.yml
```

### Protected branches

Configurer les branches protégées :

1. **Settings > Repository > Protected branches**
2. Protéger `main` et `develop` :
   - Allowed to merge: Maintainers
   - Allowed to push: No one
   - Require approval from code owners: ✅

### Protected variables

Les variables sensibles doivent être :
- **Protected** : Utilisables uniquement sur branches protégées
- **Masked** : Non visibles dans les logs

## 📊 Métriques et monitoring

### Afficher les métriques

```
CI/CD > Pipelines > Analytics
```

Métriques disponibles :
- Durée moyenne des pipelines
- Taux de succès
- Fréquence des déploiements
- Lead time for changes

### Badges

Ajouter les badges dans votre README :

```markdown
[![Pipeline Status](https://gitlab.com/votre-groupe/lootopia/badges/main/pipeline.svg)](https://gitlab.com/votre-groupe/lootopia/-/commits/main)
[![Coverage](https://gitlab.com/votre-groupe/lootopia/badges/main/coverage.svg)](https://gitlab.com/votre-groupe/lootopia/-/commits/main)
```

## 🎓 Ressources

- [GitLab CI/CD Documentation](https://docs.gitlab.com/ee/ci/)
- [GitLab CI/CD YAML Reference](https://docs.gitlab.com/ee/ci/yaml/)
- [GitLab Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/)
- [GitLab Environments](https://docs.gitlab.com/ee/ci/environments/)
- [pnpm CI Guide](https://pnpm.io/continuous-integration)

## 🎯 Roadmap

Améliorations futures :
- [ ] Tests de performance (Lighthouse CI)
- [ ] Tests de charge (k6, Gatling)
- [ ] Notifications Slack/Discord
- [ ] Rollback automatique
- [ ] Blue-green deployment
- [ ] Monitoring post-déploiement (Prometheus, Grafana)
- [ ] Review apps automatiques pour les MR
- [ ] Scheduled pipelines pour les tests de nuit
