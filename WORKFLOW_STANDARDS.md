# GitHub Workflow Standards

Standards for CI/CD workflows across all AlternateFutures repositories.

## Naming Convention

| Workflow | Filename | Trigger | Purpose |
|----------|----------|---------|---------|
| CI | `ci.yml` | PR + push to main | Lint, typecheck, test with coverage |
| Deploy | `deploy-aws.yml` / `docker-build.yml` | push to main (path-filtered) | Build, push, deploy to production |
| Backup | `backup-database.yml` | Scheduled (cron) | Database backup |
| Health | `health-check.yml` | Scheduled (5min cron) | Service health monitoring |

## Required Checks for All Repos

Every repository MUST have a `ci.yml` workflow that runs on PRs and pushes to `main`, performing:

1. **Lint** (`pnpm lint` or equivalent)
2. **Type check** (`pnpm build` or `tsc --noEmit`)
3. **Tests with coverage** (call the reusable `test.yml` or run `pnpm test:coverage`)

### Reusable Test Workflow

The org-level reusable workflow lives at `.github/.github/workflows/test.yml` and should be called by repos that don't need service containers (Postgres, Redis):

```yaml
jobs:
  test:
    uses: alternatefutures/.github/.github/workflows/test.yml@main
    with:
      node-version: "20"
```

Do NOT pass `pnpm-version` if the repo's `package.json` has a `packageManager`
field (it should) — `pnpm/action-setup` hard-fails when both are set. The
reusable workflow picks up the `packageManager` version automatically; only
pass `pnpm-version` for repos without the field.

Repos needing service containers (e.g. `service-cloud-api`) run tests inline but MUST still produce `coverage/coverage-summary.json`.

## Coverage Thresholds

All repos use vitest with `@vitest/coverage-v8`. Minimum thresholds (enforced in `vitest.config.ts`):

| Metric | Minimum |
|--------|---------|
| Lines | 60% |
| Functions | 60% |
| Branches | 50% |
| Statements | 60% |

Coverage reporters: `['text', 'json-summary', 'lcov']` (json-summary for badge generation, lcov for external tools).

## Coverage Badges

The reusable `test.yml` workflow extracts coverage from `coverage-summary.json` and renders a shields.io badge in the GitHub Actions step summary. To add to your README:

```markdown
![Coverage](https://img.shields.io/badge/coverage-XX%25-brightgreen)
```

Replace `XX` with actual coverage. Badge colors: >= 80% green, >= 60% yellowgreen, >= 40% yellow, < 40% red.

## Deploy Workflows

Deploy workflows MUST:

1. Build with `--platform linux/amd64` (Apple Silicon dev, amd64 servers)
2. Push to GHCR (`ghcr.io/alternatefutures/<image>`)
3. Run `prisma migrate deploy` after deploy (if applicable)
4. Include Discord notifications on success and failure
5. Perform a health check after deployment

## Repo-Specific Notes

### service-auth
- `test.yml` — calls reusable workflow
- `docker-build.yml` — build, push to GHCR, deploy to K3s, Discord alerts

### service-cloud-api
- `ci.yml` — inline test/lint/build with Postgres + Redis service containers
- `deploy-aws.yml` — build, push to GHCR, deploy to K3s, prisma migrate, Discord alerts
- `backup-database.yml` — scheduled DB backup

### web-app.alternatefutures.ai
- `ci.yml` — lint, typecheck, test with coverage (calls reusable)
- Deploys via Vercel (auto-deploy on push to main, no custom workflow needed)

### package-cloud-cli
- `test-runner.yml` — test on push
- `npm-publish.yml` — publish to npm on release
