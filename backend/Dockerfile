# ---- Build stage ----
FROM node:20-slim AS build

WORKDIR /app

COPY package.json package-lock.json* ./
RUN npm ci --omit=dev && npm cache clean --force

# ---- Production stage ----
FROM node:20-slim AS production

RUN apt-get update && \
    apt-get install -y --no-install-recommends dumb-init=1.2.5-2 && \
    rm -rf /var/lib/apt/lists/*

RUN groupadd -r appgroup && useradd -r -g appgroup appuser

WORKDIR /app

COPY --from=build /app/node_modules ./node_modules
COPY src/ ./src/

RUN chown -R appuser:appgroup /app

USER appuser

EXPOSE 5000

ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "src/index.js"]
