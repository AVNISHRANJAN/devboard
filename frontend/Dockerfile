# =========================
# Stage 1: Build
# =========================
FROM node:22-alpine AS build

WORKDIR /app

# Copy dependency files first for better Docker caching
COPY package*.json ./

# Install dependencies
RUN npm ci --legacy-peer-deps

# Copy application source
COPY . .

# Build Vite application
RUN npm run build


# =========================
# Stage 2: Production
# =========================
FROM nginxinc/nginx-unprivileged:1.29-alpine-slim

# Refresh base-image packages so security fixes released after the image
# build are included in the immutable application image.
USER root
RUN apk upgrade --no-cache

# The unprivileged image intentionally runs as a non-root user, so its
# default files are not removable during the image build. Copying the SPA
# output over index.html is sufficient; the remaining default 50x page is
# harmless and is replaced by the custom error handling at runtime if used.
COPY --from=build /app/dist/ /usr/share/nginx/html/

# SPA configuration for React Router
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 8080

USER 101
CMD ["nginx", "-g", "daemon off;"]
