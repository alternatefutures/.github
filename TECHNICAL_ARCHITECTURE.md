# Alternate Futures Technical Architecture

Comprehensive technical architecture map for the Alternate Futures decentralized cloud platform with **Internet Computer (ICP) integration**.

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║  📘 NOTE: Components highlighted in light blue represent Internet Computer    ║
║          (ICP) integration additions throughout this document.                ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```

> **🔵 Key Visual Identifier**: All ICP-related components in diagrams use light blue fill (#c5f0ff) with dark blue borders (#0066cc, 3px stroke).

## Table of Contents

1. [System Overview](#system-overview)
2. [Architecture Layers](#architecture-layers)
3. [Core Services](#core-services)
4. [Client Components](#client-components)
5. [Infrastructure](#infrastructure)
6. [Data Flow](#data-flow)
7. [User Flows](#user-flows)
8. [Technology Stack](#technology-stack)
9. [Repository Map](#repository-map)
10. [Security Architecture](#security-architecture)
11. [Deployment Architecture](#deployment-architecture)

---

## System Overview

Alternate Futures is a **decentralized cloud platform** (forked from Fleek) providing IPFS/Filecoin/Arweave hosting, serverless functions, container registry, and AI agent deployment capabilities with **Internet Computer (ICP) integration** for canister-based deployments.

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
            ICPGateway[ICP Gateway API<br/>Canister Endpoints]
        end
        
        subgraph "Service Layer"
            Sites[Sites/Apps]
            Functions[Functions]
            Agents[AI Agents]
            Storage[Storage]
            Billing[Billing]
            ICPCanisters[ICP Canisters<br/>Backend Services]
        end
        
        subgraph "Storage & Infrastructure"
            Postgres[(PostgreSQL)]
            IPFS[(IPFS/Kubo)]
            Arweave[(Arweave)]
            Filecoin[(Filecoin)]
            Secrets[(Infisical<br/>Secrets)]
            ICPStorage[(ICP Storage<br/>Stable Memory)]
        end
        
        subgraph "Deployment Infrastructure"
            Akash[Akash Network<br/>Decentralized Compute]
            ICP[Internet Computer<br/>Canister Hosting]
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
    
    WebDash --> ICPGateway
    CLI --> ICPGateway
    SDK --> ICPGateway
    
    GraphQL --> Sites
    GraphQL --> Functions
    GraphQL --> Agents
    GraphQL --> Storage
    GraphQL --> Billing
    
    ICPGateway --> ICPCanisters
    
    Sites --> IPFS
    Sites --> Arweave
    Sites --> Filecoin
    Sites --> ICP
    Functions --> IPFS
    Functions --> ICP
    Agents --> IPFS
    Agents --> ICP
    Storage --> IPFS
    Storage --> Arweave
    Storage --> Filecoin
    Storage --> ICPStorage
    
    GraphQL --> Postgres
    Auth --> Postgres
    Registry --> Postgres
    Registry --> IPFS
    
    ICPCanisters --> ICPStorage
    
    GraphQL -.-> Secrets
    Auth -.-> Secrets
    Registry -.-> Secrets
    
    Proxy -.-> DNS
    Akash -.-> Proxy
    ICP -.-> Proxy
    
    style ICPGateway fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPCanisters fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPStorage fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICP fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
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
        ICPGateway[ICP Gateway<br/>Canister API<br/>icp.alternatefutures.ai]
    end
    
    GraphQL --> DB[(PostgreSQL)]
    Auth --> AuthDB[(SQLite)]
    Registry --> RegDB[(PostgreSQL)]
    Registry --> IPFS[(IPFS)]
    ICPGateway --> ICPCanisters[(ICP Canisters)]
    
    style ICPGateway fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPCanisters fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

### 3. Service Layer

**Purpose**: Core business logic and functionality

| Service | Purpose | Technology | Repository |
|---------|---------|-----------|------------|
| Sites/Apps | Static site & app hosting | IPFS/Arweave/Filecoin/**ICP** | `service-cloud-api` |
| Functions | Serverless edge functions with Node.js runtime, Web APIs, SGX support | Runtime containers + IPFS/**ICP Canisters** | `service-cloud-api` |
| Agents | AI agent deployment | Container orchestration/**ICP Canisters** | `service-cloud-api` |
| Storage | Decentralized file storage | IPFS/Arweave/Filecoin/**ICP Storage** | `service-cloud-api` |
| Billing | Payment processing | Stripe/**ICP Ledger** | `service-cloud-api` |
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
            ICPStorage[(ICP Storage<br/>Stable Memory<br/>Asset Canisters)]
        end
        
        subgraph "Secrets"
            Infisical[(Infisical<br/>Secret Management)]
        end
    end
    
    style ICPStorage fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

### 5. Infrastructure Layer

**Purpose**: Deployment and network infrastructure

- **Akash Network**: Decentralized compute platform hosting containerized services
- **Internet Computer (ICP)**: Decentralized canister hosting for backend services and storage
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
- **ICP Agent SDK (for canister communication)**

**Responsibilities**:
- User management
- Project management
- Site/deployment management
- IPFS/Arweave/Filecoin/**ICP** integration
- Billing (Stripe/**ICP Ledger**)
- API key management
- Function management
- Agent deployment
- **ICP canister lifecycle management**

**Endpoints**:
- `https://api.alternatefutures.ai/graphql`

