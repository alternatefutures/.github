# Alternate Futures Technical Architecture

Comprehensive technical architecture map for the Alternate Futures decentralized cloud platform.

## Table of Contents

1. [System Overview](#system-overview)
2. [Architecture Layers](#architecture-layers)
3. [Core Services](#core-services)
4. [Client Components](#client-components)
5. [Infrastructure](#infrastructure)
6. [Data Flow](#data-flow)
7. [Technology Stack](#technology-stack)
8. [Repository Map](#repository-map)
9. [Security Architecture](#security-architecture)
10. [Deployment Architecture](#deployment-architecture)

---

## System Overview

Alternate Futures is a **decentralized cloud platform** (forked from Fleek) providing IPFS/Filecoin/Arweave hosting, serverless functions, container registry, and AI agent deployment capabilities.

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ALTERNATE FUTURES PLATFORM                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │                    Client Layer                                │  │
│  ├───────────────────────────────────────────────────────────────┤  │
│  │  Web Dashboard  │  CLI (af)  │  SDK  │  Docker Client        │  │
│  └────────┬──────────────┬────────────┬──────────────┬───────────┘  │
│           │              │            │              │              │
│  ┌────────▼──────────────▼────────────▼──────────────▼───────────┐  │
│  │                    API Gateway Layer                          │  │
│  ├──────────────────────────────────────────────────────────────┤  │
│  │  GraphQL API  │  Auth Service  │  Registry API              │  │
│  └────────┬──────────────┬────────────┬──────────────────────────┘  │
│           │              │            │                             │
│  ┌────────▼──────────────▼────────────▼──────────────────────────┐  │
│  │                    Service Layer                              │  │
│  ├──────────────────────────────────────────────────────────────┤  │
│  │  Sites/Apps  │  Functions  │  Agents  │  Storage  │  Billing │  │
│  └────────┬──────────────┬────────────┬──────────────┬───────────┘  │
│           │              │            │              │              │
│  ┌────────▼──────────────▼────────────▼──────────────▼───────────┐  │
│  │                   Storage & Infrastructure                    │  │
│  ├──────────────────────────────────────────────────────────────┤  │
│  │  PostgreSQL  │  IPFS  │  Arweave  │  Filecoin  │  Secrets   │  │
│  └────────┬──────────────┬────────────┬──────────────┬───────────┘  │
│           │              │            │              │              │
│  ┌────────▼──────────────▼────────────▼──────────────▼───────────┐  │
│  │                  Deployment Infrastructure                    │  │
│  ├──────────────────────────────────────────────────────────────┤  │
│  │              Akash Network (Decentralized Compute)           │  │
│  │         SSL Proxy (Pingap) │ DNS (Multi-Provider)           │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Architecture Layers

### 1. Client Layer

**Purpose**: User-facing interfaces and tools

| Component | Technology | Repository | Description |
|-----------|-----------|------------|-------------|
| Web Dashboard | SvelteKit 2 + Svelte 5 | `web-app.alternatefutures.ai` | Main user interface |
| CLI | Node.js (Commander.js) | `package-cloud-cli` | Command-line tool (`af`) |
| SDK | TypeScript | `package-cloud-sdk` | JavaScript SDK for programmatic access |
| Docker Client | OCI Compatible | - | Standard Docker client for registry |

### 2. API Gateway Layer

**Purpose**: Entry points for all client requests

```
┌──────────────────────────────────────────────────────────────┐
│                      API Gateway Layer                       │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌────────────────┐  ┌─────────────────┐  ┌─────────────┐  │
│  │  GraphQL API   │  │  Auth Service   │  │  Registry   │  │
│  │  (Yoga + Prisma│  │  (Hono + SQLite)│  │  (OCI API)  │  │
│  └───────┬────────┘  └────────┬────────┘  └──────┬──────┘  │
│          │                    │                   │          │
│     api.alternatefutures.ai                                  │
│     auth.alternatefutures.ai                                 │
│     registry.alternatefutures.ai                             │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### 3. Service Layer

**Purpose**: Core business logic and functionality

| Service | Purpose | Technology | Repository |
|---------|---------|-----------|------------|
| Sites/Apps | Static site & app hosting | IPFS/Arweave/Filecoin | `service-cloud-api` |
| Functions | Serverless function execution | Runtime containers | `service-cloud-api` |
| Agents | AI agent deployment | Container orchestration | `service-cloud-api` |
| Storage | Decentralized file storage | IPFS/Arweave/Filecoin | `service-cloud-api` |
| Billing | Payment processing | Stripe | `service-cloud-api` |
| Registry | Container image storage | IPFS + PostgreSQL | Separate stack |

### 4. Storage & Data Layer

**Purpose**: Persistent data storage

```
┌─────────────────────────────────────────────────────────┐
│                    Storage Layer                        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────────────┐  ┌───────────┐  ┌─────────────────┐ │
│  │  PostgreSQL  │  │   IPFS    │  │ Arweave/Filecoin│ │
│  │              │  │  (Kubo)   │  │                 │ │
│  │  Metadata    │  │  Content  │  │  Permanent      │ │
│  │  Users       │  │  Blobs    │  │  Storage        │ │
│  │  Sites       │  │  Files    │  │                 │ │
│  │  Deployments │  │           │  │                 │ │
│  └──────────────┘  └───────────┘  └─────────────────┘ │
│                                                         │
│  ┌──────────────┐  ┌───────────┐                      │
│  │  Infisical   │  │  SQLite   │                      │
│  │  (Secrets)   │  │  (Auth)   │                      │
│  └──────────────┘  └───────────┘                      │
└─────────────────────────────────────────────────────────┘
```

### 5. Infrastructure Layer

**Purpose**: Deployment and network infrastructure

- **Akash Network**: Decentralized compute platform hosting all services
- **SSL Proxy (Pingap)**: SSL termination and traffic routing
- **DNS**: Multi-provider DNS (Cloudflare, Google, deSEC)
- **Monitoring**: Observability and health checks

---

## Core Services

### 1. GraphQL API (`service-cloud-api`)

**Repository**: `alternatefutures/service-cloud-api`

**Technology Stack**:
- GraphQL Yoga (GraphQL server)
- Prisma (ORM)
- PostgreSQL (database)
- Node.js/TypeScript

**Responsibilities**:
- User management
- Project management
- Site/deployment management
- IPFS/Arweave/Filecoin integration
- Billing (Stripe)
- API key management
- Function management
- Agent deployment

**Endpoints**:
- `https://api.alternatefutures.ai/graphql`

**Key Features**:
- Depth limiting
- Complexity analysis
- Rate limiting
- JWT authentication
- Personal Access Tokens

### 2. Auth Service (`service-auth`)

**Repository**: `alternatefutures/service-auth`

**Technology Stack**:
- Hono (HTTP framework)
- SQLite (database)
- Node.js/TypeScript

**Authentication Methods**:
1. Email magic links (passwordless)
2. SMS OTP
3. Web3 wallets (SIWE - Sign-In with Ethereum)
   - MetaMask
   - WalletConnect
   - Phantom
4. Social OAuth
   - Google
   - GitHub
   - Twitter
   - Discord
5. Account linking (multiple methods per user)

**Endpoints**:
- `https://auth.alternatefutures.ai`

### 3. Container Registry

**Technology Stack**:
- OpenRegistry (OCI-compliant API)
- IPFS/Kubo (blob storage)
- PostgreSQL (metadata)

**Architecture**:
```
┌────────────────────────────────────────────┐
│        Container Registry Stack            │
├────────────────────────────────────────────┤
│                                            │
│  ┌──────────────┐  ┌──────────────────┐  │
│  │ OpenRegistry │◄─┤   PostgreSQL     │  │
│  │  (OCI API)   │  │   (Metadata)     │  │
│  └──────┬───────┘  └──────────────────┘  │
│         │                                  │
│         ▼                                  │
│  ┌──────────────┐                        │
│  │ IPFS (Kubo)  │                        │
│  │ (Blob Store) │                        │
│  └──────────────┘                        │
│                                            │
└────────────────────────────────────────────┘
```

**Features**:
- OCI-compliant (Docker compatible)
- Content-addressed storage (IPFS)
- Layer deduplication
- Authentication via JWT
- TLS/HTTPS

**Endpoints**:
- `https://registry.alternatefutures.ai`

See [Registry Architecture Documentation](https://github.com/alternatefutures/web-docs.alternatefutures.ai/blob/main/docs/guides/registry-architecture.md) for detailed information.

### 4. Secrets Management (`service-secrets`)

**Repository**: `alternatefutures/service-secrets`

**Technology**: Infisical (self-hosted)

**Purpose**: Centralized secrets management for all services

**Architecture Decision**: Runs outside the SSL proxy for resilience - if proxy has issues, Infisical remains accessible.

**Endpoints**:
- `https://secrets.alternatefutures.ai`

### 5. Proxy Service (`infrastructure-proxy`)

**Repository**: `alternatefutures/infrastructure-proxy`

**Technology**: Pingap (based on Pingora)

**Purpose**:
- SSL termination
- Traffic routing
- Load balancing
- Let's Encrypt DNS-01 via Cloudflare

**Deployment**:
- Dedicated IP: 170.75.255.101
- DSEQ: 24758214
- Provider: leet.haus

**Routes**:
- auth.alternatefutures.ai → Auth Service
- api.alternatefutures.ai → GraphQL API
- app.alternatefutures.ai → Web Dashboard
- docs.alternatefutures.ai → Documentation
- alternatefutures.ai → Company Website
- registry.alternatefutures.ai → Container Registry

### 6. DNS Management (`infrastructure-dns`)

**Repository**: `alternatefutures/infrastructure-dns`

**Technology**: OctoDNS

**Providers**:
- Cloudflare (primary)
- Google Cloud DNS
- deSEC

**Features**:
- Multi-provider sync
- Automatic failover monitoring
- Version control for DNS records

---

## Client Components

### 1. Web Dashboard

**Repository**: `alternatefutures/web-app.alternatefutures.ai`

**Technology**:
- SvelteKit 2.0
- Svelte 5 (runes-based reactivity)
- SMUI (Svelte Material UI)
- Monaco Editor (code editing)

**Features**:
- Project management
- Site deployment
- Function management
- Agent deployment
- API key management
- Billing dashboard
- Real-time deployment logs

**URL**: `https://app.alternatefutures.ai`

### 2. CLI Tool

**Repository**: `alternatefutures/package-cloud-cli`

**Binary Name**: `af`

**Technology**: Node.js + Commander.js

**Features**:
- Site deployment (`af deploy`)
- Site management (`af sites list`)
- Function deployment
- Agent deployment
- Authentication
- Configuration management

**Installation**:
```bash
npm install -g @alternatefutures/cli
```

### 3. SDK

**Repository**: `alternatefutures/package-cloud-sdk`

**Technology**: TypeScript (browser + Node.js)

**Features**:
- Programmatic API access
- Site deployment
- File upload to IPFS/Arweave
- Project management
- TypeScript support

**Installation**:
```bash
npm install @alternatefutures/sdk
```

### 4. Documentation Site

**Repository**: `alternatefutures/web-docs.alternatefutures.ai`

**Technology**: VitePress

**Content**:
- Guides
- API reference
- CLI reference
- SDK reference
- Architecture documentation

**URL**: `https://docs.alternatefutures.ai`

### 5. Company Website

**Repository**: `alternatefutures/web-alternatefutures.ai`

**Technology**: Astro

**Purpose**: Marketing and company information

**URL**: `https://alternatefutures.ai`

---

## Infrastructure

### Akash Network Deployments

All services run on **Akash Network**, a decentralized compute marketplace.

| Service | DSEQ | Provider | Ingress/IP | Resources |
|---------|------|----------|------------|-----------|
| SSL Proxy | 24758214 | leet.haus | 170.75.255.101 (dedicated) | 2 CPU, 4GB RAM |
| Secrets (Infisical) | 24672527 | Europlots | ddchr1pel5e0p8i0c46drjpclg.ingress.europlots.com | 2 CPU, 4GB RAM, 20GB storage |

**Deployment Management**:
- Repository: `alternatefutures/admin`
- File: `infrastructure/deployments.ts`
- Tool: Akash MCP Server (for Claude Code integration)

### Traffic Flow

```
                    Internet
                       │
        ┌──────────────┴──────────────┐
        │                             │
        ▼                             ▼
┌───────────────┐           ┌─────────────────┐
│  SSL Proxy    │           │ Secrets Service │
│ 170.75.255.101│           │  (Direct/       │
│               │           │   Isolated)     │
└───────┬───────┘           └─────────────────┘
        │
        ├─► auth.alternatefutures.ai
        ├─► api.alternatefutures.ai
        ├─► app.alternatefutures.ai
        ├─► docs.alternatefutures.ai
        ├─► registry.alternatefutures.ai
        └─► alternatefutures.ai
```

### DNS Architecture

Multi-provider setup for redundancy:

1. **Cloudflare** (primary)
   - SSL/TLS proxy
   - DDoS protection
   - DNS-01 challenge for Let's Encrypt

2. **Google Cloud DNS** (secondary)
   - Failover support

3. **deSEC** (secondary)
   - Decentralized DNS option

**Management**: OctoDNS with automatic sync across providers

---

## Data Flow

### Site Deployment Flow

```
┌──────────┐
│  User    │
│ (CLI/UI) │
└────┬─────┘
     │ 1. af deploy / UI deploy
     ▼
┌──────────────┐
│  Auth Check  │
└──────┬───────┘
       │ 2. JWT token validation
       ▼
┌──────────────┐
│  GraphQL API │
└──────┬───────┘
       │ 3. Create deployment record
       │ 4. Upload files
       ▼
┌──────────────┐
│   Storage    │
│ IPFS/Arweave │
└──────┬───────┘
       │ 5. Return CID/hash
       ▼
┌──────────────┐
│  PostgreSQL  │
│ Save metadata│
└──────┬───────┘
       │ 6. Update deployment status
       ▼
┌──────────────┐
│   Gateway    │
│ IPFS/Custom  │
└──────┬───────┘
       │ 7. Site accessible at gateway
       ▼
┌──────────────┐
│   User       │
│ View site    │
└──────────────┘
```

### Registry Push Flow

```
┌──────────┐
│  Docker  │
│  Client  │
└────┬─────┘
     │ 1. docker push registry.alternatefutures.ai/app:v1
     ▼
┌──────────────┐
│ OpenRegistry │
└──────┬───────┘
       │ 2. Authenticate (JWT)
       │ 3. Split into layers (blobs)
       │ 4. For each layer:
       ▼
┌──────────────┐
│  IPFS (Kubo) │
└──────┬───────┘
       │ 5. Store blob, return CID
       │ 6. Pin CID
       ▼
┌──────────────┐
│  PostgreSQL  │
└──────┬───────┘
       │ 7. Store digest → CID mapping
       │ 8. Store manifest
       ▼
┌──────────────┐
│   Complete   │
└──────────────┘
```

### Authentication Flow

```
┌─────────┐
│  User   │
└────┬────┘
     │ 1. Login request
     ▼
┌──────────────┐
│ Auth Service │
└──────┬───────┘
       │ 2. Verify credentials
       │    (email magic link, Web3, OAuth, etc.)
       ▼
┌──────────────┐
│  Generate    │
│  JWT Token   │
└──────┬───────┘
       │ 3. Return token
       ▼
┌──────────────┐
│   Client     │
│ Store token  │
└──────┬───────┘
       │ 4. Include in future requests
       ▼
┌──────────────┐
│ GraphQL API  │
│ Verify token │
└──────────────┘
```

---

## Technology Stack

### Backend

| Layer | Technology | Purpose |
|-------|-----------|---------|
| API Framework | GraphQL Yoga | GraphQL server |
| ORM | Prisma | Database abstraction |
| HTTP Framework | Hono | Auth service HTTP server |
| Database | PostgreSQL | Primary relational database |
| Database (Auth) | SQLite | Auth service local database |
| Runtime | Node.js/TypeScript | JavaScript runtime |
| Testing | Vitest | Unit/integration testing |

### Frontend

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Framework | SvelteKit 2 | Web framework |
| UI Library | Svelte 5 | Reactive UI |
| Component Library | SMUI | Material UI components |
| Editor | Monaco Editor | Code editing |
| State Management | Svelte Stores | Client-side state |
| Build Tool | Vite | Build tooling |

### Storage

| Technology | Purpose | Provider/Options |
|-----------|---------|------------------|
| IPFS | Content-addressed storage | Pinata, Web3.Storage, Lighthouse |
| Arweave | Permanent storage | Turbo SDK |
| Filecoin | Decentralized storage | - |
| PostgreSQL | Relational data | Akash-hosted |
| SQLite | Local auth data | File-based |

### Infrastructure

| Technology | Purpose | Notes |
|-----------|---------|-------|
| Akash Network | Decentralized compute | All services hosted here |
| Pingap/Pingora | SSL proxy | High-performance reverse proxy |
| OctoDNS | DNS management | Multi-provider sync |
| Infisical | Secrets management | Self-hosted |
| Let's Encrypt | SSL certificates | DNS-01 via Cloudflare |

### Tools & CLI

| Tool | Technology | Purpose |
|------|-----------|---------|
| CLI | Commander.js | Command-line interface |
| SDK | TypeScript | Programmatic API access |
| Linter (Backend) | Biome | Code linting/formatting |
| Linter (Frontend) | ESLint + Prettier | Code linting/formatting |
| Package Manager | pnpm | Monorepo package management |

---

## Repository Map

### Core Services

| Repository | Type | Technology | Purpose |
|-----------|------|-----------|---------|
| `service-cloud-api` | Service | GraphQL + Prisma | Main API server |
| `service-auth` | Service | Hono + SQLite | Authentication service |
| `service-secrets` | Infrastructure | Infisical | Secrets management |

### Client Applications

| Repository | Type | Technology | Purpose |
|-----------|------|-----------|---------|
| `web-app.alternatefutures.ai` | Web App | SvelteKit 2 | User dashboard |
| `web-docs.alternatefutures.ai` | Documentation | VitePress | Documentation site |
| `web-alternatefutures.ai` | Website | Astro | Company website |

### Packages

| Repository | Type | Technology | Purpose |
|-----------|------|-----------|---------|
| `package-cloud-cli` | Package | Node.js | CLI tool |
| `package-cloud-sdk` | Package | TypeScript | JavaScript SDK |

### Infrastructure

| Repository | Type | Technology | Purpose |
|-----------|------|-----------|---------|
| `infrastructure-proxy` | Infrastructure | Pingap | SSL proxy |
| `infrastructure-dns` | Infrastructure | OctoDNS | DNS management |

### Templates

| Repository | Type | Framework | Purpose |
|-----------|------|-----------|---------|
| `template-cloud-nextjs` | Template | Next.js | Next.js starter |
| `template-cloud-astro` | Template | Astro | Astro starter |
| `template-cloud-react` | Template | React | React starter |
| `template-cloud-vue` | Template | Vue | Vue starter |
| `template-cloud-hugo` | Template | Hugo | Hugo starter |

### Tools & Registry

| Repository | Type | Technology | Purpose |
|-----------|------|-----------|---------|
| `repo-cloud-templates` | Tools | - | Template repository |
| `adapter-cloud-next` | Adapter | Next.js | Next.js deployment adapter |
| `open-next` | Adapter | Next.js | Open-source Next.js adapter |

### Admin & Organization

| Repository | Type | Purpose |
|-----------|------|---------|
| `admin` | Admin | Organization administration |
| `.github` | Meta | GitHub org configuration |
| `grants` | Admin | Grant applications |

### Archived

| Repository | Type | Status |
|-----------|------|--------|
| `archived-web-app.alternatefutures.ai` | Web App | Archived (old dashboard) |

---

## Security Architecture

### Authentication & Authorization

**Multi-Method Authentication**:
1. Email magic links (passwordless)
2. SMS OTP
3. Web3 wallets (SIWE)
4. Social OAuth (Google, GitHub, Twitter, Discord)

**Token System**:
- JWT tokens (short-lived)
- Personal Access Tokens (long-lived, API access)
- OAuth tokens (social login)

**Authorization**:
- Role-based access control (RBAC)
- Project-level permissions
- API key scoping

### Network Security

**SSL/TLS**:
- Let's Encrypt certificates
- DNS-01 challenge via Cloudflare
- Automatic renewal
- TLS 1.2+ enforced

**Traffic Isolation**:
- Internal service network (not exposed)
- Public endpoints via SSL proxy
- Secrets service isolated from proxy

**DDoS Protection**:
- Cloudflare proxy
- Rate limiting on API
- Complexity analysis on GraphQL

### Data Security

**Encryption**:
- Data in transit: TLS/HTTPS
- Secrets: Infisical vault
- Database: Encrypted at rest (provider-dependent)

**Content Addressing**:
- IPFS CID verification
- SHA256 digest validation
- Tamper detection

---

## Deployment Architecture

### Akash Network

All services deployed on **Akash Network**, a decentralized compute marketplace where providers bid on workloads.

**Benefits**:
- Censorship resistance
- Geographic distribution
- Cost efficiency
- No single point of failure

**Deployment Process**:
1. Define SDL (Service Definition Language)
2. Create deployment on Akash
3. Providers bid on deployment
4. Select provider based on price/location/reputation
5. Deployment runs on selected provider
6. Monitor via Akash console

**Persistent Storage**:
- Backed by provider infrastructure
- Data persists across deployments
- Redundant storage recommended

### High Availability

**Multi-Provider Strategy**:
- DNS: Cloudflare (primary), Google DNS, deSEC
- IPFS: Multiple pinning services (Pinata, Web3.Storage, Lighthouse)
- Storage: IPFS + Arweave + Filecoin

**Monitoring**:
- Health checks on all services
- Uptime monitoring
- Alert system

**Failover**:
- DNS failover to backup providers
- Service redeployment on different Akash provider
- Data redundancy across storage layers

---

## Integration Points

### External Services

| Service | Purpose | Integration |
|---------|---------|-------------|
| Pinata | IPFS pinning | API |
| Web3.Storage | IPFS pinning | API |
| Lighthouse | IPFS pinning | API |
| Arweave | Permanent storage | Turbo SDK |
| Filecoin | Decentralized storage | API |
| Stripe | Payment processing | API |
| Cloudflare | DNS, SSL, CDN | API + OctoDNS |
| Google Cloud DNS | DNS | API + OctoDNS |
| deSEC | DNS | API + OctoDNS |

### Internal Communication

**Service-to-Service**:
- GraphQL API ↔ Auth Service (JWT validation)
- GraphQL API ↔ PostgreSQL (Prisma ORM)
- OpenRegistry ↔ PostgreSQL (SQL)
- OpenRegistry ↔ IPFS (HTTP API)
- All Services ↔ Infisical (Secrets retrieval)

**Client-to-Service**:
- Web Dashboard → GraphQL API (GraphQL)
- CLI → GraphQL API (GraphQL)
- SDK → GraphQL API (GraphQL)
- Docker Client → Registry (OCI HTTP API)

---

## Scalability

### Horizontal Scaling

**API Server**:
- Multiple instances behind load balancer
- Stateless design (JWT tokens)
- Shared PostgreSQL database

**Registry**:
- Multiple OpenRegistry instances
- Shared IPFS node
- Shared PostgreSQL database

### Storage Scaling

**IPFS**:
- Increase storage allocation (100GB → 500GB+)
- Add IPFS cluster for redundancy
- Multiple pinning services

**Database**:
- PostgreSQL read replicas
- Connection pooling
- Query optimization

### Geographic Distribution

**Multi-Region Deployment**:
- Deploy stacks in different Akash regions
- GeoDNS routing to nearest instance
- Regional IPFS gateways

---

## Monitoring & Observability

### Metrics

**Application Metrics**:
- Request rate, latency, error rate
- GraphQL query complexity
- Deployment success rate

**Infrastructure Metrics**:
- CPU, memory, disk usage
- Network bandwidth
- IPFS repository size

**Business Metrics**:
- Active users
- Deployments per day
- Storage usage
- Revenue

### Logging

**Structured Logs**:
- JSON format
- Centralized logging
- Log levels (debug, info, warn, error)

**Log Sources**:
- API server logs
- Auth service logs
- Registry logs
- Proxy access logs

### Health Checks

**Endpoints**:
- `GET /health` on all services
- GraphQL introspection
- IPFS node status
- Database connection check

---

## Future Enhancements

### Planned Features

1. **Registry**:
   - IPFS cluster for redundancy
   - Image signing (Cosign)
   - Vulnerability scanning (Trivy)
   - Cross-region replication
   - Usage analytics
   - Webhook notifications

2. **Platform**:
   - Edge functions (Cloudflare Workers-like)
   - Real-time collaboration
   - AI agent marketplace
   - Template marketplace
   - Custom domain SSL automation

3. **Infrastructure**:
   - Multi-cloud support (beyond Akash)
   - Backup/restore automation
   - Disaster recovery
   - Performance optimization

---

## References

### Documentation

- [Registry Architecture](https://github.com/alternatefutures/web-docs.alternatefutures.ai/blob/main/docs/guides/registry-architecture.md)
- [API Documentation](https://docs.alternatefutures.ai)
- [CLI Reference](https://docs.alternatefutures.ai/cli)
- [SDK Reference](https://docs.alternatefutures.ai/sdk)

### External Resources

- [Akash Network](https://akash.network)
- [IPFS](https://ipfs.tech)
- [Arweave](https://arweave.org)
- [Filecoin](https://filecoin.io)
- [OCI Distribution Spec](https://github.com/opencontainers/distribution-spec)

### Related Files

- `admin/CLAUDE.md` - Repository structure and development guide
- `admin/infrastructure/deployments.ts` - Akash deployment configuration
- `.github/DEPLOYMENTS.md` - Deployment tracking
- `.github/CONTRIBUTING.md` - Contribution guidelines

---

## Glossary

- **AF**: Alternate Futures
- **CLI**: Command-Line Interface
- **CID**: Content Identifier (IPFS)
- **DSEQ**: Deployment Sequence Number (Akash)
- **DePIN**: Decentralized Physical Infrastructure Network
- **IPFS**: InterPlanetary File System
- **IPNS**: InterPlanetary Name System
- **JWT**: JSON Web Token
- **OCI**: Open Container Initiative
- **SDL**: Service Definition Language (Akash)
- **SIWE**: Sign-In with Ethereum
- **SSL**: Secure Sockets Layer
- **TLS**: Transport Layer Security

---

**Last Updated**: 2026-01-21  
**Version**: 1.0.0  
**Maintainers**: Alternate Futures Team
