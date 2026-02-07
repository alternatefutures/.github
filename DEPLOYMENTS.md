# Akash Network Deployments

This file tracks active deployments on Akash Network. All information here is public blockchain data.

## Active Deployments

### postgres (database)
| Field | Value |
|-------|-------|
| **DSEQ** | 25423159 |
| **Provider** | `akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc` |
| **Services** | PostgreSQL 16 Alpine |
| **Status** | Running |

### data services (IPFS + Jaeger)
| Field | Value |
|-------|-------|
| **DSEQ** | 25423172 |
| **Provider** | `akash1k94uya5rhrtj9rfw850az9aq2d6vdpjmtnlgd0` |
| **Services** | IPFS + Jaeger (OTel collector disabled) |
| **Custom Domains** | ipfs.alternatefutures.ai, jaeger.alternatefutures.ai |
| **Status** | Running |

### api
| Field | Value |
|-------|-------|
| **DSEQ** | 25423196 |
| **Provider** | `akash1q4nsecnmxh9rmdyvlfx3nt654nkgp628yuy8sg` |
| **Image** | `ghcr.io/alternatefutures/service-cloud-api:latest` |
| **Custom Domain** | api.alternatefutures.ai (via SSL proxy) |
| **Status** | Running |
| **CI/CD** | `deploy-akash.yml` (full) / `update-manifest.yml` (in-place) |

### service-auth (Authentication)
| Field | Value |
|-------|-------|
| **DSEQ** | 25423184 |
| **Provider** | `akash1xmjzu9dczlg9fa4v3pfvwzn7ty89r003laj4ac` |
| **Image** | `ghcr.io/alternatefutures/service-auth:main-*` |
| **Custom Domain** | auth.alternatefutures.ai (via SSL proxy) |
| **Status** | Running |
| **CI/CD** | `deploy-akash.yml` (full) / `update-manifest.yml` (in-place) |

### infrastructure-proxy (SSL Proxy)
| Field | Value |
|-------|-------|
| **DSEQ** | 25423216 |
| **Provider** | `akash1zlsep362zz46qlwzttm06t8lv9qtg8gtaya97u` |
| **Image** | `ghcr.io/alternatefutures/infrastructure-proxy-pingap:main` |
| **Dedicated IP** | 198.12.74.90 |
| **Domains Routed** | auth, api, app, docs.alternatefutures.ai |
| **Status** | Running |

## Database (PostgreSQL)

PostgreSQL is deployed separately and exposed globally via TCP:
| Field | Value |
|-------|-------|
| **Host** | provider.europlots.com |
| **Port** | 32648 |
| **Database** | alternatefutures |
| **User** | alternatefutures |
| **Connection** | `postgresql://alternatefutures:<password>@provider.europlots.com:32648/alternatefutures` |

## Secrets Management

No Infisical deployment. Secrets are injected directly as SDL environment variables at deploy time.
The auth service reads JWT_SECRET, RESEND_API_KEY, etc. from env vars (built-in fallback).

## Akash Account

- **Address**: `akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn`
- **Network**: Mainnet
- **Explorer**: [Cloudmos](https://deploy.cloudmos.io/addresses/akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn)

## Infrastructure & SSL

All custom domains route through the SSL proxy (Pingap on Cloudflare's Pingora framework) at `198.12.74.90`.

| Domain | Routing | SSL |
|--------|---------|-----|
| auth.alternatefutures.ai | SSL proxy (198.12.74.90) | Cloudflare Origin Cert |
| api.alternatefutures.ai | SSL proxy (198.12.74.90) | Cloudflare Origin Cert |
| app.alternatefutures.ai | Vercel | Vercel managed |
| ipfs.alternatefutures.ai | SSL proxy (198.12.74.90) | Cloudflare Origin Cert |
| jaeger.alternatefutures.ai | SSL proxy (198.12.74.90) | Cloudflare Origin Cert |

## Blocked Providers

| Provider | Reason |
|----------|--------|
| `akash1smapjx8m8363nmdvc2yr9atlqy8vcql73m9l0v` | Broken hostname |

## View Deployments

Deployments can be viewed on Cloudmos:
```
https://deploy.cloudmos.io/deployment/akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn/{dseq}
```

---

*Last updated: 2026-02-07*
