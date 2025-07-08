# Stage 1 - Build the frontend
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

ARG VITE_API_URL

RUN echo "VITE_API_URL=$VITE_API_URL" > .env.production

RUN npm run build

# Stage 2 - Nginx
FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html


