# Release Process

This document describes how GENIUS GROUPS SAS manages releases across all products. It covers versioning, the release lifecycle, changelog management, and release notes.

---

## Table of Contents

1. [Semantic Versioning](#1-semantic-versioning)
2. [Release Types](#2-release-types)
3. [Release Process](#3-release-process)
4. [Changelog](#4-changelog)
5. [Release Notes](#5-release-notes)
6. [Hotfix Process](#6-hotfix-process)
7. [Deprecation Policy](#7-deprecation-policy)

---

## 1. Semantic Versioning

All GENIUS GROUPS products follow [Semantic Versioning 2.0.0](https://semver.org/).

### Format

```
MAJOR.MINOR.PATCH
```

| Component | When It Changes | Example |
|---|---|---|
| **MAJOR** (X.0.0) | Breaking changes / incompatible API changes | `1.0.0` → `2.0.0` |
| **MINOR** (0.Y.0) | New features, backward-compatible | `1.0.0` → `1.1.0` |
| **PATCH** (0.0.Z) | Bug fixes, backward-compatible | `1.0.0` → `1.0.1` |

### Pre-release Versions

| Suffix | Usage | Example |
|---|---|---|
| `-alpha.N` | Internal testing, unstable | `1.2.0-alpha.1` |
| `-beta.N` | Feature-complete, external testing | `1.2.0-beta.1` |
| `-rc.N` | Release candidate, production-ready pending validation | `1.2.0-rc.1` |

### Rules

- **MAJOR version 0** (`0.x.y`) means the API is not yet stable. Anything may change at any time.
- Once a product reaches `1.0.0`, backward compatibility is guaranteed within the same major version.
- Breaking changes require a **new major version** and a **migration guide**.
- Version numbers are **monotonically increasing** — never decrease.

---

## 2. Release Types

| Type | Frequency | Trigger | Version Bump |
|---|---|---|---|
| **Patch** | As needed | Bug fix, security fix | `x.y.Z` |
| **Minor** | Every 2-4 weeks | New features, enhancements | `x.Y.0` |
| **Major** | Every 6-12 months | Breaking changes, significant architecture shifts | `X.0.0` |
| **Hotfix** | As needed | Critical production issue | `x.y.Z` |
| **Pre-release** | As needed | Alpha, beta, RC before a minor/major | `x.y.z-alpha.N` |

---

## 3. Release Process

### Pre-release Checklist

Before cutting a release, the Lead Maintainer verifies:

- [ ] All planned features/fixes are merged to `main`
- [ ] All CI checks pass on `main`
- [ ] `CHANGELOG.md` is updated with all changes since the last release
- [ ] Migration guide is written (if breaking changes)
- [ ] Release notes are drafted
- [ ] Version number is determined (based on Conventional Commits since last release)
- [ ] Security review completed (if security-related changes)
- [ ] Documentation is updated
- [ ] OpenAPI spec is updated (if API changes)

### Step-by-Step Process

#### 1. Create a Release Branch (for minor/major releases)

```bash
git checkout main
git pull origin main
git checkout -b release/v1.2.0
```

#### 2. Update Version and Changelog

```bash
# Update version in package.json / pyproject.toml / composer.json
# Update CHANGELOG.md with the new version and date
git commit -m "chore(release): v1.2.0"
```

#### 3. Create a Pull Request

```bash
git push origin release/v1.2.0
# Open PR: release/v1.2.0 → main
# Title: "chore(release): v1.2.0"
```

#### 4. Review and Merge

- The release PR is reviewed by at least one other maintainer.
- CI must pass.
- Merge via squash-merge.

#### 5. Tag the Release

```bash
git checkout main
git pull origin main
git tag -a v1.2.0 -m "Release v1.2.0"
git push origin v1.2.0
```

#### 6. Create GitHub Release

- Go to the repository's **Releases** page on GitHub.
- Click **Draft a new release**.
- Select the tag `v1.2.0`.
- Title: `v1.2.0`
- Paste the changelog entries as the description.
- Attach any binaries (SDK packages, etc.).
- Publish the release.

#### 7. Publish Packages (if applicable)

```bash
# NPM
npm publish

# PyPI
python -m build && twine upload dist/*

# Packagist
# (automatic via git tag webhook)
```

#### 8. Deploy

- The tag push triggers the CD pipeline.
- The pipeline builds the production Docker image, pushes it to the registry, and deploys.
- For major releases, production deployment requires **manual approval**.

#### 9. Announce

- Post in GitHub Discussions (Announcements category)
- Update the status page if there was downtime
- Notify enterprise customers via email (for major releases)

### Automated Release (for patch releases)

For patch releases with only bug fixes, the process can be simplified:

```bash
# Ensure main is up to date and CI passes
git checkout main && git pull origin main

# Tag and push
git tag -a v1.0.3 -m "Release v1.0.3"
git push origin v1.0.3

# GitHub Release is created automatically by CI
```

---

## 4. Changelog

Every product repository maintains a `CHANGELOG.md` file at the root.

### Format

We follow the [Keep a Changelog](https://keepachangelog.com/) format.

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- New endpoint: POST /api/v1/quotes/{id}/duplicate
- Webhook event: quote.expired

### Changed
- Quote response now includes `signature_status` field

### Deprecated
- GET /api/v1/quotes?client_id= (use client_name filter instead)

### Removed
- Removed deprecated /api/v0/quotes endpoint

### Fixed
- Fixed HMAC signature verification failing on webhooks with large payloads
- Fixed race condition in invoice number generation

### Security
- Patched dependency vulnerability in request library (CVE-2024-XXXX)

## [1.2.0] - 2026-07-15

### Added
- Payment Plans (installments) with automated scheduling
- Escrow with conditional release

### Changed
- Upgraded API version to v1
- Webhook payload now includes `event_id` for idempotency

### Fixed
- Fixed duplicate webhook delivery on retry

## [1.1.0] - 2026-06-01

### Added
- GeniusPay sandbox environment support
- SDK: JavaScript/TypeScript package

## [1.0.0] - 2026-05-01

### Added
- Initial release
- Core payment engine (cards, Mobile Money, wallets)
- Merchant API REST
- Webhooks (HMAC-SHA256)
```

### Categories

| Category | Usage |
|---|---|
| **Added** | New features |
| **Changed** | Changes in existing functionality |
| **Deprecated** | Soon-to-be removed features |
| **Removed** | Removed features |
| **Fixed** | Bug fixes |
| **Security** | Vulnerability fixes |

### Rules

- The `[Unreleased]` section at the top accumulates changes between releases.
- When a release is cut, `[Unreleased]` becomes `[X.Y.Z] - YYYY-MM-DD`.
- A new empty `[Unreleased]` section is added at the top.
- Entries are generated from Conventional Commits since the last release.
- **Every** merged PR should add an entry to `[Unreleased]`.

---

## 5. Release Notes

Release notes are the public-facing summary of what changed in a release. They are published on the GitHub Release page.

### Structure

```markdown
## v1.2.0 — July 15, 2026

### 🎉 New Features

- **Payment Plans** — Merchants can now offer installment payments with automated scheduling and notifications.
- **Escrow** — Funds can be held in escrow until delivery is confirmed, with conditional release rules.

### 🔄 Changes

- API version bumped to v1. The previous version (v0) is deprecated and will be removed in v2.0.
- Webhook payloads now include `event_id` for improved idempotency handling.

### 🐛 Bug Fixes

- Fixed HMAC signature verification failing on webhooks with payloads > 1MB.
- Fixed race condition that could cause duplicate invoice numbers under high concurrency.

### 🔒 Security

- Patched CVE-2024-XXXX in the `request` library. All users should upgrade.

### 📦 Upgrading

See the [Migration Guide](./docs/migrations/v1.2.0.md) for breaking changes and upgrade steps.

### ❤️ Contributors

Thanks to @username1, @username2, and @username3 for their contributions to this release.
```

### Tone

- **Celebrate** new features — use emojis, highlight user value.
- **Be clear** about breaking changes — don't bury them.
- **Credit** contributors — name them publicly.
- **Link** to migration guides and detailed docs.

---

## 6. Hotfix Process

When a critical bug is found in production, a hotfix is released as quickly as possible.

### Process

```
1. Identify → 2. Branch from tag → 3. Fix → 4. Test → 5. Tag → 6. Deploy → 7. Merge back
```

#### Step-by-Step

```bash
# 1. Branch from the affected release tag
git checkout v1.2.0
git checkout -b hotfix/v1.2.1

# 2. Fix the bug
# ... make changes ...
git commit -m "fix(payments): correct webhook retry logic for failed deliveries"

# 3. Test
npm test

# 4. Update changelog
# Add entry to [Unreleased] or create a new [1.2.1] section

# 5. Tag
git tag -a v1.2.1 -m "Hotfix v1.2.1"
git push origin hotfix/v1.2.1
git push origin v1.2.1

# 6. CD pipeline deploys automatically

# 7. Merge back to main
git checkout main
git merge hotfix/v1.2.1
git push origin main
```

### Hotfix SLA

| Severity | Target Time to Release |
|---|---|
| **Critical** (production down, data loss) | 2 hours |
| **High** (major feature broken) | 8 hours |
| **Medium** (minor feature broken) | Next patch release |

---

## 7. Deprecation Policy

### Deprecation Timeline

| Stage | Duration | Action |
|---|---|---|
| **Announced** | T-6 months | Feature is marked deprecated in docs and changelog. Warning logs added. |
| **Sunset** | T-3 months | Feature still works but returns deprecation headers/warnings. Migration guide published. |
| **Removed** | T-0 | Feature is removed in the next major version. |

### API Version Deprecation

- Each API major version (v1, v2, etc.) is supported for **at least 12 months** after the next major version is released.
- Deprecation is announced via:
  - `Deprecation` HTTP header on deprecated endpoints
  - `Sunset` HTTP header with the removal date
  - Email notification to all API key holders
  - GitHub Discussions announcement
  - Changelog entry

### SDK Deprecation

- SDK major versions are supported for **at least 6 months** after the next major version.
- Deprecated SDK versions receive critical security fixes only — no new features.

---

## Release Calendar

| Product | Current | Next Minor | Next Major |
|---|---|---|---|
| GeniusPay™ | v1.0.x | v1.1.0 (Q3 2026) | v2.0.0 (2027) |
| Genius Invoice™ | — | v1.0.0 (Q4 2026) | — |
| Genius Docs Sign™ | — | — | v1.0.0 (2027) |
| Genius Identity™ | — | — | v1.0.0 (2027) |
| Genius AI™ | — | — | v1.0.0 (2027) |

---

> **GENIUS GROUPS SAS** — Releases are promises to our users. We make them carefully, document them thoroughly, and support them reliably.
