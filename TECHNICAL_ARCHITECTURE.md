# Alternate Futures Technical Architecture

Comprehensive technical architecture map for the Alternate Futures decentralized cloud platform with **Internet Computer (ICP) integration**.

> **Note**: Components highlighted in light blue represent Internet Computer integration additions.

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

```mermaid
flowchart TD
    Start([Deploy Serverless Function]) --> ChooseInterface{Choose Interface}
    
    ChooseInterface -->|CLI| CLIFlow[CLI Workflow]
    ChooseInterface -->|Web UI| WebFlow[Web Dashboard]
    ChooseInterface -->|SDK| SDKFlow[SDK Integration]
    
    CLIFlow --> CLIAuth{Authenticated?}
    CLIAuth -->|No| CLILogin[af login]
    CLIAuth -->|Yes| CLICreate
    CLILogin --> CLICreate[af functions create]
    
    WebFlow --> WebAuth{Authenticated?}
    WebAuth -->|No| WebLogin[Login to Dashboard]
    WebAuth -->|Yes| WebNav
    WebLogin --> WebNav[Navigate to Functions]
    WebNav --> WebNew[Click New Function]
    
    SDKFlow --> SDKInit[Initialize SDK]
    SDKInit --> SDKCreate[Create Function Code]
    
    CLICreate --> FunctionName[Enter Function Name]
    WebNew --> FunctionName
    SDKCreate --> FunctionName
    
    FunctionName --> DeploymentTarget{Deployment Target}
    DeploymentTarget -->|Akash/Traditional| TraditionalRuntime[Traditional Runtime]
    DeploymentTarget -->|ICP Canister| ICPCanisterRuntime[ICP Canister]
    
    TraditionalRuntime --> Runtime{Choose Runtime}
    Runtime -->|Node.js| NodeRuntime[Node.js Runtime]
    Runtime -->|Web APIs| WebRuntime[Web Standards]
    
    NodeRuntime --> CodeSource
    WebRuntime --> CodeSource
    
    ICPCanisterRuntime --> ICPLanguage{Canister Language}
    ICPLanguage -->|Motoko| MotokoLang[Motoko Development]
    ICPLanguage -->|Rust| RustLang[Rust Development]
    ICPLanguage -->|Azle Node.js| AzleLang[Azle JavaScript/TS]
    
    MotokoLang --> CodeSource
    RustLang --> CodeSource
    AzleLang --> CodeSource
    
    CodeSource{Code Source}
    CodeSource -->|Write Inline| InlineEditor[Inline Code Editor]
    CodeSource -->|Upload File| UploadCode[Upload Function File]
    CodeSource -->|Git Repo| GitRepo[Connect Repository]
    CodeSource -->|Template| UseTemplate[Select Template]
    
    InlineEditor --> WriteCode[Write Function Code]
    UploadCode --> CodeReady
    GitRepo --> SelectBranch[Select Branch/Path]
    UseTemplate --> CodeReady
    
    WriteCode --> CodeReady[Code Ready]
    SelectBranch --> CodeReady
    
    CodeReady --> RouteConfig[Configure Routes]
    RouteConfig --> RoutePattern{Route Pattern}
    RoutePattern -->|Path| PathRoute["e.g., /api/*"]
    RoutePattern -->|Exact| ExactRoute["e.g., /users/:id"]
    RoutePattern -->|Wildcard| WildcardRoute["e.g., /*"]
    
    PathRoute --> EnvVars
    ExactRoute --> EnvVars
    WildcardRoute --> EnvVars
    
    EnvVars[Environment Variables]
    EnvVars --> AddSecrets{Add Secrets?}
    AddSecrets -->|Yes| SecretManager[Select from Secrets]
    AddSecrets -->|No| SecurityOptions
    SecretManager --> SecurityOptions
    
    SecurityOptions{Security Features}
    SecurityOptions -->|SGX| EnableSGX[Enable SGX Enclave]
    SecurityOptions -->|Standard| StandardSec[Standard Security]
    SecurityOptions -->|ICP Native| ICPSecurity[ICP Canister Security]
    
    EnableSGX --> DomainConfig
    StandardSec --> DomainConfig
    ICPSecurity --> DomainConfig
    
    DomainConfig{Domain Configuration}
    DomainConfig -->|Default| DefaultDomain[Use *.af-functions.app]
    DomainConfig -->|Custom| CustomDomain[Add Custom Domain]
    DomainConfig -->|ICP Native| ICPNativeDomain[Use *.ic0.app]
    
    DefaultDomain --> ValidateCode
    CustomDomain --> DNSVerify[Verify DNS]
    ICPNativeDomain --> ValidateCode
    DNSVerify --> ValidateCode
    
    ValidateCode[Validate Function Code]
    ValidateCode --> CodeCheck{Valid?}
    CodeCheck -->|No| ShowErrors[Show Validation Errors]
    ShowErrors --> FixCode{Fix Code?}
    FixCode -->|Yes| CodeSource
    FixCode -->|No| CancelDeploy([Deployment Cancelled])
    
    CodeCheck -->|Yes| DeployChoice{Deploy Where?}
    
    DeployChoice -->|Traditional| PackageFunction[Package Function]
    PackageFunction --> HashCode[Generate BLAKE3 Hash]
    HashCode --> UploadIPFS[Upload to IPFS]
    UploadIPFS --> GetCID[Get CID]
    GetCID --> RegisterFunc[Register in Database]
    RegisterFunc --> ConfigRouter[Configure RuntimeRouter]
    ConfigRouter --> DeployRuntime[Deploy Runtime]
    
    DeployChoice -->|ICP| BuildCanister[Build Canister]
    BuildCanister --> AllocateCycles[Allocate Cycles]
    AllocateCycles --> CreateICPCanister[Create Canister]
    CreateICPCanister --> InstallCode[Install Wasm Code]
    InstallCode --> DeployRuntime
    
    DeployRuntime --> HealthCheck{Health Check}
    HealthCheck -->|Failed| DeployError[Deployment Error]
    HealthCheck -->|Success| FunctionLive[Function Live]
    
    DeployError --> Retry{Retry?}
    Retry -->|Yes| DeployRuntime
    Retry -->|No| CancelDeploy
    
    FunctionLive --> GetURL[Get Function URL]
    GetURL --> TestFunction{Test Function?}
    
    TestFunction -->|Yes| SendRequest[Send Test Request]
    SendRequest --> ViewResponse[View Response]
    ViewResponse --> TestResult{Works?}
    TestResult -->|No| Debug[Debug Function]
    TestResult -->|Yes| MonitorFunc
    Debug --> ViewLogs[View Function Logs]
    ViewLogs --> FixCode
    
    TestFunction -->|No| MonitorFunc
    
    MonitorFunc[Monitor Function]
    MonitorFunc --> Metrics[View Metrics]
    Metrics --> MetricsData{Metrics}
    MetricsData --> Invocations[Invocations Count]
    MetricsData --> Latency[Response Time]
    MetricsData --> Errors[Error Rate]
    MetricsData --> Traffic[Traffic Patterns]
    
    Invocations --> Management
    Latency --> Management
    Errors --> Management
    Traffic --> Management
    
    Management{Function Management}
    Management -->|Update| UpdateFunc[Update Function Code]
    Management -->|Scale| ScaleFunc[Adjust Resources]
    Management -->|Delete| DeleteFunc[Delete Function]
    Management -->|Done| End([Function Deployed])
    
    UpdateFunc --> CodeSource
    ScaleFunc --> MonitorFunc
    DeleteFunc --> End
    
    style Start fill:#e1f5e1
    style End fill:#e1f5e1
    style CancelDeploy fill:#ffe1e1
    style FunctionLive fill:#e1e5f5
    style DeployError fill:#ffe1e1
    style ICPCanisterRuntime fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPLanguage fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style MotokoLang fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style RustLang fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style AzleLang fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPSecurity fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPNativeDomain fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style BuildCanister fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style AllocateCycles fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style CreateICPCanister fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style InstallCode fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

**Key Steps**:
1. Choose deployment interface (CLI, Web UI, or SDK)
2. **NEW: Select deployment target - Traditional (Akash) or ICP Canister**
3. **For ICP: Choose canister language (Motoko, Rust, or Azle)**
4. Provide function code (inline, upload, Git, or template)
5. Configure routes and patterns
6. Set environment variables and secrets
7. **For ICP: Use ICP native canister security**
8. Configure domain (default, custom, **or ICP native .ic0.app**)
9. Validate and package function
10. **For ICP: Build canister, allocate cycles, create canister, install Wasm**
11. **For Traditional: Upload to IPFS, get CID, deploy runtime**
12. Health check and get function URL
13. Test function and view metrics
14. Monitor and manage function

### 4. Container Registry User Flow

(Same as before - no ICP changes for container registry)

### 5. AI Agent Deployment Flow (with ICP Canister Option)

```mermaid
flowchart TD
    Start([Deploy AI Agent]) --> AgentType{Choose Agent Type}
    
    AgentType -->|Containerized| ContainerAgent[Container-based Agent]
    AgentType -->|Function| FunctionAgent[Function-based Agent]
    AgentType -->|ICP Canister| ICPCanisterAgent[ICP Canister Agent]
    AgentType -->|Template| TemplateAgent[Pre-built Template]
    
    ContainerAgent --> ContainerSource{Container Source}
    ContainerSource -->|Registry| PullFromRegistry[Pull from AF Registry]
    ContainerSource -->|Docker Hub| PullDockerHub[Pull from Docker Hub]
    ContainerSource -->|Build New| BuildNew[Build New Container]
    
    PullFromRegistry --> SelectImage[Select Image/Tag]
    PullDockerHub --> SpecifyImage[Specify Image Name]
    BuildNew --> CreateDockerfile[Create Dockerfile]
    
    SelectImage --> ConfigureAgent
    SpecifyImage --> ConfigureAgent
    CreateDockerfile --> BuildContainer[Build Container]
    BuildContainer --> PushRegistry[Push to Registry]
    PushRegistry --> ConfigureAgent
    
    FunctionAgent --> FunctionWizard[Function Agent Wizard]
    FunctionWizard --> SelectAIModel{Select AI Model}
    
    ICPCanisterAgent --> ICPCanisterWizard[ICP Canister Wizard]
    ICPCanisterWizard --> ICPAIModel{Select AI Model}
    ICPAIModel -->|On-Chain Model| OnChainModel[Deploy On-Chain AI Model]
    ICPAIModel -->|Off-Chain API| OffChainAPI[Connect External API]
    
    OnChainModel --> ICPLanguageChoice{Canister Language}
    ICPLanguageChoice -->|Motoko| MotokoAgent[Motoko Agent]
    ICPLanguageChoice -->|Rust| RustAgent[Rust Agent]
    MotokoAgent --> AgentBehavior
    RustAgent --> AgentBehavior
    
    OffChainAPI --> SelectAIModel
    
    SelectAIModel -->|OpenAI| OpenAIConfig[Configure OpenAI]
    SelectAIModel -->|Anthropic| AnthropicConfig[Configure Anthropic]
    SelectAIModel -->|Custom| CustomModel[Custom Model Endpoint]
    SelectAIModel -->|Local| LocalModel[Self-hosted Model]
    
    OpenAIConfig --> AgentBehavior
    AnthropicConfig --> AgentBehavior
    CustomModel --> AgentBehavior
    LocalModel --> AgentBehavior
    
    AgentBehavior[Define Agent Behavior]
    AgentBehavior --> SystemPrompt[Set System Prompt]
    SystemPrompt --> Tools{Add Tools?}
    Tools -->|Yes| SelectTools[Select Tools/APIs]
    Tools -->|No| AgentCode
    SelectTools --> AgentCode
    
    AgentCode[Write Agent Logic]
    AgentCode --> ConfigureAgent
    
    TemplateAgent --> BrowseTemplates[Browse Templates]
    BrowseTemplates --> SelectTemplate{Select Template}
    SelectTemplate -->|Chatbot| ChatbotTemplate[Chatbot Template]
    SelectTemplate -->|Assistant| AssistantTemplate[Assistant Template]
    SelectTemplate -->|Automation| AutomationTemplate[Automation Template]
    SelectTemplate -->|ICP Canister| ICPTemplate[ICP Canister Template]
    SelectTemplate -->|Custom| CustomTemplate[Custom Template]
    
    ChatbotTemplate --> CustomizeTemplate
    AssistantTemplate --> CustomizeTemplate
    AutomationTemplate --> CustomizeTemplate
    ICPTemplate --> CustomizeICPTemplate[Customize ICP Template]
    CustomTemplate --> CustomizeTemplate
    
    CustomizeTemplate[Customize Template]
    CustomizeTemplate --> ConfigureAgent
    CustomizeICPTemplate --> ConfigureAgent
    
    ConfigureAgent[Configure Agent]
    ConfigureAgent --> AgentName[Set Agent Name]
    AgentName --> DeployTarget{Deployment Target}
    
    DeployTarget -->|Akash| AkashResources[Akash Resources]
    DeployTarget -->|ICP| ICPResources[ICP Resources]
    
    AkashResources --> CPU[CPU: 0.5-4 cores]
    AkashResources --> Memory[RAM: 512MB-8GB]
    AkashResources --> Storage[Storage: 1GB-100GB]
    
    ICPResources --> ICPCycles[Cycles Allocation]
    ICPResources --> ICPMemory[Stable Memory Size]
    ICPResources --> ICPSubnetChoice[Subnet Selection]
    
    CPU --> EnvConfig
    Memory --> EnvConfig
    Storage --> EnvConfig
    ICPCycles --> EnvConfig
    ICPMemory --> EnvConfig
    ICPSubnetChoice --> EnvConfig
    
    EnvConfig[Environment Variables]
    EnvConfig --> APIKeys[Add API Keys]
    APIKeys --> Secrets[Add Secrets]
    Secrets --> ModelConfig[Model Configuration]
    
    ModelConfig --> ModelSettings{Model Settings}
    ModelSettings --> Temperature[Temperature]
    ModelSettings --> MaxTokens[Max Tokens]
    ModelSettings --> TopP[Top P]
    
    Temperature --> NetworkConfig
    MaxTokens --> NetworkConfig
    TopP --> NetworkConfig
    
    NetworkConfig{Network Configuration}
    NetworkConfig -->|Public| PublicEndpoint[Public Endpoint]
    NetworkConfig -->|Private| PrivateNetwork[Private Network]
    NetworkConfig -->|VPN| VPNAccess[VPN Access]
    NetworkConfig -->|ICP Native| ICPNative[ICP Native Endpoint]
    
    PublicEndpoint --> DomainSetup
    PrivateNetwork --> DomainSetup
    VPNAccess --> DomainSetup
    ICPNative --> DomainSetup
    
    DomainSetup{Domain Setup}
    DomainSetup -->|Custom| CustomDomain[Add Custom Domain]
    DomainSetup -->|Subdomain| AFSubdomain[Use AF Subdomain]
    DomainSetup -->|Default| DefaultURL[Use Default URL]
    DomainSetup -->|ICP Native| ICPDomain[Use *.ic0.app]
    
    CustomDomain --> PersistenceConfig
    AFSubdomain --> PersistenceConfig
    DefaultURL --> PersistenceConfig
    ICPDomain --> PersistenceConfig
    
    PersistenceConfig{Data Persistence}
    PersistenceConfig -->|Stateless| NoStorage[No Persistent Storage]
    PersistenceConfig -->|Stateful| AddVolume[Add Persistent Volume]
    PersistenceConfig -->|ICP Stable| ICPStable[ICP Stable Memory]
    
    NoStorage --> MonitoringSetup
    AddVolume --> VolumeSize[Specify Volume Size]
    VolumeSize --> MonitoringSetup
    ICPStable --> MonitoringSetup
    
    MonitoringSetup{Monitoring}
    MonitoringSetup --> EnableLogs[Enable Logging]
    MonitoringSetup --> EnableMetrics[Enable Metrics]
    MonitoringSetup --> EnableAlerts[Configure Alerts]
    
    EnableLogs --> Deploy
    EnableMetrics --> Deploy
    EnableAlerts --> Deploy
    
    Deploy[Deploy Agent]
    Deploy --> TargetCheck{Target Platform?}
    
    TargetCheck -->|Akash| AkashDeploy[Akash Deployment]
    TargetCheck -->|ICP| ICPDeploy[ICP Deployment]
    
    AkashDeploy --> CreateSDL[Generate SDL Definition]
    CreateSDL --> SelectProvider[Select Akash Provider]
    SelectProvider --> BiddingProcess[Bidding Process]
    BiddingProcess --> ReviewBids[Review Provider Bids]
    ReviewBids --> SelectBid{Select Bid}
    SelectBid --> AcceptBid[Accept Bid]
    AcceptBid --> CreateLease[Create Lease]
    CreateLease --> DeployToProvider[Deploy to Provider]
    DeployToProvider --> WaitDeploy[Wait for Deployment]
    
    ICPDeploy --> BuildCanisterAgent[Build Canister]
    BuildCanisterAgent --> AllocateCyclesAgent[Allocate Cycles]
    AllocateCyclesAgent --> CreateCanisterAgent[Create Canister]
    CreateCanisterAgent --> InstallCodeAgent[Install Wasm Code]
    InstallCodeAgent --> WaitDeploy
    
    WaitDeploy --> HealthCheck{Health Check}
    
    HealthCheck -->|Failed| DeployError[Deployment Error]
    HealthCheck -->|Success| AgentLive[Agent Live]
    
    DeployError --> CheckLogs[Check Deployment Logs]
    CheckLogs --> FixIssue{Fix Issue?}
    FixIssue -->|Yes| ConfigureAgent
    FixIssue -->|Retry| Deploy
    FixIssue -->|No| CancelDeploy([Deployment Cancelled])
    
    AgentLive --> GetEndpoint[Get Agent Endpoint]
    GetEndpoint --> TestAgent{Test Agent?}
    
    TestAgent -->|Yes| SendTestQuery[Send Test Query]
    SendTestQuery --> ViewResponse[View Agent Response]
    ViewResponse --> TestResult{Works as Expected?}
    
    TestResult -->|No| Debug[Debug Agent]
    TestResult -->|Yes| MonitorAgent
    
    Debug --> ViewAgentLogs[View Agent Logs]
    ViewAgentLogs --> CheckMetrics[Check Metrics]
    CheckMetrics --> FixConfig{Fix Configuration?}
    FixConfig -->|Yes| ConfigureAgent
    FixConfig -->|No| CancelDeploy
    
    TestAgent -->|No| MonitorAgent
    
    MonitorAgent[Monitor Agent]
    MonitorAgent --> Dashboard[Open Agent Dashboard]
    Dashboard --> Metrics{View Metrics}
    
    Metrics --> RequestCount[Request Count]
    Metrics --> ResponseTime[Response Time]
    Metrics --> ErrorRate[Error Rate]
    Metrics --> TokenUsage[Token Usage]
    Metrics --> CostTracking[Cost Tracking]
    Metrics --> ICPCyclesUsage[ICP Cycles Usage]
    
    RequestCount --> Management
    ResponseTime --> Management
    ErrorRate --> Management
    TokenUsage --> Management
    CostTracking --> Management
    ICPCyclesUsage --> Management
    
    Management{Agent Management}
    Management -->|Update| UpdateAgent[Update Configuration]
    Management -->|Scale| ScaleAgent[Scale Resources]
    Management -->|Restart| RestartAgent[Restart Agent]
    Management -->|Stop| StopAgent[Stop Agent]
    Management -->|Delete| DeleteAgent[Delete Agent]
    Management -->|Done| End([Agent Deployed])
    
    UpdateAgent --> ConfigureAgent
    ScaleAgent --> DeployTarget
    RestartAgent --> MonitorAgent
    StopAgent --> End
    DeleteAgent --> CloseLease[Close Lease/Delete Canister]
    CloseLease --> CleanupResources[Cleanup Resources]
    CleanupResources --> End
    
    style Start fill:#e1f5e1
    style End fill:#e1f5e1
    style CancelDeploy fill:#ffe1e1
    style AgentLive fill:#e1e5f5
    style DeployError fill:#ffe1e1
    style ICPCanisterAgent fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPCanisterWizard fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPAIModel fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style OnChainModel fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPLanguageChoice fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style MotokoAgent fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style RustAgent fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPTemplate fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style CustomizeICPTemplate fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPResources fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPCycles fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPMemory fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPSubnetChoice fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPNative fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPDomain fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPStable fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPDeploy fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style BuildCanisterAgent fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style AllocateCyclesAgent fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style CreateCanisterAgent fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style InstallCodeAgent fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPCyclesUsage fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

