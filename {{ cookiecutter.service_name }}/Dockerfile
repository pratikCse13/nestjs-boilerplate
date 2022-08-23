FROM node:16.16-alpine3.16 as builder

WORKDIR /app

COPY package.json ./
COPY package-lock.json ./
COPY tsconfig.json ./
COPY tsconfig.build.json ./
COPY src ./src

RUN --mount=type=secret,id=npmrc,dst=.npmrc npm ci
RUN npm run build
RUN npm prune --production

FROM alpine:3.16 as final

LABEL org.opencontainers.image.source = "https://github.com/{{ cookiecutter.service_name }}"

RUN apk --no-cache add --upgrade nodejs=16.16.0-r0

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV

WORKDIR /app

COPY --from=builder --chown=appuser:appgroup /app/dist ./dist
COPY --from=builder --chown=appuser:appgroup /app/node_modules ./node_modules

CMD ["node", "./dist/main.js"]
