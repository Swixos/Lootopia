# CI/CD Documentation

Ce document décrit les pipelines d'intégration continue et de déploiement continu du projet Lootopia.

## 📋 Vue d'ensemble

Le projet utilise **GitHub Actions** pour automatiser :
- ✅ Tests et validation du code
- ✅ Build des applications
- ✅ Analyses de sécurité
- ✅ Déploiement en production
- ✅ Mises à jour automatiques des dépendances

## 🔄 Pipelines disponibles

### 1. CI Pipeline (`.github/workflows/ci.yml`)

**Déclenchement** :
- Push sur `main`, `develop`, ou branches `claude/**`
- Pull requests vers `main` ou `develop`

**Étapes** :

1. **Install** : Installation et cache des dépendances pnpm
2. **Lint** : Vérification du code avec ESLint et Prettier
3. **Test Backend** :
   - Tests unitaires (Jest)
   - Tests e2e
   - Service MariaDB inclus
4. **Test Frontend** :
   - Tests unitaires (Vitest)
5. **Build Backend** : Compilation TypeScript
6. **Build Frontend** : Build de production Angular
7. **Build Docker** : Construction des images Docker (uniquement sur `main`)

**Artefacts générés** :
- `backend-coverage` : Couverture de code backend
- `frontend-coverage` : Couverture de code frontend
- `backend-build` : Build backend compilé
- `frontend-build` : Build frontend compilé

### 2. Deploy Pipeline (`.github/workflows/deploy.yml`)

**Déclenchement** :
- Manuel via `workflow_dispatch`
- Tags de version (`v*.*.*`)

**Étapes** :

1. **Build and Push** :
   - Build des images Docker optimisées
   - Push vers GitHub Container Registry (GHCR)
   - Tags automatiques (version, branch, SHA)

2. **Deploy** :
   - Déploiement vers l'environnement choisi
   - ⚠️ À configurer selon votre infrastructure

3. **Health Check** :
   - Vérification de l'état des services
   - ⚠️ À configurer avec vos URLs de production

**Environnements** :
- `production` : Environnement de production
- `staging` : Environnement de pré-production

### 3. Security Pipeline (`.github/workflows/security.yml`)

**Déclenchement** :
- Push sur `main` ou `develop`
- Pull requests
- Hebdomadaire (lundi 8h)
- Manuel

**Analyses** :

1. **Dependency Check** :
   - Audit des dépendances (`pnpm audit`)
   - Vérification des packages obsolètes

2. **Snyk Security** (optionnel) :
   - Scan des vulnérabilités
   - Nécessite `SNYK_TOKEN` dans les secrets

3. **CodeQL Analysis** :
   - Analyse statique du code
   - Détection de patterns de sécurité dangereux

4. **Secret Scan** :
   - Détection de secrets dans le code
   - Utilise TruffleHog

5. **License Check** :
   - Vérification des licences des dépendances

6. **OWASP Dependency Check** (optionnel) :
   - Scan complet OWASP
   - Désactivé par défaut (plus lourd)

## 🤖 Dependabot

Configuration dans `.github/dependabot.yml`

**Mises à jour automatiques** :
- Dépendances npm/pnpm (hebdomadaire)
- GitHub Actions (hebdomadaire)
- Images Docker (hebdomadaire)

**Pull Requests automatiques** :
- Créées chaque lundi à 8h
- Labellisées et assignées automatiquement
- Limitées pour éviter la surcharge

## 🔧 Configuration requise

### Secrets GitHub

#### Pour le déploiement (optionnel) :
```
DEPLOY_HOST       # Serveur de déploiement
DEPLOY_USER       # Utilisateur SSH
DEPLOY_KEY        # Clé SSH privée
```

#### Pour Snyk (optionnel) :
```
SNYK_TOKEN        # Token API Snyk
```

### Permissions requises

Le workflow nécessite les permissions suivantes :
- `contents: read` - Lecture du code
- `packages: write` - Push d'images Docker
- `security-events: write` - Écriture d'événements de sécurité

## 📦 Artifacts et Cache

### Cache pnpm
- Clé : `${{ runner.os }}-pnpm-store-${{ hashFiles('**/pnpm-lock.yaml') }}`
- Accélère les installations de 50-80%

### Artifacts conservés
- Builds : 7 jours
- Coverage : 7 jours
- Reports : 30 jours

## 🚀 Utilisation

### Lancer la CI manuellement

```bash
# Via GitHub CLI
gh workflow run ci.yml

# Via l'interface web
Actions > CI/CD Pipeline > Run workflow
```

### Déployer en production

```bash
# Via GitHub CLI
gh workflow run deploy.yml -f environment=production

# Via l'interface web
Actions > Deploy to Production > Run workflow > Select environment
```

### Créer une release

```bash
# Tag de version
git tag v1.0.0
git push origin v1.0.0

# Déclenche automatiquement le déploiement
```

## 🐛 Dépannage

### CI échoue sur les tests

1. Vérifier les logs de l'étape échouée
2. Reproduire localement :
   ```bash
   pnpm test
   pnpm --filter @lootopia/backend test:e2e
   ```

### Build Docker échoue

1. Vérifier le Dockerfile
2. Tester localement :
   ```bash
   docker-compose -f docker-compose.prod.yml build
   ```

### Cache pnpm invalide

1. Vider le cache :
   - Settings > Actions > Caches
   - Supprimer les caches obsolètes

### Secrets manquants

1. Vérifier dans Settings > Secrets and variables > Actions
2. Ajouter les secrets requis

## 📊 Badges pour le README

```markdown
![CI Status](https://github.com/Swixos/Lootopia/workflows/CI%2FCD%20Pipeline/badge.svg)
![Security](https://github.com/Swixos/Lootopia/workflows/Security%20Checks/badge.svg)
![Deploy](https://github.com/Swixos/Lootopia/workflows/Deploy%20to%20Production/badge.svg)
```

## 🔐 Bonnes pratiques

### Sécurité
- ✅ Ne jamais committer de secrets
- ✅ Utiliser les secrets GitHub pour les credentials
- ✅ Activer les analyses de sécurité
- ✅ Mettre à jour régulièrement les dépendances

### Performance
- ✅ Utiliser le cache pnpm
- ✅ Paralléliser les jobs indépendants
- ✅ Limiter les artefacts conservés

### Maintenance
- ✅ Monitorer les échecs de pipeline
- ✅ Activer Dependabot
- ✅ Réviser les PRs automatiques
- ✅ Nettoyer les caches obsolètes

## 📚 Ressources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [pnpm CI Guide](https://pnpm.io/continuous-integration)
- [Docker Build Actions](https://github.com/docker/build-push-action)
- [CodeQL Documentation](https://codeql.github.com/docs/)

## 🎯 Prochaines améliorations

- [ ] Tests de performance (Lighthouse CI)
- [ ] Tests de charge (k6, JMeter)
- [ ] Notifications Slack/Discord
- [ ] Rollback automatique en cas d'échec
- [ ] Blue-green deployment
- [ ] Monitoring post-déploiement (Prometheus, Grafana)
