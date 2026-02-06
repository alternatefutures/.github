# Akash Network Deployments

This file tracks active deployments on Akash Network. All information here is public blockchain data.

## Active Deployments

### service-cloud-api (Full Stack)
| Field | Value |
|-------|-------|
| **DSEQ** | 25411473 |
| **Provider** | `akash1kqzpqqhm39umt06wu8m4hx63v5hefhrfmjf9dj` (leet.haus) |
| **Services** | API + YugabyteDB + IPFS + Jaeger + OTel Collector |
| **Custom Domains** | api.alternatefutures.ai, yb.alternatefutures.ai, ipfs.alternatefutures.ai |
| **Resources** | 8 CPU, 20Gi RAM, 280Gi storage |
| **Status** | Running |
| **CI/CD** | `deploy-akash.yml` (full) / `update-manifest.yml` (in-place) |

### service-auth (Authentication)
| Field | Value |
|-------|-------|
| **DSEQ** | 25412621 |
| **Provider** | `akash1xmjzu9dczlg9fa4v3pfvwzn7ty89r003laj4ac` (tagus.host) |
| **Image** | `ghcr.io/alternatefutures/service-auth:main-*` |
| **Custom Domain** | auth.alternatefutures.ai (via SSL proxy) |
| **Resources** | 1 CPU, 1Gi RAM, 1Gi storage |
| **Status** | Running |
| **CI/CD** | `deploy-akash.yml` (full) / `update-manifest.yml` (in-place) |

### infrastructure-proxy (SSL Proxy)
| Field | Value |
|-------|-------|
| **DSEQ** | 25312670 |
| **Provider** | DigitalFrontier (`akash1aaul837r7en7hpk9wv2svg8u78fdq0t2j2e82z`) |
| **Image** | `ghcr.io/alternatefutures/infrastructure-proxy-pingap:main` |
| **Dedicated IP** | 77.76.13.213 |
| **Domains Routed** | auth, api, app, docs.alternatefutures.ai |
| **Status** | Running |

### Infisical Secrets Manager
| Field | Value |
|-------|-------|
| **DSEQ** | 25354545 |
| **Provider** | Europlots (`akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc`) |
| **Ingress** | `uvhirubqe1aa1att76elejdi3c.ingress.europlots.com` |
| **Custom Domain** | secrets.alternatefutures.ai (direct, not through proxy) |
| **Status** | Running |

## Akash Account

- **Address**: `akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn`
- **Network**: Mainnet
- **Explorer**: [Cloudmos](https://deploy.cloudmos.io/addresses/akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn)

## Infrastructure & SSL

All custom domains route through the SSL proxy (Pingap on Cloudflare's Pingora framework) at `77.76.13.213`, **except** `secrets.alternatefutures.ai` which connects directly to its Akash deployment for resilience.

| Domain | Routing | SSL |
|--------|---------|-----|
| auth.alternatefutures.ai | SSL proxy (77.76.13.213) | Cloudflare Origin Cert |
| api.alternatefutures.ai | SSL proxy (77.76.13.213) | Cloudflare Origin Cert |
| app.alternatefutures.ai | Vercel | Vercel managed |
| secrets.alternatefutures.ai | Direct to Akash | Cloudflare proxy |
| yb.alternatefutures.ai | SSL proxy (77.76.13.213) | Cloudflare Origin Cert |
| ipfs.alternatefutures.ai | SSL proxy (77.76.13.213) | Cloudflare Origin Cert |

## Quick Reference: DSEQ to Service

| DSEQ | Service | Primary URL |
|------|---------|-------------|
| 25411473 | service-cloud-api (full stack) | api.alternatefutures.ai |
| 25412621 | service-auth | auth.alternatefutures.ai |
| 25312670 | infrastructure-proxy (SSL) | 77.76.13.213 |
| 25354545 | Infisical Secrets | secrets.alternatefutures.ai |

> **Note:** Akash Console shows deployments as "Unknown" since SDL naming is not supported.

## Provider Reference

| Provider | Name | Used For |
|----------|------|----------|
| `akash1kqzpqqhm39umt06wu8m4hx63v5hefhrfmjf9dj` | leet.haus | service-cloud-api |
| `akash1xmjzu9dczlg9fa4v3pfvwzn7ty89r003laj4ac` | tagus.host | service-auth |
| `akash1aaul837r7en7hpk9wv2svg8u78fdq0t2j2e82z` | DigitalFrontier | SSL proxy |
| `akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc` | Europlots | Infisical (BLOCKED for other services — NAT hairpin) |

## Blocked Providers

| Provider | Reason |
|----------|--------|
| `akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc` | Europlots — SSL proxy historically ran here; NAT hairpin issue |
| `akash1smapjx8m8363nmdvc2yr9atlqy8vcql73m9l0v` | Broken hostname |

## View Deployments

Deployments can be viewed on Cloudmos:
```
https://deploy.cloudmos.io/deployment/akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn/{dseq}
```

---

*Last updated: 2026-02-06*
