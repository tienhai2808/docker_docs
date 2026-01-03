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
    npm set cache /app/.npm && \
    npm ci --only=production

COPY --chown=node:node ./healthcheck ./healthcheck

COPY --chown=node:node ./src ./src 

FROM build AS production

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD ["node", "./healthcheck/healthcheck.js"]

USER node 

ENV NODE_ENV=production

EXPOSE 3000

ENTRYPOINT ["node"]

CMD ["./src/index.js"]

FROM gcr.io/distroless/nodejs18-debian11:nonroot AS distroless-runtime

WORKDIR /app 

COPY --from=build --chown=nonroot:nonroot /app/node_modules ./node_modules

COPY --from=build --chown=nonroot:nonroot /app/healthcheck ./healthcheck

COPY --from=build --chown=nonroot:nonroot /app/src ./src

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD ["./healthcheck/healthcheck.js"]

USER nonroot

ENV NODE_ENV=production

EXPOSE 3000

CMD ["./src/index.js"]