**Key Steps**:
1. **NEW: Choose agent type including ICP Canister option**
2. **For ICP Canister: Choose on-chain or off-chain AI model**
3. **For on-chain: Select Motoko or Rust for canister development**
4. Configure agent source and behavior
5. Set up AI model and parameters
6. **NEW: Choose deployment target - Akash or ICP**
7. **For ICP: Allocate cycles, configure stable memory, select subnet**
8. Configure resources based on platform
9. Set environment variables and secrets
10. **Configure networking including ICP native endpoint option**
11. **Set up domain including ICP native .ic0.app option**
12. **Configure data persistence including ICP stable memory**
13. Enable monitoring and logging
14. **Deploy to chosen platform (Akash bidding or ICP canister creation)**
15. Health check and endpoint access
16. Test agent functionality
17. Monitor metrics including **ICP cycles usage**
18. Manage agent

### 6. Billing and Subscription Management Flow

(Same as before - billing flow remains unchanged)

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
| **ICP Integration** | **ICP Agent SDK** | **Canister communication** |
| **ICP Development** | **dfx CLI** | **Canister deployment** |

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
| **ICP Integration** | **@dfinity/agent** | **ICP authentication & calls** |
| **ICP Auth** | **@dfinity/auth-client** | **Internet Identity** |

