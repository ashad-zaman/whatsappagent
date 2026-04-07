# syntax=docker/dockerfile:1.7
# ============================================
# WhatsApp AI - RAG Service
# Optimized for production with multi-stage build
# ============================================

# Stage 1: Dependencies
FROM node:20-alpine AS deps
WORKDIR /app

RUN npm install -g pnpm@9.15.9

COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./
COPY packages/*/package.json packages/
COPY services/*/package.json services/

RUN pnpm install --frozen-lockfile --prod=false

# Stage 2: Builder
FROM node:20-alpine AS builder
WORKDIR /app

RUN npm install -g pnpm@9.15.9

COPY --from=deps /app/node_modules ./node_modules
COPY --from=deps /app/packages ./packages
COPY --from=deps /app/services ./services
COPY . .

# Stage 3: Runner
FROM node:20-alpine AS runner
WORKDIR /app

ENV NODE_ENV=production
ENV NODE_OPTIONS="--max-old-space-size=2048"

RUN addgroup --system --gid 1001 nodejs && \
    adduser --system --uid 1001 rag

RUN apk add --no-cache dumb-init tini && npm install -g tsx

COPY --from=builder --chown=rag:nodejs /app/services/rag/src ./src
COPY --from=builder --chown=rag:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=rag:nodejs /app/packages ./packages

RUN chown -R 1001:1001 /app

USER rag

HEALTHCHECK --interval=30s --timeout=10s --start-period=15s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:3002/health || exit 1

EXPOSE 3002

ENTRYPOINT ["tini", "--"]
CMD ["tsx", "src/index.ts"]
