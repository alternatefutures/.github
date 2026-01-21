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

```mermaid
graph TB
    subgraph "ALTERNATE FUTURES PLATFORM"
        subgraph "Client Layer"
            WebDash[Web Dashboard]
            CLI[CLI - af]
            SDK[SDK]
            Docker[Docker Client]
        end
        
        subgraph "API Gateway Layer"
            GraphQL[GraphQL API<br/>Yoga + Prisma]
            Auth[Auth Service<br/>Hono + SQLite]
            Registry[Registry API<br/>OCI Compatible]
        end
        
        subgraph "Service Layer"
            Sites[Sites/Apps]
            Functions[Functions]
            Agents[AI Agents]
            Storage[Storage]
            Billing[Billing]
        end
        
        subgraph "Storage & Infrastructure"
            Postgres[(PostgreSQL)]
            IPFS[(IPFS/Kubo)]
            Arweave[(Arweave)]
            Filecoin[(Filecoin)]
            Secrets[(Infisical<br/>Secrets)]
        end
        
        subgraph "Deployment Infrastructure"
            Akash[Akash Network<br/>Decentralized Compute]
            Proxy[SSL Proxy<br/>Pingap]
            DNS[Multi-Provider DNS<br/>Cloudflare/Google/deSEC]
        end
    end
    
    WebDash --> GraphQL
    CLI --> GraphQL
    SDK --> GraphQL
    Docker --> Registry
    
    WebDash --> Auth
    CLI --> Auth
    SDK --> Auth
    
    GraphQL --> Sites
    GraphQL --> Functions
    GraphQL --> Agents
    GraphQL --> Storage
    GraphQL --> Billing
    
    Sites --> IPFS
    Sites --> Arweave
    Sites --> Filecoin
    Functions --> IPFS
    Agents --> IPFS
    Storage --> IPFS
    Storage --> Arweave
    Storage --> Filecoin
    
    GraphQL --> Postgres
    Auth --> Postgres
    Registry --> Postgres
    Registry --> IPFS
    
    GraphQL -.-> Secrets
    Auth -.-> Secrets
    Registry -.-> Secrets
    
    Proxy -.-> DNS
    Akash -.-> Proxy
```

---

## Architecture Layers

### 1. Client Layer

**Purpose**: User-facing interfaces and tools

| Component | Technology | Repository | Description |
|-----------|-----------|------------|-------------|
| Web Dashboard | Next.js 16 + React 19 | `web-app.alternatefutures.ai` | Main user interface |
| CLI | Node.js (Commander.js) | `package-cloud-cli` | Command-line tool (`af`) |
| SDK | TypeScript | `package-cloud-sdk` | JavaScript SDK for programmatic access |
| Docker Client | OCI Compatible | - | Standard Docker client for registry |

### 2. API Gateway Layer

**Purpose**: Entry points for all client requests

```mermaid
graph LR
    subgraph "API Gateway Layer"
        GraphQL[GraphQL API<br/>Yoga + Prisma<br/>api.alternatefutures.ai]
        Auth[Auth Service<br/>Hono + SQLite<br/>auth.alternatefutures.ai]
        Registry[Registry API<br/>OCI Compatible<br/>registry.alternatefutures.ai]
    end
    
    GraphQL --> DB[(PostgreSQL)]
    Auth --> AuthDB[(SQLite)]
    Registry --> RegDB[(PostgreSQL)]
    Registry --> IPFS[(IPFS)]
```

### 3. Service Layer

**Purpose**: Core business logic and functionality

| Service | Purpose | Technology | Repository |
|---------|---------|-----------|------------|
| Sites/Apps | Static site & app hosting | IPFS/Arweave/Filecoin | `service-cloud-api` |
| Functions | Serverless edge functions with Node.js runtime, Web APIs, SGX support | Runtime containers + IPFS | `service-cloud-api` |
| Agents | AI agent deployment | Container orchestration | `service-cloud-api` |
| Storage | Decentralized file storage | IPFS/Arweave/Filecoin | `service-cloud-api` |
| Billing | Payment processing | Stripe | `service-cloud-api` |
| Registry | Container image storage | IPFS + PostgreSQL | Separate stack |

