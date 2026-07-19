# Governance

This document describes how GENIUS GROUPS SAS governs its open-source and internal projects. It defines roles, responsibilities, decision-making processes, and the RFC/ADR lifecycle.

---

## Table of Contents

1. [Leadership Structure](#1-leadership-structure)
2. [Roles & Responsibilities](#2-roles--responsibilities)
3. [Maintainers](#3-maintainers)
4. [Core Team](#4-core-team)
5. [Decision Process](#5-decision-process)
6. [RFC Process](#6-rfc-process)
7. [ADR Process](#7-adr-process)
8. [Conflict Resolution](#8-conflict-resolution)

---

## 1. Leadership Structure

```
                    ┌─────────────────────┐
                    │   Chief Executive    │
                    │      Officer         │
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │  Chief Technology    │
                    │      Officer         │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
    ┌─────────▼──────┐ ┌──────▼───────┐ ┌──────▼───────┐
    │  Product Lead   │ │  Engineering │ │  Security    │
    │                 │ │    Lead      │ │    Lead      │
    └─────────┬──────┘ └──────┬───────┘ └──────┬───────┘
              │                │                │
    ┌─────────▼────────────────▼────────────────▼───────┐
    │                  Core Team                         │
    │          (Product Maintainers + Contributors)      │
    └────────────────────────────────────────────────────┘
```

### Executive Sponsors

| Role | Responsibility |
|---|---|
| **CEO** | Strategic direction, business alignment, external partnerships |
| **CTO** | Technical vision, architecture standards, engineering culture |
| **Product Lead** | Product roadmap, prioritization, user research |
| **Engineering Lead** | Engineering execution, code quality, release management |
| **Security Lead** | Security policy, vulnerability response, compliance |

---

## 2. Roles & Responsibilities

| Role | Who | Permissions | Responsibilities |
|---|---|---|---|
| **Contributor** | Anyone | Open issues, PRs, discussions | Follow guidelines, write tests, document changes |
| **Triager** | Community members with `triage` permission | Label issues, close duplicates, manage stale issues | Triage within 48 hours, route to maintainers |
| **Maintainer** | Core team members assigned to a product | Review PRs, merge to `main`, cut releases | Ensure quality, enforce standards, mentor contributors |
| **Lead Maintainer** | One per product | Everything a maintainer can do + set product direction | Roadmap, release scheduling, final arbitration |
| **Core Team** | Senior engineers across products | Cross-product decisions, RFC approval | Architecture standards, cross-cutting concerns |
| **Security Lead** | Designated security officer | Security advisories, CVE management | Vulnerability response, security audits |

---

## 3. Maintainers

### Becoming a Maintainer

Maintainers are selected by the **Lead Maintainer** and **CTO** based on:

- **Sustained contribution** — consistent, high-quality PRs over 3+ months
- **Domain expertise** — deep understanding of the product's codebase and architecture
- **Code review quality** — thorough, constructive, timely reviews
- **Community engagement** — helpful in discussions, mentors newcomers
- **Alignment** — understands and embodies GENIUS GROUPS engineering values

### Maintainer Responsibilities

1. **Review PRs** within 24 hours of request.
2. **Merge approved PRs** — ensure CI passes, squash-merge, delete branch.
3. **Triage issues** — label, prioritize, assign, or close.
4. **Cut releases** — follow the [Release Process](./RELEASE.md).
5. **Enforce standards** — code quality, architecture, security, testing.
6. **Mentor contributors** — guide newcomers, provide constructive feedback.
7. **Write ADRs** — document significant decisions.
8. **Participate in RFCs** — review and comment on proposals.

### Stepping Down

A maintainer may step down at any time by notifying the Lead Maintainer. We ask for a 2-week notice to transition responsibilities.

### Removal

A maintainer may be removed for:

- Violations of the [Code of Conduct](./CODE_OF_CONDUCT.md)
- Prolonged inactivity (60+ days without engagement)
- Repeated failure to uphold engineering standards

Removal decisions are made by the Lead Maintainer and CTO, and are communicated privately first.

---

## 4. Core Team

The Core Team is the cross-product governing body responsible for organization-wide standards and decisions.

### Composition

| Seat | Representation |
|---|---|
| CTO (chair) | Engineering vision |
| Product Lead | Product strategy |
| Security Lead | Security and compliance |
| Lead Maintainer — GeniusPay™ | Payment infrastructure |
| Lead Maintainer — Genius Invoice™ | Invoicing and signature |
| Lead Maintainer — Genius Identity™ | Identity and access |
| Lead Maintainer — Genius AI™ | AI and data |
| At-large member (rotating) | Community voice |

### Core Team Responsibilities

- Approve or reject RFCs
- Approve cross-product architectural changes
- Maintain [ARCHITECTURE.md](./ARCHITECTURE.md) standards
- Resolve cross-product conflicts
- Approve new product incubation
- Approve major version releases
- Review and update governance documents

### Meeting Cadence

- **Weekly sync** — 30 minutes, status and blockers
- **Monthly review** — 60 minutes, RFCs, roadmap, metrics
- **Quarterly planning** — 2 hours, strategic direction

---

## 5. Decision Process

### Decision Types

| Type | Made By | Process |
|---|---|---|
| **Code-level** (implementation, refactoring) | Maintainer | PR review and merge |
| **Product-level** (features, UX) | Product Lead + Lead Maintainer | Issue → Discussion → PR |
| **Architecture-level** (patterns, frameworks) | Core Team | RFC → ADR → Implementation |
| **Strategic** (new products, partnerships) | CEO + CTO | Executive decision |
| **Security** (vulnerabilities, policies) | Security Lead | Security Policy → Advisory |

### Decision Framework

Every significant decision should answer:

1. **What** are we deciding?
2. **Why** are we deciding this now? (context, forces)
3. **Who** is affected?
4. **What** are the alternatives?
5. **What** are the consequences (positive and negative)?
6. **How** do we measure success?

### Consensus vs. Authority

- **Default: consensus** — the Core Team discusses until agreement is reached.
- **Fallback: authority** — if consensus cannot be reached within 2 weeks, the CTO makes the final call.
- **Override: executive** — the CEO can override any decision for strategic reasons, with written justification.

---

## 6. RFC Process

An RFC (Request for Comments) is the process for proposing significant changes that affect multiple products, teams, or the organization as a whole.

### When to Write an RFC

Write an RFC when you propose:

- A new architectural pattern or standard
- A cross-product API change
- A new product or major feature
- A change to the development process
- A change to governance

### RFC Format

```markdown
# RFC-NNN: Title

## Status
Draft | Under Review | Accepted | Rejected | Implemented

## Author(s)
Name(s) and role(s)

## Date
YYYY-MM-DD

## Summary
One paragraph summary of the proposal.

## Motivation
Why is this needed? What problem does it solve?

## Detailed Design
The full proposal. Include diagrams, API schemas, data models, etc.

## Alternatives Considered
What other approaches were considered and why were they rejected?

## Impact
- Affected products:
- Breaking changes:
- Migration plan:
- Testing plan:

## Open Questions
List any unresolved questions for discussion.

## References
Links to related RFCs, ADRs, issues, discussions.
```

### RFC Lifecycle

```
Draft → Submitted (PR to /docs/rfcs/) → Under Review (2 weeks)
    → Accepted (Core Team approves) → Implemented
    → Rejected (Core Team rejects, with reasoning)
```

### RFC Rules

- RFCs are submitted as PRs to the `/docs/rfcs/` directory in the relevant repository.
- RFCs are numbered sequentially: `RFC-001`, `RFC-002`, etc.
- RFCs must be open for review for **at least 2 weeks** before a decision.
- Anyone can comment on an RFC — it's not limited to the Core Team.
- The Core Team makes the final accept/reject decision.
- Accepted RFCs are linked to one or more ADRs for implementation.

---

## 7. ADR Process

An ADR (Architecture Decision Record) captures a single architectural decision. See [CONTRIBUTING.md §9](./CONTRIBUTING.md#9-architecture-decision-records-adr) for the full ADR format and lifecycle.

### Relationship Between RFCs and ADRs

| RFC | ADR |
|---|---|
| Proposes a significant change | Records a specific decision |
| Organization-wide impact | Product or component-level impact |
| Requires Core Team approval | Requires Lead Maintainer approval |
| May result in multiple ADRs | May be created without an RFC |

### ADR Repository

ADRs live in each product's repository under `/docs/adr/`:

```
docs/
└── adr/
    ├── ADR-001-choice-of-database.md
    ├── ADR-002-api-versioning-strategy.md
    ├── ADR-003-webhook-signature-verification.md
    └── ...
```

---

## 8. Conflict Resolution

### Technical Disagreements

1. **Discuss** — the parties discuss in the PR or issue comments.
2. **Escalate** — if unresolved, the Lead Maintainer mediates.
3. **Core Team** — if still unresolved, the Core Team decides.
4. **CTO** — final technical authority.

### Behavioral Conflicts

Behavioral conflicts are handled under the [Code of Conduct](./CODE_OF_CONDUCT.md) enforcement process. Report to [conduct@geniusgroups.org](mailto:conduct@geniusgroups.org).

### Product vs. Engineering Conflicts

When product priorities conflict with engineering standards:

1. The **Product Lead** and **Engineering Lead** discuss trade-offs.
2. If unresolved, the **CTO** arbitrates.
3. Engineering standards (security, architecture, quality) are **never** compromised without CTO approval.

---

## Changes to This Document

Changes to this governance document require:

1. An RFC submitted as a PR
2. Core Team review (minimum 2 weeks)
3. Core Team majority approval
4. CTO sign-off

---

> **GENIUS GROUPS SAS** — Governance is not bureaucracy. It's the structure that enables fast, safe, and aligned decisions.