### Storage

| Technology | Purpose | Provider/Options |
|-----------|---------|------------------|
| IPFS | Content-addressed storage | Pinata, Web3.Storage, Lighthouse |
| Arweave | Permanent storage | Turbo SDK |
| Filecoin | Decentralized storage | - |
| **Internet Computer** | **Canister storage & hosting** | **ICP Stable Memory, Asset Canisters** |
| PostgreSQL | Relational data | Akash-hosted |
| SQLite | Local auth data | File-based |

### Infrastructure

| Technology | Purpose | Notes |
|-----------|---------|-------|
| Akash Network | Decentralized compute | Containerized services |
| **Internet Computer** | **Decentralized canisters** | **Backend services & storage** |
| Pingap/Pingora | SSL proxy | High-performance reverse proxy |
| OctoDNS | DNS management | Multi-provider sync |
| Infisical | Secrets management | Self-hosted |
| Let's Encrypt | SSL certificates | DNS-01 via Cloudflare |

### Tools & CLI

| Tool | Technology | Purpose |
|------|-----------|---------|
| CLI | Commander.js + **dfx** | Command-line interface |
| SDK | TypeScript + **ICP Agent SDK** | Programmatic API access |
| Linter (Backend) | Biome | Code linting/formatting |
| Linter (Frontend) | ESLint | Code linting/formatting |
| Package Manager | pnpm | Monorepo package management |
| **ICP Development** | **dfx CLI** | **Canister management** |