### 4. Storage & Data Layer

**Purpose**: Persistent data storage

```mermaid
graph TB
    subgraph "Storage Layer"
        subgraph "Relational Data"
            PG[(PostgreSQL<br/>Metadata, Users,<br/>Sites, Deployments)]
            SQLite[(SQLite<br/>Auth Data)]
        end
        
        subgraph "Content Storage"
            IPFS[(IPFS/Kubo<br/>Content, Blobs,<br/>Files)]
            Arweave[(Arweave<br/>Permanent Storage)]
            Filecoin[(Filecoin<br/>Decentralized Storage)]
        end
        
        subgraph "Secrets"
            Infisical[(Infisical<br/>Secret Management)]
        end
    end
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

```mermaid
graph TB
    subgraph "Container Registry Stack"
        OpenReg[OpenRegistry<br/>OCI API Server]
        RegDB[(PostgreSQL<br/>Metadata)]
        RegIPFS[(IPFS Kubo<br/>Blob Storage)]
    end
    
    OpenReg <--> RegDB
    OpenReg --> RegIPFS
    
    Docker[Docker Client] -->|docker push/pull| OpenReg
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

### 4. Functions Service (Cloud Functions)

**Repository**: `alternatefutures/service-cloud-api`

**Technology Stack**:
- Node.js-compatible runtime
- IPFS (code storage)
- RuntimeRouter (request handling & routing)
- Optional SGX (Software Guard Extensions)

**Capabilities**:
- **Serverless Edge Execution**: Functions run on the edge, close to users
- **Web Standard APIs**: Full support for `fetch`, `Request`, `Response`, and other Web APIs
- **Node.js Compatibility**: Node.js-compatible runtime environment
- **Route Configuration**: Flexible route patterns (`/api/*`, `/users/:id`, etc.)
- **Automatic Scaling**: Scales based on demand
- **SGX Support**: Optional encrypted execution for confidential computing
- **IPFS Code Storage**: Function code stored and retrieved from IPFS via CID
- **Custom Domains**: Mount functions on custom domains or use default URLs

**Function Runtime Features**:
- API endpoints creation without managing servers
- Dynamic content generation
- Data processing and transformation
- Webhook handling
- Form processing
- Authentication middleware
- Image processing
- Cross-origin request support (CORS)

**URLs**:
- Default: `https://<function-slug>.af-functions.app`
- Custom domains supported

**Management**:
- CLI: `af functions create`, `af functions deploy`
- SDK: Full programmatic API via `@alternatefutures/sdk`
- Web Dashboard: Visual function management

**Architecture**:

```mermaid
graph TB
    subgraph "Function Runtime"
        Request[Incoming Request] --> Router[RuntimeRouter]
        Router -->|Route Match| Proxy[Proxy to Backend]
        Router -->|No Match| Executor[Function Executor]
        
        Executor --> IPFS[(IPFS<br/>Fetch Code by CID)]
        IPFS --> Runtime[Sandboxed Runtime<br/>Node.js + Web APIs]
        Runtime -->|Optional| SGX[SGX Enclave<br/>Encrypted Execution]
        
        Runtime --> Response[Response]
        Proxy --> Response
        SGX --> Response
    end
```

**Security**:
- Environment variable support for secrets
- SGX for encrypted execution and attestation
- Code integrity verification via BLAKE3 hash

