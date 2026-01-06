# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AlternateFutures is a decentralized cloud platform (forked from Fleek) providing IPFS/Filecoin/Arweave hosting, serverless functions, and AI agent deployment. This is a multi-project workspace containing the full platform stack.

## Repository Structure

| Directory | Package Name | Purpose |
|-----------|--------------|--------|
| `service-cloud-api/` | `@alternatefutures/backend` | GraphQL API server (GraphQL Yoga + Prisma + PostgreSQL) |
| `service-auth/` | `@alternatefutures/auth-service` | Authentication service (Hono + SQLite) |
| `web-app.alternatefutures.ai/` | `@alternatefutures/app` | Main dashboard (SvelteKit 2 + Svelte 5) |
| `package-cloud-cli/` | `@alternatefutures/cli` | CLI tool (Commander.js) |
| `package-cloud-sdk/` | `@alternatefutures/sdk` | JavaScript SDK |
| `web-docs.alternatefutures.ai/` | - | Documentation site |
| `adapter-cloud-next/` | - | Next.js adapter for AF hosting |
| `template-cloud-*` | - | Starter templates (Astro, Next.js, React, Vue, Hugo) |
| `local-packages/` | `@alternatefutures-*` | Shared internal packages |
| `dependency-packages/` | `@alternatefutures/*` | Published npm packages |
| `akash-mcp/` | - | MCP server for Akash Network deployments |

## Common Commands by Project

### Backend API (`service-cloud-api/`)
```bash
cd service-cloud-api
pnpm dev              # Start dev server (tsx watch)
pnpm test             # Run tests (vitest)
pnpm db:generate      # Generate Prisma client
pnpm db:migrate       # Run migrations
pnpm db:studio        # Open Prisma Studio
pnpm lint             # ESLint
pnpm format           # Prettier
```

### Auth Service (`service-auth/`)
```bash
cd service-auth
pnpm dev              # Start dev server
pnpm test             # Run tests
pnpm db:setup         # Initialize SQLite database
```

### Web App (`web-app.alternatefutures.ai/`)
```bash
cd web-app.alternatefutures.ai
pnpm dev              # Start Vite dev server
pnpm build            # Production build
pnpm check            # Svelte type checking
pnpm test             # Run tests
```

### CLI (`package-cloud-cli/`)
```bash
cd package-cloud-cli
pnpm build            # Build CLI (transpile + bundle)
pnpm test             # Run tests
pnpm lint             # Biome linter
pnpm format           # Biome formatter
```
The CLI binary is `af` (e.g., `af deploy`, `af sites list`).

### SDK (`package-cloud-sdk/`)
```bash
cd package-cloud-sdk
pnpm build            # Build for browser and Node
pnpm test             # Run tests
```

## Architecture

### Backend Stack
- **API Gateway**: GraphQL Yoga with depth limiting, complexity analysis, rate limiting
- **ORM**: Prisma with PostgreSQL
- **Auth**: JWT tokens + Personal Access Tokens + OAuth + Web3 wallet (SIWE)
- **Storage**: Pinata/Web3.Storage/Lighthouse for IPFS, Turbo SDK for Arweave
- **Billing**: Stripe integration

### Frontend Stack
- **Framework**: SvelteKit 2.0 with Svelte 5 (runes-based reactivity)
- **UI**: SMUI (Svelte Material UI) + Monaco Editor
- **State**: Svelte stores in `src/lib/stores/`

### Authentication Flow
The auth service (`service-auth/`) supports:
1. Email magic links (passwordless)
2. SMS OTP
3. Web3 wallets (SIWE with MetaMask, WalletConnect, Phantom)
4. Social OAuth (Google, GitHub, Twitter, Discord, etc.)
5. Account linking (multiple methods per user)

## Key Files

### Database Schema
- `service-cloud-api/prisma/schema.prisma` - Main Prisma schema with all models

### GraphQL
- `service-cloud-api/src/schema/` - GraphQL type definitions and resolvers

### Environment
Each project has its own `.env` file. Key variables:
- `DATABASE_URL` - PostgreSQL connection
- `JWT_SECRET` - Token signing
- `PINATA_JWT` - IPFS pinning
- `STRIPE_SECRET_KEY` - Billing

## Testing

All projects use **Vitest**. Run a single test file:
```bash
pnpm test path/to/file.test.ts
# or watch mode
pnpm test:watch path/to/file.test.ts
```

## Code Style

- **Backend/CLI**: Biome for linting/formatting
- **Frontend**: Prettier + ESLint
- **TypeScript**: Strict mode enabled

## Shared Packages

Internal packages in `local-packages/@alternatefutures-*`:
- `env-guards` - Environment variable parsing
- `utils-permissions` - Permission system helpers
- `utils-sites` - Site/deployment utilities
- `utils-routes` - Route generation
- `errors` - Error handling
- `utils-gateways` - IPFS gateway utilities
- `utils-ipfs`, `utils-ipns` - IPFS/IPNS helpers

## Linear Integration

This project uses Linear for issue tracking. Reference issues in commits:
- Branch naming: `feat/alt-{number}-description` or `fix/alt-{number}-description`
- Commit messages should reference Linear issue IDs

## MCP Servers

Configured MCP servers (see `.claude/README.md`):
- GitHub - Repository operations
- Linear - Issue management
- Akash - Decentralized compute deployments

## Infrastructure

### Akash Network Deployments

| Service | DSEQ | Provider | IP/Ingress |
|---------|------|----------|-----------|
| **SSL Proxy** | 24758214 | leet.haus | 170.75.255.101 (dedicated IP) |
| **Secrets (Infisical)** | 24672527 | Europlots | ddchr1pel5e0p8i0c46drjpclg.ingress.europlots.com |

### Traffic Routing Architecture

```
                Internet
                   │
    ┌──────────────┴──────────────┐
    │                             │
    ▼                             ▼
┌───────────────┐           ┌─────────────────┐
│ SSL Proxy     │           │ Secrets Service │
│ 170.75.255.101│           │ (Direct/Isolated│
└───────┬───────┘           └────────┬────────┘
       │                           │
       ├── auth.alternatefutures.ai     secrets.alternatefutures.ai
       ├── api.alternatefutures.ai      (Cloudflare Transform Rule)
       ├── app.alternatefutures.ai
       ├── docs.alternatefutures.ai
       └── alternatefutures.ai
```

### Architecture Decision: Secrets Service Isolation

The secrets service (`secrets.alternatefutures.ai`) runs **outside** the proxy, connecting directly to its Akash deployment. This provides resilience - if the proxy has issues, Infisical remains accessible for debugging and credential retrieval.

### Infrastructure Repositories

| Repository | Purpose |
|------------|--------|
| `infrastructure-proxy/` | SSL termination proxy (Pingap) |
| `service-secrets/` | Infisical secrets management deployment |
| `infrastructure-dns/` | Multi-provider DNS (Cloudflare, Google, deSEC) |
