# Development Guide

This guide covers everything you need to set up your development environment, run GENIUS GROUPS projects locally, and follow our development workflows.

---

## Table of Contents

1. [Prerequisites](#1-prerequisites)
2. [Installation](#2-installation)
3. [Docker](#3-docker)
4. [Local Development](#4-local-development)
5. [CI/CD](#5-cicd)
6. [Testing](#6-testing)
7. [Coding Standards](#7-coding-standards)

---

## 1. Prerequisites

### Required Tools

| Tool | Minimum Version | Purpose |
|---|---|---|
| **Git** | 2.40+ | Version control |
| **Docker** | 24.0+ | Containerization |
| **Docker Compose** | 2.20+ | Multi-container orchestration |
| **Node.js** | 20 LTS | JavaScript runtime (frontend, tooling) |
| **Python** | 3.12+ | Backend (selected products) |
| **PHP** | 8.3+ | Backend (selected products) |
| **PostgreSQL** | 16+ | Primary database |
| **Redis** | 7.0+ | Cache, queues, sessions |
| **Make** | 4.0+ | Task runner |

> Each product repository may specify additional or different prerequisites in its own `DEVELOPMENT.md`. Always check the product-level guide first.

### Recommended Tools

| Tool | Purpose |
|---|---|
| **VS Code** or **JetBrains IDE** | Code editor / IDE |
| **Postman** or **Bruno** | API testing |
| **TablePlus** or **pgAdmin** | Database GUI |
| **DBeaver** | Universal database tool |
| **Lens** | Kubernetes GUI (if applicable) |

---

## 2. Installation

### Clone the Repository

```bash
# For internal developers
git clone git@github.com:GENIUS-GROUPS-SAS/<repo>.git
cd <repo>

# For external contributors (fork first)
git clone https://github.com/<your-username>/<repo>.git
cd <repo>
git remote add upstream https://github.com/GENIUS-GROUPS-SAS/<repo>.git
```

### Environment Setup

Each product repository contains a `.env.example` file. Copy it and fill in the values:

```bash
cp .env.example .env
# Edit .env with your local configuration
```

#### Common Environment Variables

| Variable | Description | Example |
|---|---|---|
| `APP_ENV` | Application environment | `local` |
| `APP_DEBUG` | Debug mode | `true` |
| `APP_PORT` | Local server port | `3000` |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:pass@localhost:5432/db` |
| `REDIS_URL` | Redis connection string | `redis://localhost:6379` |
| `GENIUSPAY_API_KEY` | GeniusPay sandbox API key | `sk_test_...` |
| `GENIUSPAY_API_URL` | GeniusPay API base URL | `https://api-sandbox.geniuspay.africa` |
| `WEBHOOK_SECRET` | Webhook HMAC signing secret | `whsec_...` |
| `LOG_LEVEL` | Logging verbosity | `debug` |

> **Never commit `.env` files.** They are in `.gitignore` by default. If you accidentally commit one, revoke all secrets immediately.

### Install Dependencies

```bash
# Node.js projects
npm install        # or: pnpm install, yarn install

# Python projects
pip install -r requirements.txt    # or: poetry install

# PHP projects
composer install
```

### Database Setup

```bash
# Create the database
createdb <database_name>

# Run migrations
npm run db:migrate        # Node.js
python -m alembic upgrade head    # Python
php artisan migrate       # PHP

# Seed initial data (if available)
npm run db:seed
```

---

## 3. Docker

We use Docker to ensure consistent environments across development, staging, and production. Every product repository includes a `Dockerfile` and `docker-compose.yml`.

### Docker Compose

```bash
# Start all services (app, database, redis, etc.)
docker compose up -d

# View logs
docker compose logs -f

# Stop all services
docker compose down

# Rebuild after dependency changes
docker compose up -d --build
```

### Typical docker-compose.yml Services

| Service | Port | Description |
|---|---|---|
| `app` | 3000 / 8000 / 9000 | Application server |
| `postgres` | 5432 | PostgreSQL database |
| `redis` | 6379 | Redis cache/queue |
| `mailhog` / `mailpit` | 8025 | Local mail catcher (for email testing) |
| `minio` | 9001 | S3-compatible storage (for file uploads) |

### Docker Best Practices

- **Layer caching** — order `Dockerfile` instructions from least to most frequently changing.
- **Multi-stage builds** — use a builder stage and a slim runtime stage.
- **Non-root user** — the application runs as a non-root user inside the container.
- **Health checks** — every container has a `HEALTHCHECK` instruction.
- **`.dockerignore`** — exclude `node_modules`, `.git`, `vendor`, logs, and `.env`.

---

## 4. Local Development

### Starting the Development Server

```bash
# Node.js (Vite / Next.js)
npm run dev

# Python (FastAPI / Uvicorn)
uvicorn src.main:app --reload --port 8000

# PHP (Laravel / Artisan)
php artisan serve
```

### Hot Reload

All products support hot module replacement (HMR) for frontend development. The development server watches for file changes and reloads automatically.

### Database Migrations

```bash
# Create a new migration
npm run db:migrate:create -- --name=add_signatures_table

# Run migrations
npm run db:migrate

# Rollback last migration
npm run db:rollback

# Reset database (⚠️ destructive)
npm run db:reset
```

### Seeding Test Data

```bash
# Seed all
npm run db:seed

# Seed specific table
npm run db:seed -- --table=quotes
```

### Working with Webhooks Locally

For local webhook testing, use a tunneling tool:

```bash
# Using ngrok
ngrok http 3000

# Using Cloudflare Tunnel
cloudflared tunnel --url http://localhost:3000

# Using localtunnel
lt --port 3000
```

Point the webhook URL to the tunneled address:

```
https://<tunnel-url>/api/v1/webhooks/geniuspay
```

### Debugging

| Tool | Usage |
|---|---|
| **IDE Debugger** | Set breakpoints in VS Code / JetBrains. Use the built-in debugger. |
| **Logs** | `docker compose logs -f app` or check `storage/logs/` |
| **Database** | Connect with TablePlus / pgAdmin to inspect data |
| **Redis** | `redis-cli` to inspect cache and queues |
| **Network** | Browser DevTools Network tab, Postman for API calls |

---

## 5. CI/CD

### Pipeline Overview

```
Push / PR → CI Pipeline → Merge → CD Pipeline → Deploy
```

### CI Pipeline (runs on every PR)

| Stage | Description | Tools |
|---|---|---|
| **Lint** | Code style enforcement | ESLint, Prettier, Ruff, PHP CS Fixer (per stack) |
| **Static Analysis** | Type safety, code quality | TypeScript, Mypy, PHPStan (per stack) |
| **Security Scan** | Dependency vulnerabilities | npm audit, pip-audit, composer audit |
| **Unit Tests** | Business logic tests | Jest, Pytest, PHPUnit (per stack) |
| **Integration Tests** | API and database tests | Supertest, httpx, Laravel Feature Tests |
| **Build** | Compile / bundle | Vite, Webpack, esbuild, compiler |
| **E2E Tests** | Critical user journeys | Playwright, Cypress |

### CD Pipeline (runs on merge to `main`)

| Stage | Description |
|---|---|
| **Build Image** | Build production Docker image |
| **Push Image** | Push to container registry |
| **Deploy Staging** | Deploy to staging environment |
| **Smoke Tests** | Run smoke tests against staging |
| **Deploy Production** | Deploy to production (manual approval for major releases) |

### Branch Protection Rules

| Branch | Rule |
|---|---|
| `main` | Require PR, require CI pass, require 1+ review, no force push |
| `release/*` | Require PR, require CI pass, require 2+ reviews, no force push |
| `hotfix/*` | Require PR, require CI pass, require 1+ review |

### Secrets in CI

- All secrets are stored in GitHub Actions encrypted secrets.
- Secrets are never logged or echoed.
- Use `secrets.GENIUSPAY_API_KEY`, `secrets.DATABASE_URL`, etc.
- Rotate secrets quarterly.

---

## 6. Testing

### Test Commands

```bash
# Run all tests
npm test                    # Node.js
pytest                      # Python
php artisan test            # PHP

# Run with coverage
npm test -- --coverage
pytest --cov=src --cov-report=html
php artisan test --coverage

# Run specific test file
npm test -- tests/unit/signature.test.ts
pytest tests/unit/test_signature.py
php artisan test --filter=SignatureTest

# Watch mode (re-run on file change)
npm test -- --watch
pytest-watch
```

### Test Structure

```
tests/
├── unit/               # Fast, isolated, no external dependencies
│   ├── signature.test.ts
│   ├── quote.test.ts
│   └── payment.test.ts
├── integration/        # API endpoints, database, middleware
│   ├── quotes.api.test.ts
│   └── webhooks.api.test.ts
├── functional/         # Full user journeys
│   └── quote-to-payment.test.ts
└── e2e/                # Browser-based tests
    └── critical-paths.spec.ts
```

### Coverage Requirements

| Metric | Minimum |
|---|---|
| Business logic (unit) | 70% |
| API endpoints (integration) | 100% |
| Critical paths (functional/E2E) | 100% |
| Overall | 70% |

### Writing Good Tests

- **Arrange-Act-Assert** — structure every test clearly.
- **One assertion per test** — or a small group of related assertions.
- **Descriptive names** — `test_webhook_verification_rejects_invalid_hmac_signature`.
- **No shared state** — each test is independent. Use setup/teardown.
- **Test behavior, not implementation** — don't test private methods.
- **Use factories/builders** — don't hand-craft complex objects in every test.

---

## 7. Coding Standards

### General Principles

| Principle | Rule |
|---|---|
| **Functions** | < 50 lines |
| **Classes** | < 300 lines |
| **Files** | < 500 lines |
| **Cyclomatic complexity** | < 10 per function |
| **Duplication** | < 3% |
| **Dead code** | 0% |
| **Nesting depth** | < 4 levels |

### Naming Conventions

| Element | Convention | Example |
|---|---|---|
| Variables | camelCase (JS/TS) or snake_case (Python/PHP) | `quoteNumber`, `quote_number` |
| Functions / Methods | camelCase or snake_case (per language) | `createQuote()`, `create_quote()` |
| Classes / Interfaces | PascalCase | `QuoteService`, `PaymentGateway` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| Files | kebab-case | `quote-service.ts`, `payment_gateway.py` |
| Database tables | snake_case, plural | `quotes`, `invoice_items` |
| Database columns | snake_case | `client_name`, `created_at` |
| Environment variables | UPPER_SNAKE_CASE | `DATABASE_URL`, `REDIS_HOST` |

### Clean Code Rules

- **Meaningful names** — `processPayment()` not `doStuff()`.
- **Single Responsibility** — each function/class does one thing.
- **No magic numbers** — use named constants.
- **Early returns** — guard clauses at the top, not deep nesting.
- **No comments explaining *what*** — comments explain *why*.
- **No TODO without a ticket** — `// TODO(GI-123): ...` not `// TODO: fix later`.

### Language-Specific Standards

Each product may define its own linter and formatter configuration. The following are the organization-wide defaults:

| Language | Linter | Formatter | Static Analysis |
|---|---|---|---|
| **TypeScript / JavaScript** | ESLint | Prettier | TypeScript compiler |
| **Python** | Ruff | Ruff (format) | Mypy |
| **PHP** | PHP CS Fixer | PHP CS Fixer | PHPStan |
| **Go** | golangci-lint | gofmt | go vet |

### Git Standards

- **Conventional Commits** — see [CONTRIBUTING.md §4](./CONTRIBUTING.md#4-conventional-commits).
- **Branch naming** — see [CONTRIBUTING.md §3](./CONTRIBUTING.md#3-branch-naming).
- **Small commits** — one logical change per commit.
- **Rebase** — keep history clean. No merge commits in feature branches.

### Code Review Standards

- **Minimum 1 approval** required before merge.
- **All CI checks must pass.**
- **No self-merges** — even maintainers must get review from another maintainer.
- **Review within 24 hours** — don't let PRs sit idle.

---

## Getting Help

| Issue | Where to Go |
|---|---|
| Environment setup problems | [GitHub Discussions — Q&A](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) |
| Bug in the codebase | [Open an issue](https://github.com/orgs/GENIUS-GROUPS-SAS/issues) |
| Architecture question | Read [ARCHITECTURE.md](./ARCHITECTURE.md) or start a Discussion |
| CI/CD failure | Check the Actions tab in the repository |
| Security concern | [security@geniusgroups.org](mailto:security@geniusgroups.org) |

---

> **GENIUS GROUPS SAS** — Great development environments produce great code. Invest in your setup.
