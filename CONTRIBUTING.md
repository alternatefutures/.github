# Contributing to AlternateFutures

This document establishes the development workflow, branching strategy, and environment promotion rules for all AlternateFutures repositories.

## Table of Contents

- [Branch Strategy](#branch-strategy)
- [Environment Promotion](#environment-promotion)
- [Branch Naming Conventions](#branch-naming-conventions)
- [Commit Message Format](#commit-message-format)
- [Pull Request Process](#pull-request-process)
- [Code Review Requirements](#code-review-requirements)
- [Issue Management](#issue-management)
- [Release Process](#release-process)
- [Hotfix Procedure](#hotfix-procedure)

---

## Branch Strategy

We use a **trunk-based development** approach with protected environment branches.

```
                                    ┌─────────────┐
                                    │ PRODUCTION  │
                                    │   (main)    │
                                    └──────▲──────┘
                                           │
                              PR + Approval + CI Pass
                                           │
                                    ┌──────┴──────┐
                                    │   STAGING   │
                                    │  (staging)  │
                                    └──────▲──────┘
                                           │
                              PR + CI Pass (auto-merge option)
                                           │
                                    ┌──────┴──────┐
                                    │     DEV     │
                                    │    (dev)    │
                                    └──────▲──────┘
                                           │
                              PR + CI Pass
                                           │
                    ┌──────────────────────┴──────────────────────┐
                    │                                              │
             ┌──────┴──────┐                               ┌───────┴───────┐
             │   Feature   │                               │    Bugfix     │
             │  Branches   │                               │   Branches    │
             └─────────────┘                               └───────────────┘
```

### Protected Branches

| Branch | Purpose | Protection Rules |
|--------|---------|------------------|
| `main` | Production code | Requires PR, 1+ approval, CI pass, no force push |
| `staging` | Pre-production testing | Requires PR, CI pass, no force push |
| `dev` | Integration branch | Requires PR, CI pass |

### Branch Lifecycle

1. **Create branch** from `dev` with proper naming convention
2. **Develop** with atomic commits following commit format
3. **Push** to remote and create PR to `dev`
4. **Review** - CI runs, code review if required
5. **Merge** to `dev` using squash merge
6. **Promote** to `staging` via PR when feature complete
7. **Deploy** to production via PR to `main` after staging verification

---

## Environment Promotion

### Development → Staging

| Requirement | Details |
|-------------|---------|
| **CI Status** | All checks must pass |
| **Tests** | Unit + integration tests pass |
| **Lint** | No lint errors |
| **Types** | TypeScript strict mode passes |
| **Approval** | Optional (auto-merge allowed) |

```bash
# Create staging PR
gh pr create --base staging --head dev --title "chore: promote dev to staging"
```

### Staging → Production

| Requirement | Details |
|-------------|---------|
| **CI Status** | All checks must pass |
| **Staging Verification** | Manual QA complete |
| **Security Scan** | No critical vulnerabilities |
| **Approval** | 1+ team member approval required |
| **Changelog** | Release notes prepared |

```bash
# Create production PR
gh pr create --base main --head staging --title "release: v1.2.3"
```

### Environment URLs

| Environment | Domain Pattern | Example |
|-------------|----------------|---------|
| Development | `dev-*.alternatefutures.ai` | `dev-api.alternatefutures.ai` |
| Staging | `staging-*.alternatefutures.ai` | `staging-api.alternatefutures.ai` |
| Production | `*.alternatefutures.ai` | `api.alternatefutures.ai` |

---

## Branch Naming Conventions

### Format

```
<type>/<issue-number>-<short-description>
```

### Types

| Type | Purpose | Example |
|------|---------|---------|
| `feat` | New feature | `feat/42-user-authentication` |
| `fix` | Bug fix | `fix/87-token-expiry-bug` |
| `docs` | Documentation | `docs/15-api-documentation` |
| `style` | Formatting (no logic change) | `style/22-lint-fixes` |
| `refactor` | Code restructuring | `refactor/33-auth-service-cleanup` |
| `perf` | Performance improvement | `perf/44-query-optimization` |
| `test` | Adding/updating tests | `test/55-auth-unit-tests` |
| `chore` | Maintenance tasks | `chore/66-dependency-update` |
| `ci` | CI/CD changes | `ci/77-add-staging-workflow` |
| `security` | Security fixes | `security/88-patch-vulnerability` |
| `hotfix` | Production emergency fix | `hotfix/99-critical-auth-bug` |

### Rules

1. **Lowercase only** - No uppercase letters
2. **Hyphens for spaces** - Use `-` not `_` or camelCase
3. **Issue reference required** - Every branch must reference a GitHub issue
4. **Keep it short** - Max 50 characters for description
5. **No special characters** - Only alphanumeric and hyphens

### Examples

```bash
# Good
feat/123-webauthn-passkeys
fix/456-jwt-refresh-rotation
refactor/789-billing-service-split

# Bad
Feature/123-WebAuthn-Passkeys     # Wrong: uppercase
feat_123_webauthn                 # Wrong: underscores
feat/webauthn-passkeys            # Wrong: no issue number
feat/123-implement-new-webauthn-passkey-authentication-system  # Wrong: too long
```

---

## Commit Message Format

We use **Conventional Commits** specification.

### Format

```
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

### Types

Same as branch types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `ci`, `security`

### Scopes

| Scope | Description |
|-------|-------------|
| `auth` | Authentication service |
| `api` | Cloud API service |
| `app` | Web application |
| `cli` | CLI tool |
| `sdk` | JavaScript SDK |
| `docs` | Documentation site |
| `proxy` | Infrastructure proxy |
| `dns` | DNS infrastructure |
| `billing` | Billing system |
| `agents` | AI agents system |

### Examples

```bash
# Feature
feat(auth): add WebAuthn passkey authentication

Implements FIDO2 WebAuthn for passwordless login.
- Add registration flow with resident credentials
- Add authentication flow with discoverable credentials
- Support cross-platform authenticators

Closes #53

# Bug fix
fix(api): resolve JWT refresh token race condition

Tokens were being rotated before previous request completed,
causing intermittent 401 errors under load.

Fixes #127

# Breaking change
feat(sdk)!: update authentication API

BREAKING CHANGE: AuthClient constructor now requires config object
instead of individual parameters.

Before: new AuthClient(url, key)
After: new AuthClient({ url, apiKey })

# Chore
chore(deps): update dependencies

- @hono/node-server 1.13.2 → 1.13.7
- prisma 5.22.0 → 5.23.0
- vitest 2.1.5 → 2.1.6
```

### Commit Rules

1. **Subject line max 72 characters**
2. **Use imperative mood** - "add feature" not "added feature"
3. **No period at end of subject**
4. **Blank line between subject and body**
5. **Body wraps at 72 characters**
6. **Reference issues in footer** - `Closes #123` or `Fixes #456`

---

## Pull Request Process

### PR Title Format

Same as commit format - PR titles are validated by CI.

```
<type>(<scope>): <description>
```

### PR Description Template

All PRs must include:

```markdown
## Summary
<!-- What does this PR do? 1-3 bullet points -->

## Changes
<!-- List specific changes made -->

## Testing
<!-- How was this tested? -->
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed

## Screenshots
<!-- If applicable, add screenshots -->

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Documentation updated (if needed)
- [ ] No console.log or debug statements
- [ ] No hardcoded secrets or credentials

## Related Issues
<!-- Link related issues -->
Closes #
```

### PR Size Guidelines

| Size | Lines Changed | Review Time |
|------|---------------|-------------|
| XS | < 50 | < 15 min |
| S | 50-200 | 15-30 min |
| M | 200-500 | 30-60 min |
| L | 500-1000 | 1-2 hours |
| XL | > 1000 | Split into smaller PRs |

### Merge Strategies

| Target Branch | Strategy | Reason |
|---------------|----------|--------|
| `dev` | Squash merge | Clean history, single commit per feature |
| `staging` | Merge commit | Preserve feature commits for debugging |
| `main` | Merge commit | Full history preserved |

---

## Code Review Requirements

### Review Matrix

| Change Type | Required Reviewers | Auto-merge |
|-------------|-------------------|------------|
| Documentation only | 0 | Yes (if CI passes) |
| Test only | 0 | Yes (if CI passes) |
| Chore/deps | 1 | No |
| Feature/fix | 1 | No |
| Security-related | 2 (including security lead) | No |
| Breaking change | 2 | No |
| Production hotfix | 1 (expedited) | No |

### Review Checklist

Reviewers should verify:

- [ ] Code is readable and maintainable
- [ ] Logic is correct and handles edge cases
- [ ] Error handling is appropriate
- [ ] No security vulnerabilities introduced
- [ ] Tests cover new/changed functionality
- [ ] No breaking changes (or properly documented)
- [ ] Performance impact considered
- [ ] Documentation updated if needed

### Review Response Time

| Priority | Response Time |
|----------|---------------|
| Critical (production issue) | < 2 hours |
| High (blocking work) | < 4 hours |
| Normal | < 24 hours |
| Low | < 48 hours |

---

## Issue Management

### Issue Types

| Type | Label | Template |
|------|-------|----------|
| Feature Request | `enhancement` | feature_request.md |
| Bug Report | `bug` | bug_report.md |
| Task | `task` | task.md |
| Security | `security` | SECURITY.md |

### Issue Labels

#### Priority
- `priority: critical` - Production down, security breach
- `priority: high` - Major feature blocker
- `priority: medium` - Important but not blocking
- `priority: low` - Nice to have

#### Status
- `status: triage` - Needs initial review
- `status: accepted` - Confirmed and prioritized
- `status: in-progress` - Being worked on
- `status: blocked` - Waiting on dependency
- `status: needs-info` - Waiting on reporter

#### Type
- `type: bug` - Something isn't working
- `type: feature` - New functionality
- `type: enhancement` - Improvement to existing
- `type: docs` - Documentation
- `type: security` - Security related

#### Component
- `component: auth` - Authentication service
- `component: api` - Cloud API
- `component: app` - Web application
- `component: cli` - CLI tool
- `component: infra` - Infrastructure

### Issue → Branch → PR Flow

```
┌─────────────────┐
│  Create Issue   │
│    #123         │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Create Branch  │
│feat/123-feature │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Development    │
│  (commits)      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Create PR      │
│  → dev branch   │
│  Closes #123    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Review/Merge   │
│  Issue closed   │
└─────────────────┘
```

---

## Release Process

### Version Format

We use **Semantic Versioning** (SemVer): `MAJOR.MINOR.PATCH`

| Increment | When |
|-----------|------|
| MAJOR | Breaking changes |
| MINOR | New features (backward compatible) |
| PATCH | Bug fixes (backward compatible) |

### Release Workflow

1. **Prepare Release**
   ```bash
   # From staging branch
   git checkout staging
   git pull origin staging

   # Create release branch
   git checkout -b release/v1.2.3
   ```

2. **Update Version**
   ```bash
   # Update package.json, CHANGELOG.md
   pnpm version minor  # or major/patch
   ```

3. **Create Release PR**
   ```bash
   gh pr create --base main --title "release: v1.2.3" \
     --body "## Release v1.2.3\n\nSee CHANGELOG.md for details"
   ```

4. **After Merge - Tag Release**
   ```bash
   git checkout main
   git pull origin main
   git tag -a v1.2.3 -m "Release v1.2.3"
   git push origin v1.2.3
   ```

5. **Create GitHub Release**
   ```bash
   gh release create v1.2.3 --title "v1.2.3" --notes-file CHANGELOG.md
   ```

### Release Checklist

- [ ] All tests passing
- [ ] Staging environment verified
- [ ] Security scan complete
- [ ] CHANGELOG.md updated
- [ ] Version numbers updated
- [ ] Documentation updated
- [ ] Migration guide (if breaking changes)
- [ ] Team notified

---

## Hotfix Procedure

For critical production issues that cannot wait for normal release cycle.

### Hotfix Flow

```
                    ┌─────────────┐
                    │    main     │
                    └──────┬──────┘
                           │
              ┌────────────┴────────────┐
              │                         │
              ▼                         │
       ┌──────────────┐                 │
       │hotfix/99-fix │                 │
       └──────┬───────┘                 │
              │                         │
              │ PR + expedited review   │
              │                         │
              ▼                         │
       ┌──────────────┐                 │
       │    main      │◄────────────────┘
       └──────┬───────┘
              │
              │ Backport
              ▼
       ┌──────────────┐
       │   staging    │
       └──────┬───────┘
              │
              │ Backport
              ▼
       ┌──────────────┐
       │     dev      │
       └──────────────┘
```

### Hotfix Steps

1. **Create hotfix branch from main**
   ```bash
   git checkout main
   git pull origin main
   git checkout -b hotfix/99-critical-bug
   ```

2. **Fix and commit**
   ```bash
   git commit -m "fix(auth): resolve critical token validation bug

   Fixes #99"
   ```

3. **Create expedited PR to main**
   ```bash
   gh pr create --base main --title "fix(auth): critical token validation" \
     --label "priority: critical,hotfix"
   ```

4. **After merge - backport to staging and dev**
   ```bash
   # Backport to staging
   git checkout staging
   git cherry-pick <commit-sha>
   git push origin staging

   # Backport to dev
   git checkout dev
   git cherry-pick <commit-sha>
   git push origin dev
   ```

---

## CI/CD Workflows

### Required Workflows

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| `pr-check.yml` | PR to any branch | Lint, type-check, test |
| `deploy-dev.yml` | Push to `dev` | Deploy to dev environment |
| `deploy-staging.yml` | Push to `staging` | Deploy to staging environment |
| `deploy-prod.yml` | Push to `main` | Deploy to production |
| `security-scan.yml` | Daily + PR | Dependency vulnerability scan |

### CI Checks

All PRs must pass:

- [ ] Conventional commit title
- [ ] TypeScript type check
- [ ] ESLint/Biome lint
- [ ] Unit tests
- [ ] Integration tests (where applicable)
- [ ] Build succeeds
- [ ] No security vulnerabilities (critical/high)

---

## Quick Reference

### Create Feature Branch
```bash
# From dev branch
git checkout dev && git pull
git checkout -b feat/123-feature-name
```

### Create PR
```bash
gh pr create --base dev --title "feat(scope): description" \
  --body "Closes #123"
```

### Promote to Staging
```bash
gh pr create --base staging --head dev \
  --title "chore: promote dev to staging"
```

### Promote to Production
```bash
gh pr create --base main --head staging \
  --title "release: v1.2.3"
```

### Create Hotfix
```bash
git checkout main && git pull
git checkout -b hotfix/999-critical-fix
# Fix, commit, PR directly to main
```

---

*Last updated: 2025-12-18*
