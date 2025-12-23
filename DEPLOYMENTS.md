# Akash Network Deployments

This file tracks active deployments on Akash Network. All information here is public blockchain data.

## Active Deployments

### IPFS Node (Kubo)
| Field | Value |
|-------|-------|
| **dseq** | 24642352 |
| **Provider** | Europlots |
| **Image** | ipfs/kubo:v0.27.0 |
| **API URL** | `http://provider.europlots.com:30670` |
| **Gateway URL** | `http://provider.europlots.com:32160` |
| **Swarm Port** | `provider.europlots.com:30951` |
| **Peer ID** | `12D3KooWHvattdgfMSsEvi93hYjpp5ixkr7ZFQfsBN8soHPLy7wV` |
| **Resources** | 2 CPU, 4GB RAM, 100GB persistent storage |
| **Cost** | ~32 uakt/block (~$2.50/month) |
| **Status** | Running |

#### Pinned Content
| Site | CID |
|------|-----|
| web-docs | `QmeQe1QuyiAiyCrJLASixPtH2VW6xQZxcpqCHJsUTtxfUR` |
| web-app | `QmU4VRKexpuA6RvYfXY9nUsgiHRMLDhxvZFi7ssGHn3aHj` |

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
| **dseq** | 24562739 |
| **Provider** | Europlots |
| **Ingress URL** | `ubsm31q4ol97b1pi5l06iognug.ingress.europlots.com` |
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

| Service | Domain/Endpoint | SSL | Status |
|---------|-----------------|-----|--------|
| GraphQL API | api.alternatefutures.ai | Provider cert | Active |
| Auth Service | auth.alternatefutures.ai | Provider cert | Active |
| Secrets Manager | secrets.alternatefutures.ai | Provider cert | Active |
| IPFS API | provider.europlots.com:30670 | - | Active |
| IPFS Gateway | provider.europlots.com:32160 | - | Active |

## DNS Configuration

| Domain | Record Type | Value |
|--------|-------------|-------|
| api.alternatefutures.ai | A | 170.75.255.101 |
| auth.alternatefutures.ai | A | 86.33.22.194 |
| secrets.alternatefutures.ai | A | 170.75.255.101 |

## Provider Reference

| Provider | Address | Region |
|----------|---------|--------|
| Europlots | `akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc` | EU |
| Spheron | `akash1u5cdg7k3gl43mukca4aeultuz8x2j68mgwn28e` | - |

## Environment Variables

```bash
# IPFS Node
IPFS_API_URL=http://provider.europlots.com:30670
IPFS_GATEWAY_URL=http://provider.europlots.com:32160
```

## View Deployments

Deployments can be viewed on Cloudmos:
```
https://deploy.cloudmos.io/deployment/akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn/{dseq}
```

---

*Last updated: 2025-12-15*