---

## Deployment Architecture

### Multi-Platform Deployment

Services can be deployed on multiple decentralized platforms for maximum resilience and flexibility:

### Akash Network

All containerized services deployed on **Akash Network**, a decentralized compute marketplace where providers bid on workloads.

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

### Internet Computer (NEW)

Backend services and storage canisters deployed on **Internet Computer**, a blockchain-based decentralized compute platform.

```mermaid
graph LR
    subgraph "Internet Computer"
        DFX[dfx CLI / Candid] -->|Deploy| ICPNetwork[IC Network]
        ICPNetwork -->|Route| SubnetNNS[NNS Subnet]
        ICPNetwork -->|Route| SubnetApp[Application Subnet]
        ICPNetwork -->|Route| SubnetSystem[System Subnet]
        
        SubnetApp -->|Hosts| Canister[Running Canister]
        
        Canister --> Cycles[Cycles Management]
        Canister --> StableMemory[(Stable Memory)]
    end
    
    User((User)) -->|Create| DFX
    User -->|Monitor| Cycles
    
    style DFX fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPNetwork fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style SubnetNNS fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style SubnetApp fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style SubnetSystem fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style Canister fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style Cycles fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style StableMemory fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

**Benefits**:
- On-chain computation
- Deterministic execution
- Native authentication (Internet Identity)
- Automatic scaling
- Built-in storage (stable memory)
- Direct HTTP requests to canisters

**Deployment Process**:
1. Write canister code (Motoko, Rust, or Azle)
2. Build canister with dfx
3. Allocate cycles for computation
4. Create canister on ICP
5. Install Wasm code to canister
6. Monitor via canister dashboard

### High Availability (with ICP Integration)

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
            ICPStore[ICP Storage<br/>Stable Memory]
        end
        
        subgraph "Compute Layer"
            Akash1[Akash Provider 1]
            Akash2[Akash Provider 2<br/>Failover]
            ICPSubnet1[ICP Subnet 1]
            ICPSubnet2[ICP Subnet 2<br/>Failover]
        end
    end
    
    CF -.->|Failover| GCP
    GCP -.->|Failover| DeSEC
    
    P1 -.->|Redundancy| IPFS
    P2 -.->|Redundancy| IPFS
    P3 -.->|Redundancy| IPFS
    IPFS -.->|Backup| AR
    IPFS -.->|Backup| FC
    IPFS -.->|Alternative| ICPStore
    
    Akash1 -.->|Failover| Akash2
    ICPSubnet1 -.->|Failover| ICPSubnet2
    
    style ICPStore fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPSubnet1 fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style ICPSubnet2 fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

**Multi-Platform Strategy**:
- DNS: Cloudflare (primary), Google DNS, deSEC
- IPFS: Multiple pinning services (Pinata, Web3.Storage, Lighthouse)
- Storage: IPFS + Arweave + Filecoin + **ICP Stable Memory**
- Compute: Akash Network + **Internet Computer**

**Monitoring**:
- Health checks on all services
- Uptime monitoring
- Alert system
- **ICP cycles monitoring**

**Failover**:
- DNS failover to backup providers
- Service redeployment on different Akash provider
- **Canister redeployment on different ICP subnet**
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
| **Internet Computer** | **Canister hosting** | **ICP Agent SDK, dfx CLI** |
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
        
        API <-->|Agent SDK| ICPGateway[ICP Gateway]
        ICPGateway <-->|Canister Calls| Canisters[(ICP Canisters)]
        
        API -.->|Secrets| Inf[Infisical]
        Auth -.->|Secrets| Inf
        Reg -.->|Secrets| Inf
    end
    
    subgraph "Client-to-Service"
        WebUI[Web Dashboard] -->|GraphQL| API
        CLI[CLI Tool] -->|GraphQL| API
        SDK[SDK] -->|GraphQL| API
        Docker[Docker Client] -->|OCI HTTP| Reg
        
        WebUI -->|Agent SDK| ICPGateway
        CLI -->|dfx/Agent SDK| ICPGateway
        SDK -->|Agent SDK| ICPGateway
    end
    
    style ICPGateway fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style Canisters fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

**Service-to-Service**:
- GraphQL API ↔ Auth Service (JWT validation)
- GraphQL API ↔ PostgreSQL (Prisma ORM)
- OpenRegistry ↔ PostgreSQL (SQL)
- OpenRegistry ↔ IPFS (HTTP API)
- **GraphQL API ↔ ICP Gateway (Agent SDK)**
- **ICP Gateway ↔ ICP Canisters (Canister calls)**
- All Services ↔ Infisical (Secrets retrieval)

**Client-to-Service**:
- Web Dashboard → GraphQL API (GraphQL)
- CLI → GraphQL API (GraphQL)
- SDK → GraphQL API (GraphQL)
- Docker Client → Registry (OCI HTTP API)
- **Web Dashboard → ICP Gateway (Agent SDK)**
- **CLI → ICP Gateway (dfx/Agent SDK)**
- **SDK → ICP Gateway (Agent SDK)**

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
        
        ICPLB[ICP Load Balancer<br/>Boundary Nodes]
        ICPLB --> Canister1[Canister<br/>Instance 1]
        ICPLB --> Canister2[Canister<br/>Instance 2]
        ICPLB --> Canister3[Canister<br/>Instance 3]
    end
    
    style ICPLB fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style Canister1 fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style Canister2 fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
    style Canister3 fill:#c5f0ff,stroke:#0066cc,stroke-width:3px
```