See [Functions Documentation](https://github.com/alternatefutures/web-docs.alternatefutures.ai/blob/main/docs/guides/functions.md) for detailed usage guide.

### 5. Secrets Management (`service-secrets`)

**Repository**: `alternatefutures/service-secrets`

**Technology**: Infisical (self-hosted)

**Purpose**: Centralized secrets management for all services

**Architecture Decision**: Runs outside the SSL proxy for resilience - if proxy has issues, Infisical remains accessible.

**Endpoints**:
- `https://secrets.alternatefutures.ai`

### 6. Proxy Service (`infrastructure-proxy`)

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

### 7. DNS Management (`infrastructure-dns`)

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
- Next.js 16.0 (with Turbopack)
- React 19.1
- Radix UI (component library)
- Tailwind CSS 4
- TypeScript 5
- next-intl (internationalization)
- next-themes (theming support)
- Lucide React (icons)
- @xyflow/react (flow diagrams)
- react-tracked (state management)

**Features**:
- Project management
- Site deployment
- Function management
- Agent deployment
- API key management
- Billing dashboard
- Real-time deployment logs
- Multi-language support
- Dark/light theme support
- Visual workflow editor

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

```mermaid
graph TB
    Internet((Internet))
    
    Internet --> Proxy[SSL Proxy<br/>170.75.255.101]
    Internet --> Secrets[Secrets Service<br/>Direct/Isolated]
    
    Proxy --> Auth[auth.alternatefutures.ai]
    Proxy --> API[api.alternatefutures.ai]
    Proxy --> App[app.alternatefutures.ai]
    Proxy --> Docs[docs.alternatefutures.ai]
    Proxy --> Reg[registry.alternatefutures.ai]
    Proxy --> Web[alternatefutures.ai]
    
    style Secrets fill:#f9f,stroke:#333,stroke-width:2px
    style Proxy fill:#bbf,stroke:#333,stroke-width:2px
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

```mermaid
sequenceDiagram
    actor User
    participant CLI as CLI/Web UI
    participant Auth as Auth Service
    participant API as GraphQL API
    participant Storage as IPFS/Arweave
    participant DB as PostgreSQL
    participant Gateway as IPFS Gateway
    
    User->>CLI: af deploy / UI deploy
    CLI->>Auth: Authenticate
    Auth-->>CLI: JWT Token
    
    CLI->>API: Deploy request + JWT
    API->>API: Validate token
    API->>DB: Create deployment record
    
    CLI->>Storage: Upload files
    Storage-->>CLI: Return CID/hash
    
    CLI->>API: Update with CID
    API->>DB: Save metadata
    API->>DB: Update deployment status
    
    API-->>CLI: Deployment complete
    CLI-->>User: Success
    
    User->>Gateway: Access site
    Gateway->>Storage: Fetch content (CID)
    Storage-->>Gateway: Return files
    Gateway-->>User: Render site
```

### Registry Push Flow

```mermaid
sequenceDiagram
    actor User
    participant Docker as Docker Client
    participant Reg as OpenRegistry
    participant IPFS as IPFS/Kubo
    participant DB as PostgreSQL
    
    User->>Docker: docker push registry.af.ai/app:v1
    Docker->>Reg: Authenticate (JWT)
    Reg-->>Docker: 200 OK
    
    Docker->>Reg: Upload manifest
    Reg->>Reg: Split into layers (blobs)
    
    loop For each layer
        Reg->>IPFS: Store blob
        IPFS-->>Reg: Return CID
        Reg->>IPFS: Pin CID
        Reg->>DB: Store digest → CID mapping
    end
    
    Reg->>DB: Store manifest
    Reg-->>Docker: Push complete
    Docker-->>User: Success
```

### Authentication Flow

```mermaid
sequenceDiagram
    actor User
    participant Client as Client App
    participant Auth as Auth Service
    participant Provider as Auth Provider<br/>(Email/Web3/OAuth)
    participant API as GraphQL API
    
    User->>Client: Login request
    Client->>Auth: Initiate auth
    Auth->>Provider: Verify credentials
    
    alt Email Magic Link
        Provider->>User: Send magic link
        User->>Provider: Click link
    else Web3 Wallet
        Provider->>User: Sign message
        User->>Provider: Signed message
    else OAuth
        Provider->>User: OAuth flow
        User->>Provider: Authorize
    end
    
    Provider-->>Auth: Verification success
    Auth->>Auth: Generate JWT token
    Auth-->>Client: Return JWT
    
    Client->>Client: Store token
    
    User->>Client: Make API request
    Client->>API: Request + JWT token
    API->>API: Verify token
    API-->>Client: Response
    Client-->>User: Display result
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
| Framework | Next.js 16 | React framework with App Router |
| UI Library | React 19 | UI rendering |
| Component Library | Radix UI | Accessible component primitives |
| Styling | Tailwind CSS 4 | Utility-first CSS framework |
| Icons | Lucide React | Icon library |
| Flow Diagrams | @xyflow/react | Interactive node-based diagrams |
| State Management | react-tracked | State management |
| Internationalization | next-intl | i18n support |
| Theming | next-themes | Dark/light mode support |
| Build Tool | Turbopack | Next.js bundler |

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
| Linter (Frontend) | ESLint | Code linting/formatting |
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
| `web-app.alternatefutures.ai` | Web App | Next.js 16 + React 19 | User dashboard |
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
| `archived-web-app.alternatefutures.ai` | Web App | Archived (former SvelteKit version) |

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

```mermaid
graph TB
    subgraph "Security Layers"
        subgraph "External"
            CF[Cloudflare<br/>DDoS Protection]
            DNS[Multi-Provider DNS<br/>Failover]
        end
        
        subgraph "SSL/TLS"
            LE[Let's Encrypt<br/>DNS-01 Challenge]
            Proxy[SSL Proxy<br/>TLS 1.2+]
        end
        
        subgraph "Application"
            Rate[Rate Limiting]
            JWT[JWT Validation]
            GraphQL[GraphQL Complexity<br/>Analysis]
        end
        
        subgraph "Data"
            Encrypt[TLS/HTTPS<br/>In Transit]
            Vault[Infisical<br/>Secrets Vault]
            CID[IPFS CID<br/>Verification]
        end
    end
    
    CF --> Proxy
    DNS -.-> CF
    LE --> Proxy
    Proxy --> Rate
    Rate --> JWT
    JWT --> GraphQL
    GraphQL --> Encrypt
    Encrypt --> Vault
    Vault -.-> CID
```

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

```mermaid
graph LR
    subgraph "Akash Network"
        SDL[SDL Definition] -->|Deploy| Marketplace[Akash Marketplace]
        Marketplace -->|Bids| P1[Provider 1]
        Marketplace -->|Bids| P2[Provider 2]
        Marketplace -->|Bids| P3[Provider 3]
        
        P1 -->|Selected| Deployment[Running Deployment]
        
        Deployment --> Monitor[Monitoring/Health]
        Deployment --> Storage[(Persistent Storage)]
    end
    
    User((User)) -->|Create| SDL
    User -->|Monitor| Monitor
```

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

```mermaid
graph TB
    subgraph "High Availability Strategy"
        subgraph "DNS Layer"
            CF[Cloudflare<br/>Primary]
            GCP[Google DNS<br/>Secondary]
            DeSEC[deSEC<br/>Secondary]
        end
        
        subgraph "Storage Layer"
            P1[Pinata]
            P2[Web3.Storage]
            P3[Lighthouse]
            IPFS[IPFS]
            AR[Arweave]
            FC[Filecoin]
        end
        
        subgraph "Compute Layer"
            Akash1[Akash Provider 1]
            Akash2[Akash Provider 2<br/>Failover]
        end
    end
    
    CF -.->|Failover| GCP
    GCP -.->|Failover| DeSEC
    
    P1 -.->|Redundancy| IPFS
    P2 -.->|Redundancy| IPFS
    P3 -.->|Redundancy| IPFS
    IPFS -.->|Backup| AR
    IPFS -.->|Backup| FC
    
    Akash1 -.->|Failover| Akash2
```

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

```mermaid
graph TB
    subgraph "Service-to-Service"
        API[GraphQL API] <-->|JWT Validation| Auth[Auth Service]
        API <-->|Prisma ORM| PG[(PostgreSQL)]
        Reg[OpenRegistry] <-->|SQL| RegDB[(PostgreSQL)]
        Reg -->|HTTP API| IPFS[(IPFS)]
        
        API -.->|Secrets| Inf[Infisical]
        Auth -.->|Secrets| Inf
        Reg -.->|Secrets| Inf
    end
    
    subgraph "Client-to-Service"
        WebUI[Web Dashboard] -->|GraphQL| API
        CLI[CLI Tool] -->|GraphQL| API
        SDK[SDK] -->|GraphQL| API
        Docker[Docker Client] -->|OCI HTTP| Reg
    end
```

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

```mermaid
graph TB
    subgraph "Horizontal Scaling Architecture"
        LB[Load Balancer]
        
        LB --> API1[GraphQL API<br/>Instance 1]
        LB --> API2[GraphQL API<br/>Instance 2]
        LB --> API3[GraphQL API<br/>Instance 3]
        
        API1 --> DB[(Shared PostgreSQL)]
        API2 --> DB
        API3 --> DB
        
        RegLB[Registry LB]
        RegLB --> Reg1[Registry<br/>Instance 1]
        RegLB --> Reg2[Registry<br/>Instance 2]
        
        Reg1 --> RegDB[(Shared PostgreSQL)]
        Reg2 --> RegDB
        
        Reg1 --> SharedIPFS[(Shared IPFS)]
        Reg2 --> SharedIPFS
    end
```

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

```mermaid
graph LR
    subgraph "Monitoring Stack"
        subgraph "Application Metrics"
            AppRate[Request Rate]
            AppLatency[Latency]
            AppErrors[Error Rate]
            GraphQLComp[GraphQL Complexity]
        end
        
        subgraph "Infrastructure Metrics"
            CPU[CPU Usage]
            Memory[Memory Usage]
            Disk[Disk Usage]
            Network[Network Bandwidth]
            IPFS[IPFS Repo Size]
        end
        
        subgraph "Business Metrics"
            Users[Active Users]
            Deploys[Deployments/Day]
            StorageUse[Storage Usage]
            Revenue[Revenue]
        end
    end
    
    AppRate --> Dashboard[Monitoring Dashboard]
    AppLatency --> Dashboard
    AppErrors --> Dashboard
    GraphQLComp --> Dashboard
    CPU --> Dashboard
    Memory --> Dashboard
    Disk --> Dashboard
    Network --> Dashboard
    IPFS --> Dashboard
    Users --> Dashboard
    Deploys --> Dashboard
    StorageUse --> Dashboard
    Revenue --> Dashboard
```

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

1. **MCP Server Integration**:
   - Model Context Protocol servers for all services
   - Enable AI assistants (Claude, ChatGPT, etc.) to interact with platform
   - **Deployments MCP**: Manage Akash deployments, view logs, check status
   - **Functions MCP**: Create, deploy, and manage serverless functions
   - **Registry MCP**: Push/pull container images, manage repositories
   - **Sites MCP**: Deploy static sites, update content, manage domains
   - **Agents MCP**: Deploy and monitor AI agents
   - **Billing MCP**: View usage, manage subscriptions, access invoices
   - Standardized tool definitions for each service domain
   - Authentication via Personal Access Tokens
   - Real-time status updates and notifications

2. **Registry**:
   - IPFS cluster for redundancy
   - Image signing (Cosign)
   - Vulnerability scanning (Trivy)
   - Cross-region replication
   - Usage analytics
   - Webhook notifications

3. **Platform**:
   - Real-time collaboration
   - AI agent marketplace
   - Template marketplace
   - Custom domain SSL automation
   - Enhanced function analytics and monitoring

4. **Infrastructure**:
   - Multi-cloud support (beyond Akash)
   - Backup/restore automation
   - Disaster recovery
   - Performance optimization

---

## References

### Documentation

- [Functions Documentation](https://github.com/alternatefutures/web-docs.alternatefutures.ai/blob/main/docs/guides/functions.md)
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
- [Model Context Protocol](https://modelcontextprotocol.io)

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
- **MCP**: Model Context Protocol (AI assistant integration standard)
- **OCI**: Open Container Initiative
- **SDL**: Service Definition Language (Akash)
- **SGX**: Software Guard Extensions (Intel trusted execution)
- **SIWE**: Sign-In with Ethereum
- **SSL**: Secure Sockets Layer
- **TLS**: Transport Layer Security

---

**Last Updated**: 2026-01-21  
**Version**: 2.3.0  
**Maintainers**: Alternate Futures Team
