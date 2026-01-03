ARG NODE_VERSION=19.6

FROM node:${NODE_VERSION}-bullseye-slim AS base 

WORKDIR /app

COPY package*.json ./

FROM base AS dev 

RUN --mount=type=cache,target=/app/.npm \
    npm set cache /app/.npm && npm install

COPY . .

ENTRYPOINT ["npm"]

CMD ["run", "dev"]

FROM base AS build

RUN --mount=type=cache,target=/app/.npm \
    npm set cache /app/.npm && npm ci
    
COPY . .

RUN npm run build

FROM nginxinc/nginx-unprivileged:1.23-alpine AS production

COPY nginx.conf /etc/nginx/conf.d/default.conf

COPY --from=build /app/dist /usr/share/nginx/html

ENV NODE_ENV=production

EXPOSE 8080