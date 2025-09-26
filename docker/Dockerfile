# Stage 1: Build Angular app
FROM node:20-alpine AS builder
WORKDIR /app

# Copy only package files for caching
COPY package.json package-lock.json ./
RUN npm ci

COPY . .
ARG APP_NAME
RUN npx nx build $APP_NAME --prod

# Stage 2: Serve with Nginx
FROM nginx:alpine
ARG APP_NAME

# Remove default nginx files
RUN rm -rf /usr/share/nginx/html/*

# Copy built app to nginx
COPY --from=builder /app/dist/apps/$APP_NAME /usr/share/nginx/html

# Copy common nginx config
COPY docker/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