**API Server**:
- Multiple instances behind load balancer
- Stateless design (JWT tokens)
- Shared PostgreSQL database

**Registry**:
- Multiple OpenRegistry instances
- Shared IPFS node
- Shared PostgreSQL database

**ICP Canisters (NEW)**:
- Automatic horizontal scaling via boundary nodes
- Multiple canister instances
- Distributed across subnets

### Storage Scaling

**IPFS**:
- Increase storage allocation (100GB → 500GB+)
- Add IPFS cluster for redundancy
- Multiple pinning services

**ICP Storage (NEW)**:
- Stable memory scaling (up to 400GB per canister)
- Multiple asset canisters for large applications
- Automatic replication across subnet nodes

**Database**:
- PostgreSQL read replicas
- Connection pooling
- Query optimization

### Geographic Distribution

**Multi-Region Deployment**:
- Deploy stacks in different Akash regions
- **Deploy canisters across different ICP subnets**
- GeoDNS routing to nearest instance
- Regional IPFS gateways
- **ICP boundary nodes provide automatic geographic distribution**

---

## Future Enhancements

### Planned Features

1. **Enhanced ICP Integration**:
   - **Full Internet Identity integration across all services**
   - **ICP Ledger integration for payments**
   - **SNS (Service Nervous System) DAO governance**
   - **ICRC-1 token support**
   - **Chain Fusion for cross-chain integration**
   - **Threshold signatures for secure multi-chain operations**

