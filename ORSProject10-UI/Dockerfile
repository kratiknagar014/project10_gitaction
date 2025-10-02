# Multi-stage build for Angular application
# Stage 1: Build stage - Using exact local Node.js version
FROM node:12.22.12-alpine AS build

# Set working directory
WORKDIR /app

# Install Angular CLI globally first (exact version)
RUN npm install -g @angular/cli@7.3.9

# Copy package files first for better layer caching
COPY package*.json ./

# Install dependencies with legacy peer deps for Angular 7
RUN npm install --legacy-peer-deps --no-optional

# Copy source code
COPY . .

# Build the Angular application for production
RUN npm run build:prod

# Stage 2: Runtime stage with Nginx
FROM nginx:alpine

# Copy custom nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Copy built Angular app from build stage
COPY --from=build /app/dist /usr/share/nginx/html

# Create nginx user and set permissions
RUN chown -R nginx:nginx /usr/share/nginx/html && \
    chmod -R 755 /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD curl -f http://localhost/ || exit 1

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