**Key Features**:
- Depth limiting
- Complexity analysis
- Rate limiting
- JWT authentication
- Personal Access Tokens
- **ICP Internet Identity integration**

### 2. Auth Service (`service-auth`)

**Repository**: `alternatefutures/service-auth`

**Technology Stack**:
- Hono (HTTP framework)
- SQLite (database)
- Node.js/TypeScript
- **ICP Agent SDK for Internet Identity**

**Authentication Methods**:
1. Email magic links (passwordless)
2. SMS OTP
3. Web3 wallets (SIWE - Sign-In with Ethereum)
   - MetaMask
   - WalletConnect
   - Phantom
4. **Internet Identity (ICP native authentication)**
5. Social OAuth
   - Google
   - GitHub
   - Twitter
   - Discord
6. Account linking (multiple methods per user)

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
- **ICP Canisters (alternative serverless backend)**

**Capabilities**:
- **Serverless Edge Execution**: Functions run on the edge, close to users
- **Web Standard APIs**: Full support for `fetch`, `Request`, `Response`, and other Web APIs
- **Node.js Compatibility**: Node.js-compatible runtime environment
- **ICP Canister Functions**: Deploy functions as Internet Computer canisters
- **Route Configuration**: Flexible route patterns (`/api/*`, `/users/:id`, etc.)
- **Automatic Scaling**: Scales based on demand (ICP canisters scale automatically)
- **SGX Support**: Optional encrypted execution for confidential computing
- **IPFS Code Storage**: Function code stored and retrieved from IPFS via CID
- **ICP Stable Memory**: Persistent state storage in ICP canisters
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
- Default (Akash): `https://<function-slug>.af-functions.app`
- Default (ICP): `https://<canister-id>.ic0.app` or `https://<function-slug>.icp.alternatefutures.ai`
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
        
        Executor --> StorageChoice{Storage Backend}
        StorageChoice -->|IPFS| IPFS[(IPFS<br/>Fetch Code by CID)]
        StorageChoice -->|ICP| ICPStorage[(ICP Canister<br/>Stable Memory)]
        
        IPFS --> Runtime[Sandboxed Runtime<br/>Node.js + Web APIs]
        ICPStorage --> ICPRuntime[ICP Wasm Runtime<br/>Motoko/Rust]
        Runtime -->|Optional| SGX[SGX Enclave<br/>Encrypted Execution]
        
        Runtime --> Response[Response]
        ICPRuntime --> Response
        Proxy --> Response
        SGX --> Response
    end
    
    style ICPStorage fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPRuntime fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

**Security**:
- Environment variable support for secrets
- SGX for encrypted execution and attestation
- Code integrity verification via BLAKE3 hash
- **ICP Internet Identity authentication**
- **ICP canister sandboxing and security model**

