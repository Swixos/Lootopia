# Dockerfile pour l'API NestJS
FROM node:20-alpine AS base

# Installer pnpm
RUN corepack enable && corepack prepare pnpm@latest --activate

FROM base AS dependencies

WORKDIR /app

# Copier les fichiers de configuration pnpm
COPY pnpm-workspace.yaml package.json pnpm-lock.yaml* ./
COPY apps/backend/package.json ./apps/backend/

# Installer les dépendances
RUN pnpm install --frozen-lockfile

FROM base AS build

WORKDIR /app

# Copier les dépendances installées
COPY --from=dependencies /app/node_modules ./node_modules
COPY --from=dependencies /app/apps/backend/node_modules ./apps/backend/node_modules

# Copier le code source
COPY apps/backend ./apps/backend
COPY pnpm-workspace.yaml package.json ./

# Build l'application
WORKDIR /app/apps/backend
RUN pnpm run build

FROM base AS runtime

WORKDIR /app

# Copier les fichiers nécessaires
COPY --from=build /app/apps/backend/dist ./dist
COPY --from=build /app/apps/backend/node_modules ./node_modules
COPY --from=build /app/apps/backend/package.json ./

# Variables d'environnement
ENV NODE_ENV=production
ENV PORT=3000

# Exposer le port
EXPOSE 3000

# Démarrer l'application
CMD ["node", "dist/main"]