2. **MCP Server Integration**:
   - Model Context Protocol servers for all services
   - Enable AI assistants (Claude, ChatGPT, etc.) to interact with platform
   - **Deployments MCP**: Manage Akash/ICP deployments, view logs, check status
   - **Functions MCP**: Create, deploy, and manage serverless functions on Akash/ICP
   - **Registry MCP**: Push/pull container images, manage repositories
   - **Sites MCP**: Deploy static sites to IPFS/Arweave/ICP, update content
   - **Agents MCP**: Deploy and monitor AI agents on Akash/ICP
   - **Billing MCP**: View usage, manage subscriptions, access invoices
   - **ICP MCP**: Manage canisters, cycles, Internet Identity
   - Standardized tool definitions for each service domain
   - Authentication via Personal Access Tokens
   - Real-time status updates and notifications

3. **Registry**:
   - IPFS cluster for redundancy
   - Image signing (Cosign)
   - Vulnerability scanning (Trivy)
   - Cross-region replication
   - Usage analytics
   - Webhook notifications

4. **Platform**:
   - Real-time collaboration
   - AI agent marketplace
   - Template marketplace
   - Custom domain SSL automation
   - Enhanced function analytics and monitoring
   - **ICP canister marketplace**
   - **On-chain governance for platform decisions**

