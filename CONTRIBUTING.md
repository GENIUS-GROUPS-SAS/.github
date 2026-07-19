# Contributing to GENIUS GROUPS

First off, thank you for considering contributing to GENIUS GROUPS. We take our engineering culture seriously, and every contribution — whether it's a bug fix, a feature, documentation, or a typo correction — makes a difference.

This document is the **single source of truth** for how we work. Read it carefully. Follow it consistently.

---

## Table of Contents

1. [Getting Started](#1-getting-started)
2. [Git Flow](#2-git-flow)
3. [Branch Naming](#3-branch-naming)
4. [Conventional Commits](#4-conventional-commits)
5. [Code Review](#5-code-review)
6. [Pull Requests](#6-pull-requests)
7. [Documentation](#7-documentation)
8. [Testing](#8-testing)
9. [Architecture Decision Records (ADR)](#9-architecture-decision-records-adr)
10. [Security Rules](#10-security-rules)

---

## 1. Getting Started

### Prerequisites

Before you start contributing, make sure you have:

- Read the [Code of Conduct](./CODE_OF_CONDUCT.md) — we enforce it.
- Read the [Architecture Standards](./ARCHITECTURE.md) — understand our engineering principles.
- Read the [Development Guide](./DEVELOPMENT.md) — get your environment set up.
- Checked existing [issues](https://github.com/orgs/GENIUS-GROUPS-SAS/issues) and [discussions](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) to avoid duplicates.

### Find Something to Work On

- Look for issues labeled [`good first issue`](https://github.com/orgs/GENIUS-GROUPS-SAS/labels/good%20first%20issue) — these are curated for newcomers.
- Look for issues labeled [`help wanted`](https://github.com/orgs/GENIUS-GROUPS-SAS/labels/help%20wanted) — these are tasks we'd love help with.
- If you have an idea that isn't in the issues, open a [Discussion](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) first to gauge interest before writing code.

### Fork and Clone

```bash
# Fork the repository on GitHub, then:
git clone https://github.com/<your-username>/<repo>.git
cd <repo>
git remote add upstream https://github.com/GENIUS-GROUPS-SAS/<repo>.git
```

---

## 2. Git Flow

We use a **trunk-based development** model with short-lived feature branches. This keeps integration pain low and deployment frequency high.

```
main (always deployable)
 │
 ├── feature/GI-101-add-webhook-signing ──→ PR ──→ merge to main
 │
 ├── bugfix/GI-102-fix-rate-limit-bug ────→ PR ──→ merge to main
 │
 └── hotfix/GI-103-patch-payment-bug ─────→ PR ──→ merge to main
```

### Rules

| Rule | Description |
|---|---|
| **`main` is sacred** | `main` is always deployable. Never commit directly to `main`. |
| **Short-lived branches** | Feature branches should live no longer than 3 days. If they do, rebase. |
| **One PR per branch** | Each branch addresses one issue or one feature. Don't mix concerns. |
| **Rebase, don't merge** | Keep history linear. Rebase your branch on `main` before opening a PR. |
| **Squash on merge** | PRs are squash-merged. The PR title becomes the commit message. |
| **Delete branches after merge** | Keep the repository clean. |

### Syncing with Upstream

```bash
git fetch upstream
git rebase upstream/main
```

---

## 3. Branch Naming

Branch names follow a strict convention that encodes the type, ticket number, and a short description.

### Format

```
<type>/<TICKET>-<short-description>
```

### Types

| Type | Usage | Example |
|---|---|---|
| `feature` | New functionality | `feature/GI-101-add-webhook-signing` |
| `bugfix` | Non-critical bug fix | `bugfix/GI-102-fix-rate-limit-bug` |
| `hotfix` | Critical production fix | `hotfix/GI-103-patch-payment-bug` |
| `docs` | Documentation only | `docs/GI-104-update-api-readme` |
| `refactor` | Code refactoring, no behavior change | `refactor/GI-105-extract-signature-service` |
| `test` | Test additions or improvements | `test/GI-106-add-signature-unit-tests` |
| `chore` | Build, CI, dependencies, cleanup | `chore/GI-107-upgrade-dependencies` |
| `security` | Security-related changes | `security/GI-108-fix-hmac-verification` |

### Rules

- Use **kebab-case** for the description (lowercase, hyphen-separated).
- Keep descriptions **under 40 characters**.
- Always include the **ticket number**. If there's no ticket, create one.
- Never use special characters other than hyphens.

---

## 4. Conventional Commits

We follow [Conventional Commits 1.0.0](https://www.conventionalcommits.org/) strictly. This enables automated changelog generation, semantic versioning, and release notes.

### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

| Type | Description | Bumps |
|---|---|---|
| `feat` | New feature | Minor (`x.Y.0`) |
| `fix` | Bug fix | Patch (`x.y.Z`) |
| `docs` | Documentation changes | — |
| `style` | Formatting, no code change | — |
| `refactor` | Code refactoring, no behavior change | — |
| `test` | Test additions or changes | — |
| `chore` | Build, CI, tooling, dependencies | — |
| `perf` | Performance improvement | Patch (`x.y.Z`) |
| `security` | Security fix | Patch or Minor |
| `BREAKING CHANGE` | Incompatible API change | Major (`X.0.0`) |

### Scopes

Scopes are optional but encouraged. They map to bounded contexts or modules:

| Scope | Context |
|---|---|
| `api` | API layer (controllers, routes, middleware) |
| `domain` | Domain layer (entities, value objects, events) |
| `infra` | Infrastructure layer (DB, external APIs, storage) |
| `ui` | Frontend / UI |
| `sdk` | SDK packages |
| `docs` | Documentation |
| `ci` | CI/CD pipeline |
| `db` | Database / migrations |
| `auth` | Authentication / authorization |
| `payments` | Payment-related logic |
| `signature` | Electronic signature |
| `webhooks` | Webhook handling |

### Examples

```
feat(api): add POST /api/v1/quotes endpoint with idempotency key
fix(payments): correct webhook signature verification for SHA-256
docs(api): update OpenAPI spec for quote creation endpoint
refactor(domain): extract Signature value object from Quote entity
test(signature): add unit tests for hash generation and verification
chore(ci): upgrade to Node 20 in CI pipeline
perf(webhooks): batch webhook delivery with exponential backoff
security(auth): enforce rate limiting on API key validation endpoint
```

### Breaking Changes

Mark breaking changes with a `!` after the scope, or with a `BREAKING CHANGE` footer:

```
feat(api)!: rename client_name to customer_name in quote schema

BREAKING CHANGE: The `client_name` field has been renamed to `customer_name`
in all quote-related API responses. Consumers must update their integrations.
```

### Rules

- **Subject line**: imperative mood, lowercase, no period, max 72 characters.
- **Body**: explain *why*, not *what*. Wrap at 80 characters.
- **Footer**: reference issues, breaking changes, co-authors.
- **One commit per logical change** — don't mix unrelated changes in a single commit.

---

## 5. Code Review

Code review is **mandatory**. No code reaches `main` without at least one approval from a maintainer or core team member.

### For Authors

1. **Self-review first.** Read your own diff before requesting review. Fix obvious issues.
2. **Keep PRs small.** Target < 400 lines of diff. If it's larger, break it into multiple PRs.
3. **Write a clear PR description.** Follow the [PR template](./PULL_REQUEST_TEMPLATE.md).
4. **Respond to feedback gracefully.** Don't take criticism personally. Engage with the substance.
5. **Resolve threads properly.** Mark conversations as resolved only after the change is pushed.

### For Reviewers

1. **Review within 24 hours.** Don't let PRs sit idle.
2. **Be kind.** Critique the code, not the person. Use "consider" instead of "you should."
3. **Be thorough.** Check logic, edge cases, security, performance, tests, and documentation.
4. **Use labels:**
   - `pr/needs-changes` — changes required before merge
   - `pr/approved` — approved and ready to merge
   - `pr/blocked` — blocked on another PR or decision
5. **Approve explicitly.** Use GitHub's "Approve" button. Don't approve with comments like "LGTM" alone — verify the changes.

### Review Checklist

| Category | What to Check |
|---|---|
| **Correctness** | Does the code do what it claims? Are edge cases handled? |
| **Architecture** | Does it follow Clean Architecture / DDD? Are boundaries respected? |
| **Security** | Input validation? Output encoding? No secrets? OWASP addressed? |
| **Performance** | N+1 queries? Unnecessary loops? Memory leaks? |
| **Tests** | Are tests meaningful? Do they cover the change? Do they pass? |
| **Documentation** | Is the change documented? API spec updated? |
| **Style** | Linter passes? No dead code? Naming conventions followed? |

---

## 6. Pull Requests

### Opening a PR

1. **Rebase** your branch on the latest `main`.
2. **Run all tests locally** — CI should be a confirmation, not a discovery.
3. **Open the PR** using the [PR template](./PULL_REQUEST_TEMPLATE.md).
4. **Link the issue** — use "Closes #NNN" in the description.
5. **Request review** from the appropriate maintainer (see [MAINTAINERS.md](./MAINTAINERS.md)).

### PR Size Guidelines

| Size | Lines Changed | Action |
|---|---|---|
| **Small** | < 100 | Quick review, fast merge |
| **Medium** | 100-400 | Standard review |
| **Large** | 400-800 | Consider splitting |
| **Extra Large** | > 800 | Must be split into multiple PRs |

### CI Requirements

All CI checks must pass before merge:

- Linter (code style)
- Static analysis (type safety, security scan)
- Unit tests
- Integration tests
- Build (compilation, bundling)
- Documentation build (if applicable)

### Merge Strategy

- **Squash and merge** — the default for all PRs.
- The PR title (in Conventional Commits format) becomes the commit on `main`.
- Delete the branch after merge.

---

## 7. Documentation

> **Documentation First** is a core principle. Nothing is merged without documentation.

### What to Document

| Change Type | Documentation Required |
|---|---|
| New API endpoint | OpenAPI spec + example request/response |
| New feature | README section + user guide update |
| Breaking change | Migration guide + CHANGELOG entry |
| Architecture change | ADR (see below) |
| New dependency | Justification in PR description + security review |
| Bug fix | CHANGELOG entry (if user-facing) |

### Documentation Standards

- Write in **English** (professional, clear, concise).
- Use **active voice** — "The API returns..." not "It is returned by the API..."
- Include **code examples** for all API endpoints and SDK methods.
- Use **diagrams** (Mermaid or PlantUML) for architectural concepts.
- Keep documentation **versioned** with the code — it lives in the same repository.

---

## 8. Testing

> **Test First** is a core principle. Tests accompany or precede implementation.

### Test Pyramid

```
        /\
       /E2E\          ← Few, slow, expensive (critical paths only)
      /------\
     /Functional\     ← More, medium speed (user journeys)
    /------------\
   /  Integration  \   ← Many, fast (API, database, services)
  /----------------\
 /     Unit         \ ← Most, fastest (logic, services, value objects)
/--------------------\
```

### Requirements

| Test Type | Minimum Coverage | What to Test |
|---|---|---|
| **Unit** | 70% of business logic | Services, value objects, domain logic, calculations |
| **Integration** | 100% of API endpoints | All REST endpoints, webhook handlers, middleware |
| **Functional** | 100% of critical paths | Quote → Signature → Invoice → Payment |
| **E2E** | Critical user journeys | Full browser-based flows |
| **Security** | OWASP Top 10 | Authentication, authorization, input validation, injection |

### Test Naming

```
test_<context>_<scenario>_<expected_result>

# Examples:
test_signature_hash_generation_returns_sha256_hex_string
test_webhook_verification_rejects_invalid_hmac_signature
test_quote_creation_with_idempotency_key_returns_existing_quote_on_duplicate
```

### What Not to Test

- Framework internals (don't test the ORM, test your queries)
- Trivial getters/setters
- Third-party libraries
- Configuration loading (unless it has logic)

---

## 9. Architecture Decision Records (ADR)

An ADR is a short markdown document that captures **one** architectural decision. Every significant technical choice must be documented as an ADR before it's merged.

### When to Write an ADR

Write an ADR when you:

- Choose a framework, library, or tool
- Define a new pattern or convention
- Change an existing architectural pattern
- Introduce a new external dependency
- Make a decision that affects multiple teams or products
- Make a decision that is difficult to reverse

### ADR Format

```markdown
# ADR-NNN: Title of the Decision

## Status
Proposed | Accepted | Deprecated | Superseded by ADR-XXX

## Date
YYYY-MM-DD

## Context
Describe the problem, constraints, and forces that motivate this decision.
What are we trying to solve? What options exist?

## Decision
State the decision clearly. "We will..." or "We have chosen..."

## Consequences
### Positive
- [Benefit 1]

### Negative
- [Trade-off 1]

### Neutral
- [Observation]

## Alternatives Considered
| Alternative | Reason for Rejection |
|---|---|
| [Option A] | [Why not] |
| [Option B] | [Why not] |
```

### ADR Lifecycle

1. **Propose** — create a new ADR file in `/docs/adr/` with status `Proposed`.
2. **Discuss** — link the ADR in a PR or Discussion. Gather feedback.
3. **Accept** — a maintainer approves the ADR. Status changes to `Accepted`.
4. **Implement** — the decision is implemented. The ADR is referenced in PRs.
5. **Deprecate** — if the decision is reversed, status changes to `Deprecated` or `Superseded by ADR-XXX`.

### ADR Numbering

- ADRs are numbered sequentially: `ADR-001`, `ADR-002`, etc.
- Numbers are never reused.
- Superseded ADRs keep their number and reference the new ADR.

---

## 10. Security Rules

Security is not optional. The following rules apply to **every** contribution.

### Never Commit Secrets

- No API keys, passwords, tokens, or credentials in code.
- Use environment variables or a secrets manager.
- If you accidentally commit a secret, **revoke it immediately** and contact [security@geniusgroups.africa](mailto:security@geniusgroups.africa).

### Input Validation

- Validate **all** user input — API payloads, query parameters, headers, file uploads.
- Use allowlists, not blocklists.
- Validate type, length, format, and range.

### Output Encoding

- Encode output for the correct context (HTML, JSON, URL, SQL).
- Never concatenate SQL — use parameterized queries.
- Use framework-provided escaping for templates.

### Authentication & Authorization

- Every API endpoint must require authentication (unless explicitly public).
- Every action must check authorization (not just authentication).
- Use the principle of least privilege.
- Rate-limit authentication endpoints.

### Dependencies

- No new dependencies without review.
- Check for known vulnerabilities (`npm audit`, `composer audit`, or equivalent).
- Pin major versions to avoid unexpected breaking changes.

### Reporting Vulnerabilities

If you discover a security vulnerability, **do not open a public issue**. Follow our [Security Policy](./SECURITY.md) and email [security@geniusgroups.africa](mailto:security@geniusgroups.africa).

---

## Thank You

Contributing to open source is an act of generosity. We recognize and appreciate every contributor. Your name may appear in our contributors list, release notes, and community highlights.

If you have questions about this document, open a [Discussion](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) — we're happy to help.

---

> **GENIUS GROUPS SAS** — Build African-first Digital Infrastructure.
