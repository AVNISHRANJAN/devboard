# =========================
# Stage 1: Build
# =========================
FROM node:22-alpine AS build

WORKDIR /app

# Copy dependency files first for better Docker caching
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy application source
COPY . .

# Build Vite application
RUN npm run build


# =========================
# Stage 2: Production
# =========================
FROM nginxinc/nginx-unprivileged:1.27-alpine

# Remove default Nginx files
RUN rm -rf /usr/share/nginx/html/*

# Copy Vite production build
COPY --from=build /app/dist /usr/share/nginx/html

# SPA configuration for React Router
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]