5. **Infrastructure**:
   - Multi-cloud support (Akash + ICP + future platforms)
   - Backup/restore automation
   - Disaster recovery
   - Performance optimization
   - **Cross-chain bridges for asset transfers**

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
- **[Internet Computer](https://internetcomputer.org)**
- **[ICP Developer Docs](https://internetcomputer.org/docs)**
- **[Motoko Language](https://internetcomputer.org/docs/current/motoko/intro)**
- **[Internet Identity](https://identity.ic0.app)**
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
- **ICP**: Internet Computer Protocol
- **II**: Internet Identity (ICP's authentication system)
- **dfx**: DFINITY Canister SDK command-line tool
- **Canister**: Smart contract on Internet Computer
- **Cycles**: Computation units on Internet Computer
- **Stable Memory**: Persistent storage in ICP canisters
- **Motoko**: Programming language for ICP canisters
- **Azle**: TypeScript/JavaScript framework for ICP
- **IPFS**: InterPlanetary File System
- **IPNS**: InterPlanetary Name System
- **JWT**: JSON Web Token
- **MCP**: Model Context Protocol (AI assistant integration standard)
- **OCI**: Open Container Initiative
- **SDL**: Service Definition Language (Akash)
- **SGX**: Software Guard Extensions (Intel trusted execution)
- **SIWE**: Sign-In with Ethereum
- **SNS**: Service Nervous System (ICP DAO framework)
- **SSL**: Secure Sockets Layer
- **TLS**: Transport Layer Security

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
- 📚 Added ICP-specific documentation and glossary terms
- 🔄 Updated technology stack with ICP Agent SDK and dfx CLI
- 🌐 Enhanced multi-platform deployment architecture
