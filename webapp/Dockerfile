# Stage 1 - Build the frontend
FROM node:20-alpine AS builder

WORKDIR /app

# Copy only package.json first (layer caching)
COPY package*.json ./

# Install deps
RUN npm install

# Copy the rest of the app
COPY . .

# Accept a build argument from outside
ARG VITE_API_URL

# Expose it to the build
ENV VITE_API_URL=$VITE_API_URL

# Debug print to ensure the value is visible
RUN echo "Building with VITE_API_URL = $VITE_API_URL"

# Run the Vite build
RUN npm run build

# Stage 2 - Nginx to serve the static files
FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html

