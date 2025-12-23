# AlternateFutures Backup Strategy

## Overview

All AlternateFutures services with databases have automated daily backups using a consistent approach:

- **Encryption:** `age` public-key encryption
- **Primary Storage:** Storacha (IPFS + Filecoin)
- **Secondary Storage:** GitHub Artifacts (30-day retention)

This dual-storage approach ensures backups are accessible even if AlternateFutures infrastructure is completely down.

## Services with Backups

| Service | Database | Schedule | Documentation |
|---------|----------|----------|---------------|
| `service-secrets` | Infisical API export | 3 AM UTC | [BACKUP.md](https://github.com/alternatefutures/service-secrets/blob/main/BACKUP.md) |
| `service-cloud-api` | PostgreSQL | 4 AM UTC | [BACKUP.md](https://github.com/alternatefutures/service-cloud-api/blob/main/BACKUP.md) |
| `service-auth` | SQLite | 5 AM UTC | [BACKUP.md](https://github.com/alternatefutures/service-auth/blob/main/BACKUP.md) |

## Storacha Configuration

All services share a single Storacha space for backups:

- **Account:** angela@alternatefutures.ai
- **Space:** `alternatefutures-backups`
- **Space DID:** `did:key:z6Mkoe31W9Z8WBznHQ6GPrfPaBdjnFC1fMt6P8QbZ9kKoQU6`
- **CI Agent DID:** `did:key:z6MksU8hnbAmL2xaY7nrRZ6az1qENLMcYdRgJvdPEzFgJzXs`
- **Gateway:** https://w3s.link/ipfs/
- **Console:** https://console.storacha.network

## Required GitHub Secrets

Each repository with backups needs these secrets:

| Secret | Description | How to Generate |
|--------|-------------|-----------------|
| `AGE_PUBLIC_KEY` | Public key for encrypting backups | `age-keygen` |
| `W3_PRINCIPAL` | Storacha agent private key (base64) | `w3 key create` |
| `W3_PROOF` | Storacha delegation proof (base64) | `w3 delegation create` |

### Additional Secrets (service-secrets only)

| Secret | Description |
|--------|-------------|
| `INFISICAL_CLIENT_ID` | Machine identity client ID |
| `INFISICAL_CLIENT_SECRET` | Machine identity secret |
| `INFISICAL_PROJECT_ID` | Infisical project UUID |

## Finding Backups

### Via Storacha Gateway

Each backup workflow logs the CID on completion. Access via:
```
https://w3s.link/ipfs/<CID>
```

### Via w3 CLI

```bash
# Login (one-time)
w3 login angela@alternatefutures.ai

# List all uploads
w3 ls
```

### Via GitHub Artifacts

1. Go to the repository's Actions tab
2. Select the backup workflow run
3. Download the artifact (available for 30 days)

## Restore Procedure (General)

1. **Download backup**
   ```bash
   curl -o backup.age "https://w3s.link/ipfs/<CID>"
   ```

2. **Decrypt with age**
   ```bash
   age -d -i ~/.age-key.txt -o backup backup.age
   ```

3. **Restore to service** (see service-specific docs)

## Age Key Management

The age private key is required for decryption. Store securely in:
- GitHub Secrets (`AGE_SECRET_KEY`)
- 1Password or other password manager
- Offline backup (USB drive, printed copy)

Generate a new key pair:
```bash
age-keygen -o ~/.age-key.txt
# Public key is printed to stdout
```

## Disaster Recovery Scenarios

### Scenario 1: Single Service Down

1. Identify which backups are needed
2. Download from Storacha or GitHub Artifacts
3. Decrypt and restore per service documentation

### Scenario 2: Complete Infrastructure Outage

1. Backups remain accessible via:
   - Storacha gateway (https://w3s.link)
   - GitHub Artifacts
2. Age private key required (ensure it's stored externally)
3. Restore order recommendation:
   1. `service-secrets` (Infisical - secrets for other services)
   2. `service-auth` (authentication)
   3. `service-cloud-api` (main application data)

## Future Improvements

See [Issue #1](https://github.com/alternatefutures/.github/issues/1) for planned self-healing backup service.

## Contacts

- **Primary:** Angela (@wonderwomancode)
- **Storacha Account:** angela@alternatefutures.ai