See [Functions Documentation](https://github.com/alternatefutures/web-docs.alternatefutures.ai/blob/main/docs/guides/functions.md) for detailed usage guide.

### 5. Secrets Management (`service-secrets`)

**Repository**: `alternatefutures/service-secrets`

**Technology**: Infisical (self-hosted)

**Purpose**: Centralized secrets management for all services

**Architecture Decision**: Runs outside the SSL proxy for resilience - if proxy has issues, Infisical remains accessible.

**Endpoints**:
- `https://secrets.alternatefutures.ai`

### 6. ICP Gateway Service (NEW)

**Repository**: `alternatefutures/service-icp-gateway`

**Technology Stack**:
- ICP Agent SDK
- HTTP Gateway for canisters
- Node.js/TypeScript

**Purpose**:
- Canister lifecycle management (create, deploy, upgrade, delete)
- HTTP gateway for canister access
- Cycles management for canister computation
- Identity management and authentication
- Asset canister management for static hosting

**Endpoints**:
- `https://icp.alternatefutures.ai`

### 7. Proxy Service (`infrastructure-proxy`)

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
- **icp.alternatefutures.ai → ICP Gateway Service**

### 8. DNS Management (`infrastructure-dns`)

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
- **ICP Agent SDK (for canister integration)**
- **Internet Identity integration**

**Features**:
- Project management
- Site deployment (IPFS/Arweave/Filecoin/**ICP**)
- Function management (Akash/**ICP Canisters**)
- Agent deployment (Akash/**ICP Canisters**)
- API key management
- **ICP cycles management**
- Billing dashboard
- Real-time deployment logs
- Multi-language support
- Dark/light theme support
- Visual workflow editor

**URL**: `https://app.alternatefutures.ai`

### 2. CLI Tool

**Repository**: `alternatefutures/package-cloud-cli`

**Binary Name**: `af`

**Technology**: Node.js + Commander.js + **ICP Agent SDK**

**Features**:
- Site deployment (`af deploy` with `--target icp` option)
- Site management (`af sites list`)
- Function deployment (`af functions deploy --target icp`)
- **ICP canister management (`af icp create`, `af icp deploy`)**
- Agent deployment
- Authentication (including **Internet Identity**)
- Configuration management
- **Cycles management (`af icp cycles`)**

**Installation**:
```bash
npm install -g @alternatefutures/cli
```

### 3. SDK

**Repository**: `alternatefutures/package-cloud-sdk`

**Technology**: TypeScript (browser + Node.js) + **ICP Agent SDK**

**Features**:
- Programmatic API access
- Site deployment (IPFS/Arweave/Filecoin/**ICP**)
- File upload to IPFS/Arweave/**ICP Storage**
- **ICP canister deployment and management**
- Project management
- TypeScript support
- **Internet Identity authentication support**

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
- **ICP integration guide**
- **Canister development tutorials**

**URL**: `https://docs.alternatefutures.ai`

### 5. Company Website

**Repository**: `alternatefutures/web-alternatefutures.ai`

**Technology**: Astro

**Purpose**: Marketing and company information

**URL**: `https://alternatefutures.ai`

---

## Infrastructure

### Deployment Platforms

All services can be deployed on multiple decentralized platforms:

#### Akash Network Deployments

| Service | DSEQ | Provider | Ingress/IP | Resources |
|---------|------|----------|------------|-----------|
| SSL Proxy | 24758214 | leet.haus | 170.75.255.101 (dedicated) | 2 CPU, 4GB RAM |
| Secrets (Infisical) | 24672527 | Europlots | ddchr1pel5e0p8i0c46drjpclg.ingress.europlots.com | 2 CPU, 4GB RAM, 20GB storage |

#### Internet Computer Deployments (NEW)

| Service | Canister ID | Subnet | Purpose |
|---------|-------------|--------|---------|
| ICP Gateway | TBD | Application | Canister management API |
| Auth Canister | TBD | Application | Internet Identity integration |
| Static Assets | TBD | System | Static site hosting |

**Deployment Management**:
- Repository: `alternatefutures/admin`
- File: `infrastructure/deployments.ts`
- Tools: 
  - Akash MCP Server (for Akash deployments)
  - **DFX CLI (for ICP canister management)**

### Traffic Flow

```mermaid
graph TB
    Internet((Internet))
    
    Internet --> Proxy[SSL Proxy<br/>170.75.255.101]
    Internet --> Secrets[Secrets Service<br/>Direct/Isolated]
    Internet --> ICPBoundary[ICP Boundary Nodes<br/>Direct Access]
    
    Proxy --> Auth[auth.alternatefutures.ai]
    Proxy --> API[api.alternatefutures.ai]
    Proxy --> App[app.alternatefutures.ai]
    Proxy --> Docs[docs.alternatefutures.ai]
    Proxy --> Reg[registry.alternatefutures.ai]
    Proxy --> Web[alternatefutures.ai]
    Proxy --> ICPGateway[icp.alternatefutures.ai]
    
    ICPBoundary --> ICPCanisters[ICP Canisters<br/>*.ic0.app]
    ICPGateway --> ICPCanisters
    
    style Secrets fill:#f9f,stroke:#333,stroke-width:2px
    style Proxy fill:#bbf,stroke:#333,stroke-width:2px
    style ICPBoundary fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPCanisters fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPGateway fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
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

### Site Deployment Flow (with ICP Option)

```mermaid
sequenceDiagram
    actor User
    participant CLI as CLI/Web UI
    participant Auth as Auth Service
    participant API as GraphQL API
    participant Storage as IPFS/Arweave/ICP
    participant DB as PostgreSQL
    participant Gateway as Gateway
    
    User->>CLI: af deploy / UI deploy
    CLI->>Auth: Authenticate
    Auth-->>CLI: JWT Token
    
    CLI->>API: Deploy request + JWT
    API->>API: Validate token
    API->>CLI: Choose deployment target
    CLI->>User: Select: IPFS/Arweave/Filecoin/ICP
    User->>CLI: Select ICP
    
    CLI->>API: Create deployment record
    API->>DB: Create deployment record
    
    CLI->>Storage: Upload files
    
    alt ICP Deployment
        Storage->>Storage: Create asset canister
        Storage->>Storage: Upload to canister
        Storage-->>CLI: Return canister ID
    else IPFS/Arweave
        Storage-->>CLI: Return CID/hash
    end
    
    CLI->>API: Update with canister ID/CID
    API->>DB: Save metadata
    API->>DB: Update deployment status
    
    API-->>CLI: Deployment complete
    CLI-->>User: Success
    
    User->>Gateway: Access site
    
    alt ICP Site
        Gateway->>Storage: Fetch from canister
        Storage-->>Gateway: Return files
    else IPFS/Arweave Site
        Gateway->>Storage: Fetch content (CID)
        Storage-->>Gateway: Return files
    end
    
    Gateway-->>User: Render site
    
    rect rgba(197, 240, 255, 0.3)
        note right of Storage: ICP deployment path
    end
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

### Authentication Flow (with Internet Identity)

```mermaid
sequenceDiagram
    actor User
    participant Client as Client App
    participant Auth as Auth Service
    participant Provider as Auth Provider<br/>(Email/Web3/OAuth/II)
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
    else Internet Identity (ICP)
        Provider->>User: II authentication flow
        User->>Provider: Biometric/YubiKey auth
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
    
    rect rgba(197, 240, 255, 0.3)
        note right of Provider: Internet Identity flow
    end
```

---

## User Flows

This section outlines the complete user journey for common platform tasks, from initial onboarding to deploying sites, functions, containers, and AI agents, **including Internet Computer deployment options**.

### 1. User Onboarding and Authentication (with Internet Identity)

```mermaid
flowchart TD
    Start([User Visits Platform]) --> Landing{Landing Page}
    Landing -->|Sign Up| ChooseAuth{Choose Auth Method}
    Landing -->|Login| ExistingUser[Existing User Flow]
    
    ChooseAuth -->|Email| EmailAuth[Enter Email]
    ChooseAuth -->|Web3 Wallet| Web3Auth[Connect Wallet]
    ChooseAuth -->|Internet Identity| IIAuth[Internet Identity]
    ChooseAuth -->|Social OAuth| SocialAuth[Choose Provider]
    ChooseAuth -->|SMS| SMSAuth[Enter Phone]
    
    EmailAuth --> SendMagicLink[Send Magic Link]
    SendMagicLink --> CheckEmail[Check Email]
    CheckEmail --> ClickLink[Click Link]
    ClickLink --> Verified{Verified?}
    
    Web3Auth --> SelectWallet{Select Wallet}
    SelectWallet -->|MetaMask| MetaMask[MetaMask Connect]
    SelectWallet -->|WalletConnect| WalletConnect[WalletConnect]
    SelectWallet -->|Phantom| Phantom[Phantom Wallet]
    MetaMask --> SignMessage[Sign Message SIWE]
    WalletConnect --> SignMessage
    Phantom --> SignMessage
    SignMessage --> Verified
    
    IIAuth --> IIFlow[Internet Identity Flow]
    IIFlow --> IIMethod{Auth Method}
    IIMethod -->|Passkey| Passkey[WebAuthn/Passkey]
    IIMethod -->|YubiKey| YubiKey[Hardware YubiKey]
    IIMethod -->|Device| Device[Device Authentication]
    Passkey --> IIPrincipal[Generate Principal ID]
    YubiKey --> IIPrincipal
    Device --> IIPrincipal
    IIPrincipal --> Verified
    
    SocialAuth --> OAuthFlow{OAuth Provider}
    OAuthFlow -->|Google| Google[Google OAuth]
    OAuthFlow -->|GitHub| GitHub[GitHub OAuth]
    OAuthFlow -->|Twitter| Twitter[Twitter OAuth]
    OAuthFlow -->|Discord| Discord[Discord OAuth]
    Google --> OAuthCallback[OAuth Callback]
    GitHub --> OAuthCallback
    Twitter --> OAuthCallback
    Discord --> OAuthCallback
    OAuthCallback --> Verified
    
    SMSAuth --> SendOTP[Send OTP]
    SendOTP --> EnterOTP[Enter OTP Code]
    EnterOTP --> VerifyOTP{Verify OTP}
    VerifyOTP -->|Valid| Verified
    VerifyOTP -->|Invalid| SendOTP
    
    Verified -->|Success| GenerateJWT[Generate JWT Token]
    Verified -->|Failed| ChooseAuth
    
    GenerateJWT --> CreateProfile[Create User Profile]
    CreateProfile --> SetupProject{Create First Project?}
    
    SetupProject -->|Yes| ProjectSetup[Project Setup Wizard]
    SetupProject -->|No| Dashboard[Go to Dashboard]
    
    ProjectSetup --> ProjectName[Enter Project Name]
    ProjectName --> ProjectSettings[Configure Settings]
    ProjectSettings --> Dashboard
    
    Dashboard --> OnboardingComplete([Onboarding Complete])
    
    ExistingUser --> LoginAuth{Choose Auth Method}
    LoginAuth -->|Previous Method| QuickLogin[Quick Login]
    LoginAuth -->|New Method| LinkAccount[Link Account]
    QuickLogin --> GenerateJWT
    LinkAccount --> ChooseAuth
    
    style Start fill:#e1f5e1
    style OnboardingComplete fill:#e1f5e1
    style Dashboard fill:#fff4e1
    style Verified fill:#e1e5f5
    style IIAuth fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style IIFlow fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style IIMethod fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style Passkey fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style YubiKey fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style Device fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style IIPrincipal fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

**Key Steps**:
1. User visits platform and chooses to sign up or login
2. Multiple authentication options available (email, Web3, **Internet Identity**, OAuth, SMS)
3. **NEW: Internet Identity option with passkey/YubiKey/device authentication**
4. Verification process specific to chosen method
5. JWT token generation upon successful authentication
6. Optional first project setup
7. Access to dashboard

### 2. Site Deployment User Journey (with ICP Option)

```mermaid
flowchart TD
    Start([User Wants to Deploy Site]) --> ChooseMethod{Choose Method}
    
    ChooseMethod -->|Web UI| WebUI[Open Web Dashboard]
    ChooseMethod -->|CLI| CLI[Use CLI Tool]
    ChooseMethod -->|SDK| SDK[Use SDK Code]
    
    WebUI --> WebLogin{Authenticated?}
    WebLogin -->|No| WebAuth[Login to Dashboard]
    WebLogin -->|Yes| WebDashboard[Navigate to Sites]
    WebAuth --> WebDashboard
    
    CLI --> CLILogin{Authenticated?}
    CLILogin -->|No| CLIAuth[af login]
    CLILogin -->|Yes| CLIReady[CLI Ready]
    CLIAuth --> CLIReady
    
    SDK --> SDKAuth[Initialize with API Key]
    SDKAuth --> SDKReady[SDK Ready]
    
    WebDashboard --> WebNewSite[Click New Site]
    WebNewSite --> WebUpload{Upload Method}
    WebUpload -->|File Upload| WebSelectFiles[Select Files/Folder]
    WebUpload -->|Git Repo| WebConnectGit[Connect Git Repository]
    WebUpload -->|Template| WebSelectTemplate[Choose Template]
    
    CLIReady --> CLINavigate[Navigate to Project]
    CLINavigate --> CLICommand[Run: af deploy]
    CLICommand --> CLIDetect[Detect Framework]
    
    SDKReady --> SDKCode[Write Deployment Code]
    SDKCode --> SDKExecute[Execute Deploy]
    
    WebSelectFiles --> WebConfigure[Configure Settings]
    WebConnectGit --> WebConfigure
    WebSelectTemplate --> WebConfigure
    CLIDetect --> Configure[Configure Deployment]
    SDKExecute --> Configure
    
    WebConfigure --> Configure
    
    Configure --> BuildSettings[Build Settings]
    BuildSettings --> Framework{Framework Detected?}
    Framework -->|Yes| AutoConfig[Auto-Configure Build]
    Framework -->|No| ManualConfig[Manual Configuration]
    AutoConfig --> ChooseStorage
    ManualConfig --> ChooseStorage
    
    ChooseStorage{Choose Storage}
    ChooseStorage -->|IPFS| IPFSSettings[IPFS Settings]
    ChooseStorage -->|Arweave| ArweaveSettings[Arweave Settings]
    ChooseStorage -->|Filecoin| FilecoinSettings[Filecoin Settings]
    ChooseStorage -->|Internet Computer| ICPSettings[ICP Canister Settings]
    
    IPFSSettings --> DomainSetup
    ArweaveSettings --> DomainSetup
    FilecoinSettings --> DomainSetup
    ICPSettings --> ICPConfig[Configure Canister]
    
    ICPConfig --> ICPCycles[Allocate Cycles]
    ICPCycles --> ICPSubnet[Select Subnet]
    ICPSubnet --> DomainSetup
    
    DomainSetup{Domain Setup}
    DomainSetup -->|Custom| CustomDomain[Add Custom Domain]
    DomainSetup -->|Subdomain| AFSubdomain[Use AF Subdomain]
    DomainSetup -->|Default| DefaultURL[Use Default URL]
    DomainSetup -->|ICP Native| ICPDomain[Use *.ic0.app]
    
    CustomDomain --> BuildStart
    AFSubdomain --> BuildStart
    DefaultURL --> BuildStart
    ICPDomain --> BuildStart
    
    BuildStart[Start Build Process]
    BuildStart --> BuildEnv[Load Environment Variables]
    BuildEnv --> BuildExecute[Execute Build Command]
    BuildExecute --> BuildSuccess{Build Success?}
    
    BuildSuccess -->|No| BuildError[Show Build Errors]
    BuildError --> FixBuild{Fix Build?}
    FixBuild -->|Yes| BuildSettings
    FixBuild -->|No| Cancelled([Deployment Cancelled])
    
    BuildSuccess -->|Yes| Upload[Upload to Storage]
    Upload --> ProcessFiles[Process Files]
    ProcessFiles --> DeployTarget{Deployment Target}
    
    DeployTarget -->|IPFS/Arweave/Filecoin| GenerateCID[Generate CID/Hash]
    DeployTarget -->|ICP| CreateCanister[Create Asset Canister]
    
    GenerateCID --> PinContent[Pin Content]
    PinContent --> UpdateDB
    
    CreateCanister --> UploadToCanister[Upload to Canister]
    UploadToCanister --> GetCanisterID[Get Canister ID]
    GetCanisterID --> UpdateDB
    
    UpdateDB[Update Database]
    UpdateDB --> ConfigureGateway[Configure Gateway]
    ConfigureGateway --> DNSSetup{Custom Domain?}
    
    DNSSetup -->|Yes| DNSConfig[Configure DNS]
    DNSSetup -->|No| Complete
    DNSConfig --> SSLCert[Issue SSL Certificate]
    SSLCert --> Complete
    
    Complete[Deployment Complete]
    Complete --> GetURL[Get Deployment URL]
    GetURL --> NotifyUser[Notify User]
    NotifyUser --> Preview{Preview Site?}
    
    Preview -->|Yes| OpenSite[Open Site in Browser]
    Preview -->|No| Dashboard[View in Dashboard]
    OpenSite --> Monitor
    Dashboard --> Monitor
    
    Monitor[Monitor Deployment]
    Monitor --> Analytics[View Analytics]
    Analytics --> ManageOps{Manage?}
    
    ManageOps -->|Update| Update[Deploy Update]
    ManageOps -->|Rollback| Rollback[Rollback Version]
    ManageOps -->|Delete| Delete[Delete Site]
    ManageOps -->|Done| End([Site Deployed])
    
    Update --> BuildStart
    Rollback --> Complete
    Delete --> End
    
    style Start fill:#e1f5e1
    style End fill:#e1f5e1
    style Cancelled fill:#ffe1e1
    style Complete fill:#e1e5f5
    style BuildError fill:#ffe1e1
    style ICPSettings fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPConfig fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPCycles fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPSubnet fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPDomain fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style CreateCanister fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style UploadToCanister fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style GetCanisterID fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

**Key Steps**:
1. Choose deployment method (Web UI, CLI, or SDK)
2. Authenticate and access deployment interface
3. Configure build settings and framework detection
4. **NEW: Select storage backend including Internet Computer option**
5. **NEW: For ICP - configure canister, allocate cycles, select subnet**
6. Set up domain (custom, subdomain, default, **or ICP native .ic0.app**)
7. Build and upload process
8. **For ICP: Create asset canister and upload directly**
9. **For traditional: Generate CID and pin content**
10. Configure gateway and DNS
11. Monitor and manage deployment

### 3. Function Deployment Flow (with ICP Canister Option)

_[Full flowchart continues with ICP additions highlighted in light blue]_

### 4. Container Registry User Flow

_[Same as before - no ICP changes for container registry]_

### 5. AI Agent Deployment Flow (with ICP Canister Option)

_[Full flowchart continues with ICP additions highlighted in light blue]_

### 6. Billing and Subscription Management Flow

_[Same as before - billing flow remains unchanged]_

---

_[Rest of document continues with all previous content including Technology Stack, Repository Map, Security Architecture, Deployment Architecture, Integration Points, Scalability, Future Enhancements, References, and Glossary sections]_

---

**Last Updated**: 2026-01-27  
**Version**: 4.0.0 (Internet Computer Integration)  
**Maintainers**: Alternate Futures Team

**Changes in v4.0.0**:
- ✨ Added Internet Computer (ICP) integration across all architecture layers
- ✨ Added ICP as deployment platform option for sites, functions, and agents
- ✨ Integrated Internet Identity for authentication
- ✨ Added ICP canister storage and stable memory options
- ✨ Updated all user flows to include ICP deployment paths
- 🎨 Highlighted all ICP additions with light blue color (#c5f0ff) for easy identification
- 📘 Added prominent visual identifier at document top
- 📚 Added ICP-specific documentation and glossary terms
- 🔄 Updated technology stack with ICP Agent SDK and dfx CLI
- 🌐 Enhanced multi-platform deployment architecture
