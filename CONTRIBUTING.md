# Guide de contribution - Lootopia

Merci de votre intérêt pour contribuer à Lootopia ! Ce guide vous explique comment contribuer au projet de manière efficace.

## 📋 Table des matières

- [Code de conduite](#code-de-conduite)
- [Workflow de contribution](#workflow-de-contribution)
- [Configuration de l'environnement](#configuration-de-lenvironnement)
- [Standards de code](#standards-de-code)
- [Process de Pull Request](#process-de-pull-request)
- [Tests](#tests)
- [Commits](#commits)

## 🤝 Code de conduite

- Soyez respectueux et bienveillant
- Acceptez les critiques constructives
- Concentrez-vous sur ce qui est meilleur pour la communauté
- Montrez de l'empathie envers les autres membres

## 🔄 Workflow de contribution

### 1. Fork et Clone

```bash
# Fork le projet sur GitHub
# Puis cloner votre fork
git clone https://github.com/VOTRE_USERNAME/Lootopia.git
cd Lootopia

# Ajouter le remote upstream
git remote add upstream https://github.com/Swixos/Lootopia.git
```

### 2. Créer une branche

```bash
# Mettre à jour depuis main
git checkout main
git pull upstream main

# Créer une branche pour votre feature/fix
git checkout -b feature/ma-nouvelle-fonctionnalite
# ou
git checkout -b fix/correction-bug
```

**Convention de nommage des branches** :
- `feature/` - Nouvelles fonctionnalités
- `fix/` - Corrections de bugs
- `refactor/` - Refactoring de code
- `docs/` - Documentation uniquement
- `test/` - Ajout ou modification de tests
- `chore/` - Maintenance (deps, config, etc.)

### 3. Développer

```bash
# Installer les dépendances
pnpm install

# Démarrer l'environnement de développement
docker-compose up
# ou
pnpm dev

# Faire vos modifications...
```

### 4. Tester localement

```bash
# Lancer les tests
pnpm test

# Vérifier le lint
pnpm lint

# Vérifier le formatage
pnpm format:check

# Build pour vérifier qu'il n'y a pas d'erreurs
pnpm build
```

### 5. Commit

```bash
# Ajouter vos modifications
git add .

# Commit avec un message conventionnel
git commit -m "feat: ajouter authentification JWT"
```

### 6. Push et Pull Request

```bash
# Push vers votre fork
git push origin feature/ma-nouvelle-fonctionnalite

# Créer une Pull Request sur GitHub
```

## 🛠️ Configuration de l'environnement

### Prérequis

- Node.js >= 20.0.0
- pnpm >= 8.0.0
- Docker (optionnel mais recommandé)

### Installation

```bash
# Cloner le repo
git clone https://github.com/Swixos/Lootopia.git
cd Lootopia

# Installer les dépendances
pnpm install

# Copier les variables d'environnement
cp .env.example .env
cp apps/backend/.env.example apps/backend/.env

# Démarrer avec Docker
docker-compose up
```

## 📝 Standards de code

### TypeScript

- Utiliser TypeScript strict mode
- Typer explicitement les paramètres et retours de fonction
- Éviter `any`, préférer `unknown` si nécessaire

```typescript
// ✅ Bon
function calculerTotal(prix: number, quantite: number): number {
  return prix * quantite;
}

// ❌ Mauvais
function calculerTotal(prix, quantite) {
  return prix * quantite;
}
```

### Nomenclature

- **Variables/Fonctions** : `camelCase`
- **Classes/Interfaces** : `PascalCase`
- **Constantes** : `UPPER_SNAKE_CASE`
- **Fichiers** : `kebab-case.ts`

```typescript
// ✅ Bon
const userName = 'John';
const MAX_RETRIES = 3;
class UserService {}
interface UserDto {}

// ❌ Mauvais
const UserName = 'John';
const maxRetries = 3;
class userService {}
```

### Principes SOLID

Respectez les principes SOLID dans votre code :
- **S**ingle Responsibility
- **O**pen/Closed
- **L**iskov Substitution
- **I**nterface Segregation
- **D**ependency Inversion

### Backend (NestJS)

```typescript
// Structure d'un module
users/
├── dto/
│   ├── create-user.dto.ts
│   └── update-user.dto.ts
├── entities/
│   └── user.entity.ts
├── users.controller.ts
├── users.service.ts
└── users.module.ts
```

### Frontend (Angular)

```typescript
// Structure d'une feature
auth/
├── components/
│   ├── login/
│   └── register/
├── services/
│   └── auth.service.ts
├── guards/
│   └── auth.guard.ts
└── auth.module.ts
```

## 🔍 Process de Pull Request

### Avant de soumettre

- [ ] Les tests passent : `pnpm test`
- [ ] Le lint passe : `pnpm lint`
- [ ] Le formatage est correct : `pnpm format:check`
- [ ] Le build fonctionne : `pnpm build`
- [ ] La documentation est à jour
- [ ] Les commits suivent la convention

### Template de PR

```markdown
## Description
Brève description de vos changements

## Type de changement
- [ ] Bug fix
- [ ] Nouvelle fonctionnalité
- [ ] Breaking change
- [ ] Documentation

## Checklist
- [ ] Tests ajoutés/mis à jour
- [ ] Documentation mise à jour
- [ ] Pas de warnings de build
- [ ] Code review effectué

## Screenshots (si applicable)
```

### Revue de code

- Les PRs nécessitent au moins 1 approbation
- Les commentaires de review doivent être adressés
- Les pipelines CI doivent passer (vert ✅)

## ✅ Tests

### Tests unitaires

```bash
# Backend
pnpm --filter @lootopia/backend test
pnpm --filter @lootopia/backend test:watch

# Frontend
pnpm --filter @lootopia/frontend test
```

### Tests e2e

```bash
pnpm --filter @lootopia/backend test:e2e
```

### Couverture

Maintenir une couverture de code > 80% :

```bash
pnpm test:cov
```

### Écrire de bons tests

```typescript
// ✅ Bon test
describe('UserService', () => {
  it('should create a new user with hashed password', async () => {
    const userData = { email: 'test@example.com', password: 'password123' };
    const user = await service.create(userData);

    expect(user.email).toBe(userData.email);
    expect(user.password).not.toBe(userData.password);
    expect(user.password).toMatch(/^\$2[ayb]\$.{56}$/); // bcrypt hash
  });
});

// ❌ Mauvais test
it('should work', () => {
  expect(true).toBe(true);
});
```

## 📦 Commits

### Convention Conventional Commits

Format : `<type>(<scope>): <description>`

**Types** :
- `feat` - Nouvelle fonctionnalité
- `fix` - Correction de bug
- `docs` - Documentation uniquement
- `style` - Formatage, points-virgules, etc.
- `refactor` - Refactoring de code
- `test` - Ajout/modification de tests
- `chore` - Maintenance, dépendances, etc.
- `perf` - Amélioration de performance
- `ci` - Modifications CI/CD

**Exemples** :

```bash
feat(auth): add JWT authentication
fix(backend): correct database connection error
docs(readme): update installation instructions
refactor(frontend): simplify auth guard logic
test(users): add unit tests for user service
chore(deps): update dependencies
```

### Messages de commit

- Utiliser l'impératif présent ("add" pas "added")
- Première ligne < 72 caractères
- Corps du message optionnel pour plus de détails
- Référencer les issues : `Fixes #123`

```bash
# ✅ Bon
git commit -m "feat(auth): add password reset functionality

Add email-based password reset flow with:
- Password reset email sending
- Token generation and validation
- Password update endpoint

Fixes #42"

# ❌ Mauvais
git commit -m "updated stuff"
```

## 🐛 Rapporter des bugs

### Template d'issue

```markdown
## Description du bug
Description claire et concise du bug

## Étapes pour reproduire
1. Aller sur '...'
2. Cliquer sur '...'
3. Voir l'erreur

## Comportement attendu
Ce qui devrait se passer

## Comportement actuel
Ce qui se passe actuellement

## Screenshots
Si applicable

## Environnement
- OS: [e.g. Windows 11]
- Browser: [e.g. Chrome 120]
- Version: [e.g. v1.0.0]

## Logs
```
Logs d'erreur pertinents
```
```

## 💡 Proposer des fonctionnalités

### Template de feature request

```markdown
## Description de la fonctionnalité
Description claire de la fonctionnalité souhaitée

## Problème résolu
Quel problème cette feature résout-elle ?

## Solution proposée
Comment imaginez-vous cette feature ?

## Alternatives considérées
Quelles autres solutions avez-vous envisagées ?

## Contexte additionnel
Screenshots, mockups, etc.
```

## 📚 Ressources

- [Documentation Angular](https://angular.io/docs)
- [Documentation NestJS](https://docs.nestjs.com/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [SOLID Principles](https://en.wikipedia.org/wiki/SOLID)

## ❓ Questions

Si vous avez des questions :
- Ouvrir une discussion sur GitHub Discussions
- Contacter les mainteneurs
- Consulter la documentation

## 🎉 Merci !

Vos contributions rendent ce projet meilleur ! Merci de prendre le temps de contribuer. 🙏
