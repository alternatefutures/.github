# Akash Network Deployments

This file tracks active deployments on Akash Network. All information here is public blockchain data.

## Active Deployments

### service-cloud-api (GraphQL API)
| Field | Value |
|-------|-------|
| **dseq** | 24363709 |
| **Provider** | Europlots |
| **Ingress URL** | `cjrdmusuql9e34bevi8mjgj8pg.ingress.europlots.com` |
| **Custom Domain** | api.alternatefutures.ai |
| **Status** | Running |

### service-auth (Authentication)
| Field | Value |
|-------|-------|
| **dseq** | 24363650 |
| **Provider** | Europlots |
| **Ingress URL** | `uilm2birnle5b7ffcqvsgpolc4.ingress.europlots.com` |
| **Custom Domain** | auth.alternatefutures.ai |
| **Status** | Running |

### Infisical (Secrets Manager)
| Field | Value |
|-------|-------|
| **dseq** | 24352697 |
| **Provider** | akash1u5cdg7k3gl43mukca4aeultuz8x2j68mgwn28e |
| **Custom Domain** | secrets.alternatefutures.ai |
| **Status** | Running |

## Akash Account

- **Address**: `akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn`
- **Network**: Mainnet
- **Explorer**: [Cloudmos](https://deploy.cloudmos.io/addresses/akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn)

## Infrastructure Status

| Service | Domain | SSL | Status |
|---------|--------|-----|--------|
| GraphQL API | api.alternatefutures.ai | Provider cert | Active |
| Auth Service | auth.alternatefutures.ai | Provider cert | Active |
| Secrets Manager | secrets.alternatefutures.ai | Pending | Active |
| SSL Gateway | - | Let's Encrypt | Pending |

## Pending Work

- [ ] Deploy Caddy SSL gateway for Let's Encrypt certificates
- [ ] Configure DNS for secrets.alternatefutures.ai
- [ ] Enable proper SSL for custom domains

## Provider Reference

| Provider | Address | Region |
|----------|---------|--------|
| Europlots | `akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc` | EU |
| Spheron | `akash1u5cdg7k3gl43mukca4aeultuz8x2j68mgwn28e` | - |

## View Deployments

Deployments can be viewed on Cloudmos:
```
https://deploy.cloudmos.io/deployment/akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn/{dseq}
```

---

*Last updated: 2025-11-26*
