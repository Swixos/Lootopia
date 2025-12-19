# Dockerfile pour l'application Angular
FROM node:20-alpine AS base

# Installer pnpm
RUN corepack enable && corepack prepare pnpm@latest --activate

FROM base AS dependencies

WORKDIR /app

# Copier les fichiers de configuration pnpm
COPY pnpm-workspace.yaml package.json pnpm-lock.yaml* ./
COPY apps/frontend/package.json ./apps/frontend/

# Installer les dépendances
RUN pnpm install --frozen-lockfile

FROM base AS build

WORKDIR /app

# Copier les dépendances installées
COPY --from=dependencies /app/node_modules ./node_modules
COPY --from=dependencies /app/apps/frontend/node_modules ./apps/frontend/node_modules

# Copier le code source
COPY apps/frontend ./apps/frontend
COPY pnpm-workspace.yaml package.json ./

# Build l'application
WORKDIR /app/apps/frontend
RUN pnpm run build:prod

FROM nginx:alpine AS runtime

# Copier la configuration nginx
COPY apps/frontend/nginx.conf /etc/nginx/nginx.conf

# Copier les fichiers buildés
COPY --from=build /app/apps/frontend/dist/frontend/browser /usr/share/nginx/html

# Exposer le port
EXPOSE 80

# Démarrer nginx
CMD ["nginx", "-g", "daemon off;"]
