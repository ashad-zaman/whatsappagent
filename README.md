# WhatsApp AI Agent Platform

A production-grade SaaS platform for building AI-powered WhatsApp assistants with multi-agent orchestration, Graph RAG, and autonomous workflow management.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)
![Next.js](https://img.shields.io/badge/Next.js-14-black.svg)

## 🚀 Features

- **Multi-Agent Orchestration** - LangChain/LangGraph powered AI agents that collaborate on complex tasks
- **Graph RAG** - Combine vector embeddings with knowledge graphs for accurate, contextual AI responses
- **WhatsApp Integration** - Full WhatsApp Cloud API integration for sending and receiving messages
- **Smart Reminders** - One-time & recurring reminders with plan-based access control
- **Calendar Management** - Create, manage, and sync calendar events with Google/Outlook OAuth
- **Call Reminders** - Schedule call reminders with smart notifications
- **Notes Management** - Create and manage notes with AI-powered search
- **Document Intelligence** - Upload, process, chunk, and query documents with RAG
- **Workflow Automation** - Temporal.io orchestration for reliable autonomous workflows
- **Stripe Billing** - Subscription management with Free/Pro/Enterprise plans

## 🏗️ Architecture

This platform follows a **microservices architecture** with event-driven communication:

```
┌─────────────────────────────────────────────────────────────────┐
│                        API Gateway (Express.js)                 │
│              Rate Limiting | JWT Auth | Request Routing        │
└──────────────────────────────┬──────────────────────────────────┘
                               │
         ┌──────────────────────┼──────────────────────┐
         │                      │                      │
         ▼                      ▼                      ▼
┌───────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Agent Service │    │  RAG Service    │    │  Messaging      │
│  (LangGraph)   │    │  (LlamaIndex)   │    │  (WhatsApp API) │
└───────────────┘    └─────────────────┘    └─────────────────┘
         │                      │                      │
         └──────────────────────┼──────────────────────┘
                               │
         ┌──────────────────────┼──────────────────────┐
         │                      │                      │
         ▼                      ▼                      ▼
┌───────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Workflow      │    │  Dashboard      │    │  API Gateway    │
│  (Temporal.io) │    │  (Next.js 14)   │    │  (Express)      │
└───────────────┘    └─────────────────┘    └─────────────────┘
```

### Core Services

| Service                   | Port            | Description                                      |
| ------------------------- | --------------- | ------------------------------------------------ |
| **API Gateway**           | 3000            | Central entry point with rate limiting, JWT auth |
| **Agent Service**         | 3001            | Multi-agent orchestration with LangGraph         |
| **RAG Service**           | 3002            | Graph RAG with Qdrant + Neo4j                    |
| **Messaging Service**     | 3003            | WhatsApp Cloud API integration                   |
| **Workflow Orchestrator** | 3004            | Temporal.io workflow management                  |
| **Dashboard**             | 3001 (ext:3005) | Next.js admin + user dashboard                   |

### Infrastructure Services

| Service        | Port | Description               |
| -------------- | ---- | ------------------------- |
| **PostgreSQL** | 5432 | Primary database          |
| **Redis**      | 6379 | Caching & session storage |
| **Qdrant**     | 6333 | Vector database for RAG   |
| **Neo4j**      | 7687 | Graph database            |
| **Kafka**      | 9092 | Event streaming           |
| **Temporal**   | 7233 | Workflow orchestration    |

## 📋 Prerequisites

- **Node.js** 20+
- **pnpm** 9.15+
- **Docker** & **Docker Compose**
- **PostgreSQL** (via Docker)
- **OpenAI API Key**

---

## 🏁 Quick Start (Local Development)

### 1. Clone and Install Dependencies

```bash
# Clone the repository
cd /Users/ashaduzzaman/sites/bekredito/whatsappagent

# Install pnpm if not already installed
npm install -g pnpm@9.15.9

# Install all dependencies
pnpm install
```

### 2. Configure Environment Variables

```bash
# Copy the example environment file
cp .env.example .env

# Edit the .env file with your values (minimum required)
# The .env file in root already has defaults for Docker-based setup
```

### 3. Start Infrastructure Services

```bash
# Start PostgreSQL, Redis, Qdrant, Neo4j, Kafka, Temporal
docker-compose up -d postgres redis qdrant neo4j kafka temporal

# Verify all services are running
docker-compose ps
```

### 4. Build and Run Services

```bash
# Build all Docker images
make build

# Start all services
docker-compose up -d
```

### 5. Verify Services

```bash
# Check service status
docker-compose ps

# View logs for a specific service
make logs SERVICE=api-gateway

# Or view all logs
docker-compose logs -f
```

---

## 🔧 Development Environment

### Available Make Commands

```bash
# Show all available commands
make help

# Build all services (Docker images)
make build

# Build specific service
make build-service SERVICE=api-gateway

# Build without cache (fresh build)
make build-no-cache

# Build production images with BuildKit
make build-prod

# View logs
make logs SERVICE=api-gateway

# Shell into container
make shell SERVICE=api-gateway

# Clean up Docker resources
make clean

# Push images to registry
make push REGISTRY=your-registry.io

# Run security scan
make scan
```

### Running Individual Services

```bash
# Build a specific service
docker-compose build api-gateway

# Run a specific service
docker-compose up -d api-gateway

# Run with logs
docker-compose up -f api-gateway
```

### Accessing Services

| Service       | URL                             |
| ------------- | ------------------------------- |
| API Gateway   | http://localhost:3000           |
| Dashboard     | http://localhost:3001           |
| Swagger UI    | http://localhost:3000/api/docs  |
| Qdrant UI     | http://localhost:6333/dashboard |
| Neo4j Browser | http://localhost:7474           |

---

## 🚀 Production Environment

### 1. Production Build

```bash
# Build production-optimized images
make build-prod

# Or build without cache
make build-no-cache
```

### 2. Environment Configuration

Create a production `.env` file with real credentials:

```bash
# Example production .env
NODE_ENV=production
PORT=3000

# PostgreSQL
DATABASE_URL=postgresql://user:password@postgres:5432/whatsapp_ai

# Redis
REDIS_URL=redis://redis:6379

# JWT (generate a secure key)
JWT_SECRET=your-secure-jwt-secret-key-min-256-bits

# OpenAI
OPENAI_API_KEY=sk-your-openai-key

# WhatsApp
WHATSAPP_PHONE_NUMBER_ID=your-phone-number-id
WHATSAPP_BUSINESS_ACCOUNT_ID=your-business-account-id
WHATSAPP_ACCESS_TOKEN=your-access-token
WHATSAPP_WEBHOOK_VERIFY_TOKEN=your-verify-token

# Stripe
STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...

# Vector DB
VECTOR_DB_URL=http://qdrant:6333
VECTOR_DB_API_KEY=your-qdrant-key

# Neo4j
NEO4J_URI=bolt://neo4j:7687
NEO4J_USER=neo4j
NEO4J_PASSWORD=your-neo4j-password
```

### 3. Start Production Services

```bash
# Start all services in production mode
docker-compose -f docker-compose.yml up -d

# Scale services as needed
docker-compose up -d --scale agent-service=3
```

### 4. Health Checks

```bash
# Check health of all services
curl http://localhost:3000/health
curl http://localhost:3001/health
curl http://localhost:3002/health
curl http://localhost:3003/health
curl http://localhost:3004/health
```

### 5. Monitoring

```bash
# View all service logs
docker-compose logs -f

# View specific service
docker-compose logs -f api-gateway

# Check resource usage
docker stats
```

---

## 🔐 Security Setup

### Generate Secure Keys

```bash
# Generate JWT secret (use openssl or similar)
openssl rand -base64 32

# Generate NextAuth secret
openssl rand -base64 32
```

### Update Environment

```bash
# Edit .env file
nano .env
```

Required security variables:

- `JWT_SECRET` - Minimum 256-bit key
- `NEXTAUTH_SECRET` - For Next.js auth
- `STRIPE_WEBHOOK_SECRET` - For Stripe callbacks

---

## 🧪 Testing

### Run Tests in Development

```bash
# Run all tests
pnpm test

# Run tests with coverage
pnpm test --coverage

# Run specific test
pnpm vitest run tests/unit/api/auth.test.ts
```

### Run Tests in Docker

```bash
# Run tests in api-gateway container
make test SERVICE=api-gateway
```

---

## 📚 Documentation

- **[Main Documentation](docs/DOCUMENTATION.md)** - Comprehensive platform documentation
- **[API Reference](docs/API_REFERENCE.md)** - Complete API documentation
- **[Architecture](ARCHITECTURE.md)** - System architecture and design patterns
- **[Deployment Guide](docs/DEPLOYMENT_RUNBOOK.md)** - Production deployment instructions

---

## 🔧 Project Structure

```
whatsappagent/
├── apps/
│   ├── dashboard/           # Next.js dashboard (Admin + User)
│   └── landing/             # Marketing landing page
├── packages/
│   ├── ai/                  # AI/LangChain integration
│   ├── config/              # Environment configuration
│   ├── database/            # Prisma, MongoDB, Redis utilities
│   ├── security/            # RBAC, encryption, rate limiting
│   ├── whatsapp/            # WhatsApp Cloud API client
│   └── ...
├── services/
│   ├── api-gateway/         # Central gateway
│   ├── agent/               # Multi-agent orchestration
│   ├── rag/                 # Graph RAG service
│   ├── workflow/            # Temporal.io workflows
│   └── messaging/           # WhatsApp messaging
├── docs/                    # Documentation
├── docker-compose.yml       # Service orchestration
├── Makefile                 # Build automation
├── pnpm-workspace.yaml      # Package workspace config
└── turbo.json               # Turborepo config
```

---

## 💰 Pricing Plans

| Feature          | Free | Pro ($29/mo) | Enterprise ($99/mo) |
| ---------------- | ---- | ------------ | ------------------- |
| Daily Messages   | 100  | 10,000       | Unlimited           |
| AI Agents        | 2    | 10           | Unlimited           |
| Reminders        | 5/mo | 100/mo       | Unlimited           |
| Document Storage | 50MB | 5GB          | Unlimited           |
| Calendar Sync    | ❌   | ✅           | ✅                  |
| RAG Queries      | ❌   | ✅           | ✅                  |
| Priority Support | ❌   | ❌           | ✅                  |

---

## 📦 Deployment

The platform is **Kubernetes-ready** with:

- Auto-scaling configurations
- Health checks & readiness probes
- Prometheus metrics & Grafana dashboards

See [Deployment Guide](docs/DEPLOYMENT_RUNBOOK.md) for production setup.

---

## ❓ Troubleshooting

### Common Issues

**Services not building**

```bash
# Clean and rebuild
make clean
make build
```

**Database connection issues**

```bash
# Check PostgreSQL is running
docker-compose ps postgres

# Check logs
docker-compose logs postgres
```

**Port conflicts**

```bash
# Check what's using the port
lsof -i :3000

# Stop conflicting service or change port in docker-compose.yml
```

**Memory issues during build**

```bash
# Increase Docker memory limit to 4GB+
# Or build services individually
make build-service SERVICE=api-gateway
```

---

## 📄 License

MIT License - see [LICENSE](LICENSE) for details.

## 🙏 Acknowledgments

- [LangChain](https://langchain.dev/) - AI framework
- [Temporal.io](https://temporal.io/) - Workflow orchestration
- [Qdrant](https://qdrant.tech/) - Vector database
- [Neo4j](https://neo4j.com/) - Graph database
- [WhatsApp Cloud API](https://developers.facebook.com/docs/whatsapp) - Messaging platform
- [Next.js](https://nextjs.org/) - React framework
