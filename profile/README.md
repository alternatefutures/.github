<div align="center">

# ☁️ Alternate Clouds

**Deploy to decentralized compute. Pay less. Own more.**

Launch containers, AI agents, and full-stack apps on decentralized infrastructure — from one dashboard or CLI.

[![Website](https://img.shields.io/badge/Website-alternatefutures.ai-0026FF?style=for-the-badge)](https://alternatefutures.ai)
[![App](https://img.shields.io/badge/App-app.alternatefutures.ai-0026FF?style=for-the-badge)](https://app.alternatefutures.ai)
[![Discord](https://img.shields.io/badge/Discord-Join%20Us-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/GwHw3aBK)

---

</div>

## Quick Start

```bash
npm install -g @alternatefutures/cli
af login
af projects create --name my-project
af services deploy
```

---

## Repositories

### Services

| Repo | Description |
|------|-------------|
| [**service-auth**](https://github.com/alternatefutures/service-auth) | Authentication, billing, AI inference proxy |
| [**service-cloud-api**](https://github.com/alternatefutures/service-cloud-api) | GraphQL API — compute orchestration, domains, templates |

### Packages

| Repo | Description |
|------|-------------|
| [**package-cloud-cli**](https://github.com/alternatefutures/package-cloud-cli) | CLI for deploying and managing services [![npm](https://img.shields.io/npm/v/@alternatefutures/cli?style=flat-square)](https://www.npmjs.com/package/@alternatefutures/cli) |
| [**package-cloud-sdk**](https://github.com/alternatefutures/package-cloud-sdk) | TypeScript SDK for programmatic access [![npm](https://img.shields.io/npm/v/@alternatefutures/sdk?style=flat-square)](https://www.npmjs.com/package/@alternatefutures/sdk) |

### Web

| Repo | Description |
|------|-------------|
| [**web-app.alternatefutures.ai**](https://github.com/alternatefutures/web-app.alternatefutures.ai) | Dashboard (Next.js, Vercel) |
| [**web-alternatefutures.ai**](https://github.com/alternatefutures/web-alternatefutures.ai) | Marketing website |
| [**web-docs.alternatefutures.ai**](https://github.com/alternatefutures/web-docs.alternatefutures.ai) | Documentation site |

---

## Why Alternate Clouds?

<table>
<tr>
<td width="50%">

### 💰 60-85% Cost Savings
Decentralized compute is a fraction of AWS/GCP. Same containers, lower bill.

</td>
<td width="50%">

### 🔒 Confidential Compute
Deploy with hardware-level isolation — your code and data stay private.

</td>
</tr>
<tr>
<td width="50%">

### 🤖 AI Inference Proxy
11 providers (OpenAI, Anthropic, Groq, etc.) behind one API with per-token billing.

</td>
<td width="50%">

### 🛠️ Developer-First
CLI, SDK, GraphQL API, and a dashboard. Pick your workflow.

</td>
</tr>
</table>

---

## Tech Stack

- **Frontend**: Next.js, TypeScript, shadcn/ui, next-intl
- **Backend**: Hono (auth), GraphQL Yoga (API), Prisma, PostgreSQL
- **Compute**: Decentralized infrastructure (standard + confidential)
- **Payments**: Stripe (subscriptions + credits wallet)
- **Auth**: Email OTP, SMS, Web3/SIWE, OAuth (Google, GitHub, Discord, X)
- **Infra**: Docker, K3s, OpenTofu, GitHub Actions

---

## Contributing

1. Browse [open issues](https://github.com/search?q=org%3Aalternatefutures+is%3Aissue+is%3Aopen&type=issues)
2. Fork the repo, create a feature branch
3. Follow conventional commits (`feat:`, `fix:`, `docs:`)
4. Submit a PR — all repos have CI checks and code review

Join our [Discord](https://discord.gg/GwHw3aBK) to chat with the team.

---

<div align="center">

**[Website](https://alternatefutures.ai)** · **[App](https://app.alternatefutures.ai)** · **[Discord](https://discord.gg/GwHw3aBK)**

MIT License

</div>
