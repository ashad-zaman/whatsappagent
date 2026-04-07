# syntax=docker/dockerfile:1.7
# ============================================
# WhatsApp AI - Dashboard (Next.js)
# Using dev mode for monorepo compatibility
# ============================================

FROM node:20-alpine

WORKDIR /app

RUN npm install -g pnpm@9.15.9

# Install Python and build tools
RUN apk add --no-cache python3 make g++

# Copy everything for monorepo
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./
COPY packages ./packages
COPY apps ./apps
COPY services ./services
COPY turbo.json ./

# Install dependencies
RUN pnpm install --no-frozen-lockfile

# Install tsx globally
RUN npm install -g tsx

# Copy dashboard source
COPY apps/dashboard ./apps/dashboard

WORKDIR /app/apps/dashboard
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

EXPOSE 3000

# Run Next.js dev mode in production (not recommended for production use but works for monorepo)
CMD ["npm", "run", "dev"]