# Development Dockerfile for Angular application (localhost)
FROM node:12.22.12-alpine

# Set working directory
WORKDIR /app

# Install Angular CLI globally
RUN npm install -g @angular/cli@7.3.9

# Copy package files and npm config
COPY package*.json .npmrc ./

# Install dependencies (using .npmrc settings)
RUN npm install

# Copy source code
COPY . .

# Expose development port
EXPOSE 4200

# Start development server with live reload
CMD ["ng", "serve", "--host", "0.0.0.0", "--port", "4200", "--poll", "2000"]
