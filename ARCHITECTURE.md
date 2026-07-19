# Architecture Standards

This document defines the architectural standards that govern all GENIUS GROUPS SAS products. These are not suggestions — they are the baseline. Deviations require an approved ADR.

---

## Table of Contents

1. [Core Principles](#1-core-principles)
2. [Clean Architecture](#2-clean-architecture)
3. [Domain-Driven Design (DDD)](#3-domain-driven-design-ddd)
4. [Hexagonal Architecture](#4-hexagonal-architecture)
5. [Event-Driven Architecture](#5-event-driven-architecture)
6. [API First](#6-api-first)
7. [REST](#7-rest)
8. [GraphQL](#8-graphql)
9. [Webhooks](#9-webhooks)
10. [Microservices](#10-microservices)
11. [Modular Monolith](#11-modular-monolith)
12. [Cross-Cutting Concerns](#12-cross-cutting-concerns)

---

## 1. Core Principles

| Principle | Summary |
|---|---|
| **Dependencies point inward** | The domain depends on nothing. Infrastructure depends on the domain. |
| **API First** | The API is designed before the UI. OpenAPI spec before code. |
| **Documentation First** | Nothing is built without documentation. |
| **Security First** | OWASP Top 10 is the baseline. Security is designed in, not bolted on. |
| **Idempotency** | Every mutating operation is idempotent. |
| **Observability** | Every service emits structured logs, metrics, and traces. |
| **Bounded Contexts** | Each domain has clear boundaries. No cross-context coupling. |
| **Eventual Consistency where appropriate** | Strong consistency within an aggregate, eventual consistency across. |

---

## 2. Clean Architecture

Clean Architecture (Robert C. Martin) is our default architectural pattern. It organizes code into concentric layers where dependencies always point inward.

```
┌───────────────────────────────────────────────────────┐
│                   Presentation                        │
│              (Controllers, Views, DTOs)               │
└───────────────────────┬───────────────────────────────┘
                        │ depends on
┌───────────────────────▼───────────────────────────────┐
│                   Application                         │
│           (Use Cases, Services, Orchestration)        │
└───────────────────────┬───────────────────────────────┘
                        │ depends on
┌───────────────────────▼───────────────────────────────┐
│                    Domain                             │
│         (Entities, Value Objects, Domain Events)      │
└───────────────────────┬───────────────────────────────┘
                        │ implemented by
┌───────────────────────▼───────────────────────────────┐
│                 Infrastructure                        │
│        (Database, External APIs, Storage, Queue)      │
└───────────────────────────────────────────────────────┘
```

### Layer Responsibilities

| Layer | Contains | Depends On |
|---|---|---|
| **Presentation** | HTTP controllers, input validation, response formatting, views | Application |
| **Application** | Use cases, application services, DTOs, orchestration | Domain |
| **Domain** | Entities, value objects, domain events, domain services, repository interfaces | Nothing |
| **Infrastructure** | Database implementations, external API clients, file storage, message queues | Application + Domain (implements interfaces) |

### Rules

1. **The Domain layer has zero external dependencies** — no framework, no ORM, no HTTP library.
2. **The Application layer depends on the Domain** but never on Infrastructure.
3. **Infrastructure implements interfaces defined in the Domain or Application** — dependency inversion.
4. **The Presentation layer is thin** — it delegates to application services. No business logic.
5. **Cross-cutting concerns** (logging, security, validation) are handled via middleware or decorators, not scattered in business logic.

### Directory Structure (typical)

```
src/
├── presentation/
│   ├── controllers/
│   ├── middleware/
│   └── dto/
├── application/
│   ├── services/
│   ├── use-cases/
│   └── handlers/
├── domain/
│   ├── entities/
│   ├── value-objects/
│   ├── events/
│   ├── services/
│   └── repositories/        (interfaces only)
└── infrastructure/
    ├── persistence/
    ├── external-apis/
    ├── storage/
    └── queue/
```

---

## 3. Domain-Driven Design (DDD)

DDD (Eric Evans) is our approach to modeling complex domains. We use tactical and strategic DDD patterns.

### Strategic DDD

| Pattern | Description |
|---|---|
| **Bounded Context** | A clear boundary within which a domain model is valid. Each context has its own ubiquitous language. |
| **Ubiquitous Language** | A shared vocabulary between developers and domain experts. Used in code, docs, and conversations. |
| **Context Map** | A diagram showing relationships between bounded contexts (upstream/downstream, anti-corruption layers, shared kernels). |

### Bounded Contexts (GeniusPay example)

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Payments    │     │   Wallets    │     │   Escrow     │
│              │     │              │     │              │
│ Transaction  │     │ Wallet       │     │ Escrow       │
│ PaymentMethod│     │ Balance      │     │ ReleaseRule  │
│ Refund       │     │ Transfer     │     │ Dispute      │
└──────┬───────┘     └──────┬───────┘     └──────┬───────┘
       │                    │                    │
       │  Domain Events     │                    │
       └────────┬───────────┘────────────────────┘
                │
        ┌───────▼───────┐
        │  Subscriptions │
        │               │
        │ Plan          │
        │ Subscription  │
        │ BillingCycle  │
        └───────────────┘
```

### Tactical DDD

| Pattern | Description |
|---|---|
| **Entity** | An object with identity that persists over time (e.g., `Quote`, `Invoice`). |
| **Value Object** | An immutable object defined by its attributes, no identity (e.g., `Money`, `Address`). |
| **Aggregate** | A cluster of entities and value objects treated as a single unit. Has a root entity. |
| **Aggregate Root** | The entry point to an aggregate. All access goes through the root. |
| **Domain Event** | Something that happened in the domain (e.g., `QuoteAccepted`, `PaymentReceived`). |
| **Repository** | An interface for retrieving and persisting aggregates. Implemented in Infrastructure. |
| **Domain Service** | Logic that doesn't naturally belong to an entity or value object. |
| **Factory** | Creates complex aggregates. |

### Aggregate Rules

1. **One transaction = one aggregate.** Never modify two aggregates in the same transaction.
2. **Reference other aggregates by ID**, not by object reference.
3. **All modifications go through the aggregate root.**
4. **The aggregate root enforces invariants.**

### Domain Events

```text
QuoteAccepted
├── quote_id: UUID
├── workspace_id: UUID
├── client_name: string
├── total_ttc: Money
├── accepted_at: DateTime
└── signature_id: UUID

PaymentReceived
├── invoice_id: UUID
├── amount: Money
├── method: PaymentMethod
├── transaction_id: string
└── paid_at: DateTime
```

---

## 4. Hexagonal Architecture

Hexagonal Architecture (Ports and Adapters, Alistair Cockburn) is an alternative to Clean Architecture that we use for products with many integration points.

```
                    ┌─────────────────┐
                    │     Domain      │
                    │    (Core Logic)  │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
         ┌────▼────┐    ┌────▼────┐    ┌────▼────┐
         │  Port   │    │  Port   │    │  Port   │
         │ (driving)│   │ (driven) │   │ (driven) │
         └────┬────┘    └────┬────┘    └────┬────┘
              │              │              │
         ┌────▼────┐    ┌────▼────┐    ┌────▼────┐
         │ Adapter │    │ Adapter │    │ Adapter │
         │  HTTP   │    │   DB    │    │  Email  │
         └─────────┘    └─────────┘    └─────────┘
```

### Ports

- **Driving ports** (primary) — how the outside world interacts with the application (e.g., `QuoteServiceInterface`).
- **Driven ports** (secondary) — how the application interacts with the outside world (e.g., `PaymentGatewayInterface`, `QuoteRepositoryInterface`).

### Adapters

- **Driving adapters** — HTTP controllers, CLI commands, GraphQL resolvers, queue consumers.
- **Driven adapters** — database repositories, external API clients, email senders, file storage.

### When to Use Hexagonal

- Multiple entry points (HTTP, CLI, gRPC, queue)
- Multiple integration points (payment gateways, storage providers, email services)
- Need to swap implementations (e.g., test vs. production payment gateway)

---

## 5. Event-Driven Architecture

We use event-driven patterns for decoupling bounded contexts and enabling asynchronous processing.

### Event Flow

```
Quote Accepted (aggregate) → Domain Event → Event Bus → Event Handlers
                                                         ├── Generate Invoice
                                                         ├── Send Notification
                                                         ├── Update Dashboard
                                                         └── Trigger Webhook
```

### Patterns

| Pattern | Usage |
|---|---|
| **Domain Events** | In-process events within an aggregate's transaction. |
| **Integration Events** | Cross-context events published to a message bus. |
| **Event Sourcing** | Store events as the source of truth. Used for audit-critical aggregates (payments, signatures). |
| **CQRS** | Separate read models from write models. Used when read/write patterns diverge significantly. |
| **Saga** | Long-running transactions across services. Used for payment + escrow + delivery flows. |

### Event Design Rules

1. **Events are immutable** — once published, they cannot be changed.
2. **Events are named in past tense** — `QuoteAccepted`, not `AcceptQuote`.
3. **Events carry enough data** — consumers shouldn't need to query the source.
4. **Events are versioned** — `QuoteAccepted.v1`, `QuoteAccepted.v2` with schema evolution.
5. **Events are idempotent** — consumers can safely process the same event multiple times.

---

## 6. API First

API First means the API is designed, documented, and reviewed **before** any implementation begins.

### Process

```
1. Define endpoints (OpenAPI) → 2. Review with stakeholders → 3. Generate mocks
→ 4. Frontend builds against mocks → 5. Backend implements → 6. Integration
```

### OpenAPI Requirements

- Every API is described in OpenAPI 3.1.
- The spec lives in the repository under `/docs/openapi/`.
- The spec is versioned with the code.
- Swagger UI is auto-generated from the spec.
- The spec includes: schemas, examples, error responses, authentication, pagination, idempotency.

---

## 7. REST

REST is our default API style. All public APIs follow these conventions:

### URL Structure

```
Base URL: https://api.geniusgroups.org/api/v1

Resources (plural, kebab-case):
GET    /api/v1/quotes              — List quotes
POST   /api/v1/quotes              — Create quote
GET    /api/v1/quotes/{id}         — Get quote
PUT    /api/v1/quotes/{id}         — Update quote
PATCH  /api/v1/quotes/{id}         — Partial update
DELETE /api/v1/quotes/{id}         — Delete quote
POST   /api/v1/quotes/{id}/accept  — Action (accept quote)
POST   /api/v1/quotes/{id}/sign    — Action (sign quote)
```

### Conventions

| Convention | Rule |
|---|---|
| **Versioning** | URL-based (`/api/v1/`, `/api/v2/`) |
| **Authentication** | `X-API-Key` header |
| **Idempotency** | `Idempotency-Key` header on POST/PUT |
| **Pagination** | `?page=1&per_page=20` → response includes `meta` with pagination info |
| **Filtering** | `?status=published&client_name=Boutique` |
| **Sorting** | `?sort=-created_at` (descending) |
| **Field selection** | `?fields=id,number,status,total_ttc` |
| **Error format** | RFC 7807 Problem Details |
| **Content type** | `application/json` (request + response) |
| **Status codes** | Standard HTTP status codes (200, 201, 204, 400, 401, 403, 404, 409, 422, 429, 500) |

### Error Response Format

```json
{
  "type": "https://docs.geniusgroups.org/errors/validation",
  "title": "Validation Failed",
  "status": 422,
  "detail": "The client_name field is required.",
  "instance": "/api/v1/quotes",
  "errors": [
    {
      "field": "client_name",
      "message": "The client_name field is required.",
      "code": "required"
    }
  ]
}
```

---

## 8. GraphQL

GraphQL is used alongside REST for products that benefit from flexible queries (e.g., dashboards, analytics).

### When to Use GraphQL

- Multiple consumers with different data needs
- Complex nested queries (e.g., "give me all quotes for this workspace with their items, signatures, and payments")
- Frontend teams that want to avoid over-fetching

### When NOT to Use GraphQL

- Simple CRUD APIs
- Public APIs for third-party integrations (REST is more predictable)
- Webhook payloads (use REST + JSON)

### Standards

- Use **federation** for multi-service schemas
- **Versioning** via deprecation of fields/types, not URL versioning
- **Authentication** via `Authorization: Bearer <token>` header
- **Rate limiting** via query complexity analysis
- **Persisted queries** for production clients

---

## 9. Webhooks

Webhooks are how we notify external systems of events in real time.

### Standards

| Aspect | Standard |
|---|---|
| **Signing** | HMAC-SHA256 |
| **Signature header** | `X-GeniusGroups-Signature: sha256=<hex>` |
| **Event header** | `X-GeniusGroups-Event: quote.accepted` |
| **Delivery header** | `X-GeniusGroups-Delivery: <uuid>` |
| **Content type** | `application/json` |
| **Method** | `POST` |
| **Retry** | Exponential backoff: 1m, 5m, 30m, 2h, 6h, 24h (max 6 attempts) |
| **Timeout** | 30 seconds |
| **Idempotency** | Delivery UUID included; consumers must handle duplicates |

### Webhook Payload

```json
{
  "id": "evt_abc123",
  "type": "quote.accepted",
  "created_at": "2026-07-19T12:00:00Z",
  "data": {
    "object": {
      "id": "qte_xyz789",
      "number": "DEV-2026-00042",
      "status": "accepted",
      "total_ttc": 749300,
      "currency": "XOF"
    }
  },
  "delivery": "dlv_def456"
}
```

### Signature Verification

```
computed_signature = HMAC-SHA256(webhook_secret, raw_request_body)
expected_signature = header["X-GeniusGroups-Signature"]

if not constant_time_compare(computed_signature, expected_signature):
    reject
```

---

## 10. Microservices

We do **not** default to microservices. We start with a modular monolith and extract services only when justified by an ADR.

### When to Extract a Microservice

| Trigger | Example |
|---|---|
| **Independent scaling** | Payment processing needs 10x the capacity of invoicing |
| **Independent deployment** | A team needs to deploy their service without coordinating |
| **Technology divergence** | A service needs a different runtime (e.g., AI service in Python, rest in TypeScript) |
| **Team boundaries** | Conway's Law — the team structure demands it |
| **Fault isolation** | A service needs to fail without taking down the rest |

### Microservice Standards

| Aspect | Standard |
|---|---|
| **Communication** | REST (external), gRPC (internal), events (async) |
| **Service discovery** | DNS or service mesh |
| **Data ownership** | Each service owns its database. No shared databases. |
| **API contract** | OpenAPI for REST, Protocol Buffers for gRPC |
| **Observability** | Distributed tracing (correlation IDs), structured logs, metrics |
| **Deployment** | Independent CI/CD pipelines. Blue/green or canary. |
| **Failure** | Circuit breakers, bulkheads, graceful degradation |

---

## 11. Modular Monolith

The modular monolith is our **default** deployment architecture. It's a single deployable unit with clear internal module boundaries.

```
┌─────────────────────────────────────────────────────┐
│                  Genius Invoice™                     │
│                                                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐          │
│  │  Quotes   │  │ Signature│  │ Invoices │          │
│  │  Module   │  │  Module  │  │  Module  │          │
│  │           │  │          │  │          │          │
│  │ entities  │  │ entities │  │ entities │          │
│  │ services  │  │ services │  │ services │          │
│  │ repos     │  │ repos    │  │ repos    │          │
│  └─────┬─────┘  └────┬─────┘  └────┬─────┘          │
│        │             │             │                 │
│        └─────────────┼─────────────┘                 │
│                      │                                │
│              ┌───────▼───────┐                       │
│              │  Payments     │                       │
│              │  Module       │                       │
│              │               │                       │
│              │ GeniusPay API │                       │
│              └───────────────┘                       │
│                                                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐          │
│  │ Dashboard │  │  Notif   │  │  Webhook │          │
│  │  Module   │  │  Module  │  │  Module  │          │
│  └──────────┘  └──────────┘  └──────────┘          │
└─────────────────────────────────────────────────────┘
```

### Module Rules

1. **Each module has its own directory, entities, services, and repositories.**
2. **Modules communicate via interfaces or domain events — never direct database access.**
3. **No module imports another module's internal classes.**
4. **A module can be extracted to a microservice with minimal refactoring** (because boundaries are already clean).

---

## 12. Cross-Cutting Concerns

### Logging

| Aspect | Standard |
|---|---|
| **Format** | Structured JSON |
| **Fields** | `timestamp`, `level`, `message`, `context`, `request_id`, `user_id` |
| **Levels** | `debug`, `info`, `warn`, `error`, `fatal` |
| **Storage** | Centralized (ELK / Loki / CloudWatch) |
| **Retention** | 90 days (info+), 1 year (error+) |
| **PII** | Never log PII (emails, phone numbers, API keys) |

### Observability

| Pillar | Tool (example) |
|---|---|
| **Metrics** | Prometheus + Grafana |
| **Logs** | Loki / ELK |
| **Traces** | OpenTelemetry + Jaeger |
| **Alerting** | Alertmanager → Slack / PagerDuty |

### Security

| Aspect | Standard |
|---|---|
| **Transport** | TLS 1.3 |
| **Secrets** | Environment variables / secrets manager |
| **API keys** | Hashed at rest, never logged |
| **Input validation** | Allowlist, server-side, on every endpoint |
| **Output encoding** | Context-aware (HTML, JSON, URL) |
| **Headers** | `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Strict-Transport-Security` |
| **CORS** | Explicit allowlist, no `*` |
| **Rate limiting** | Per API key, per IP, per endpoint |

### Multi-tenancy

| Aspect | Standard |
|---|---|
| **Isolation** | Row-level isolation with `workspace_id` |
| **Data access** | Every query filtered by `workspace_id` |
| **API** | `workspace_id` derived from API key, never user-supplied |

---

> **GENIUS GROUPS SAS** — Architecture is not about patterns. It's about boundaries. Draw them well, and everything else follows.
