# Support

We're here to help. This guide will direct you to the right channel based on what you need.

---

## Before You Ask

1. **Search the documentation** — most questions are answered in our docs.
2. **Search existing issues** — someone may have already asked the same thing.
3. **Search discussions** — community conversations often contain solutions.
4. **Check the status page** — if something is down, we'll post it there.

---

## Support Channels

| Channel | Best For | Response Time |
|---|---|---|
| 📖 **[Documentation](https://github.com/GENIUS-GROUPS-SAS)** | API references, guides, tutorials, architecture | Self-service |
| 💬 **[GitHub Discussions](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions)** | Questions, ideas, show & tell, community | Community-driven |
| 🐛 **[GitHub Issues](https://github.com/orgs/GENIUS-GROUPS-SAS/issues)** | Bug reports, feature requests, documentation issues | 24-72 hours |
| 🔒 **[security@geniusgroups.org](mailto:security@geniusgroups.org)** | Security vulnerabilities | 24 hours |
| 📧 **[hello@geniusgroups.org](mailto:hello@geniusgroups.org)** | General inquiries, partnerships | 48 hours |
| 🏢 **Enterprise Support** | SLA-backed support for enterprise customers | Per contract |

---

## Documentation

### Where to Find It

| Type | Location |
|---|---|
| API Reference (OpenAPI) | Per-product repository — `/docs/openapi/` |
| Developer Guides | Per-product repository — `/docs/guides/` |
| Architecture Documents | [ARCHITECTURE.md](./ARCHITECTURE.md) + per-product `/docs/adr/` |
| README | Per-product repository root |
| Changelog | Per-product repository — `CHANGELOG.md` |
| Roadmap | [ROADMAP.md](./ROADMAP.md) |

### Documentation Issues

If you find incorrect, outdated, or missing documentation, open an issue using the **[Documentation Issue template](https://github.com/GENIUS-GROUPS-SAS/.github/issues/new?template=documentation.yml)**.

---

## Community

### GitHub Discussions

[GitHub Discussions](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) is our primary community space. It's organized into categories:

| Category | Purpose |
|---|---|
| **📢 Announcements** | Product updates, releases, events — read-only (maintainers only) |
| **💡 Ideas & Feature Requests** | Propose new features, discuss product direction |
| **❓ Q&A** | Ask questions, get answers from the community and maintainers |
| **🙌 Show & Tell** | Share what you've built with GENIUS GROUPS products |
| **💬 General** | Anything that doesn't fit elsewhere |

### Community Guidelines

- **Be respectful** — follow our [Code of Conduct](./CODE_OF_CONDUCT.md).
- **Be specific** — provide context, code samples, and error messages.
- **Be patient** — community members volunteer their time.
- **Give back** — if someone helped you, help someone else.

---

## Issues

### When to Open an Issue

Open an issue when:

- You've found a **reproducible bug** — use the [Bug Report template](https://github.com/GENIUS-GROUPS-SAS/.github/issues/new?template=bug_report.yml).
- You have a **feature request** — use the [Feature Request template](https://github.com/GENIUS-GROUPS-SAS/.github/issues/new?template=feature_request.yml).
- You've found a **documentation problem** — use the [Documentation Issue template](https://github.com/GENIUS-GROUPS-SAS/.github/issues/new?template=documentation.yml).
- You have a **security concern** that is safe for public discussion — use the [Security template](https://github.com/GENIUS-GROUPS-SAS/.github/issues/new?template=security.yml).

### When NOT to Open an Issue

Do **not** open an issue for:

- **Security vulnerabilities** — email [security@geniusgroups.org](mailto:security@geniusgroups.org) instead.
- **General questions** — use [GitHub Discussions](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) instead.
- **Hiring inquiries** — email [hello@geniusgroups.org](mailto:hello@geniusgroups.org).
- **Partnership proposals** — email [hello@geniusgroups.org](mailto:hello@geniusgroups.org).

### Issue Lifecycle

```
Open → triage (labeled) → in progress → review → closed
                ↓
         duplicate → closed
         wontfix → closed
         needs info → waiting → closed (if no response in 14 days)
```

---

## Enterprise Support

### For Enterprise Customers

Enterprise customers receive dedicated support with SLA-backed response times. If your organization has an enterprise agreement with GENIUS GROUPS SAS, contact your account manager or:

| Tier | Response Time | Channel |
|---|---|---|
| **Critical** (production down) | 1 hour | Dedicated phone + email |
| **High** (major feature broken) | 4 hours | Dedicated email |
| **Medium** (minor feature broken) | 24 hours | Dedicated email |
| **Low** (question, enhancement) | 72 hours | Dedicated email |

### Enterprise Support Includes

- Dedicated technical account manager
- Priority bug fixes and security patches
- Custom integration assistance
- Architecture review sessions
- Onboarding and training
- Custom SLA negotiation

> To inquire about enterprise support, email [enterprise@geniusgroups.org](mailto:enterprise@geniusgroups.org).

---

## FAQ

### I found a bug. What do I do?

1. Search existing [issues](https://github.com/orgs/GENIUS-GROUPS-SAS/issues) to check if it's already reported.
2. If not, open a new issue using the [Bug Report template](https://github.com/GENIUS-GROUPS-SAS/.github/issues/new?template=bug_report.yml).
3. Provide as much detail as possible — reproduction steps, version, environment, logs.

### I have a feature idea. What do I do?

1. Check our [Roadmap](./ROADMAP.md) — it might already be planned.
2. Search [Discussions](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) for similar ideas.
3. If it's new, open a [Discussion](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) in the **Ideas & Feature Requests** category.
4. If the idea gains traction, we'll ask you to open a formal feature request issue.

### I need help integrating an API. Where do I go?

1. Read the product's API documentation (OpenAPI spec in the repository).
2. Check the SDK documentation if one exists for your language.
3. If you're still stuck, ask in [GitHub Discussions](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) in the **Q&A** category.

### I think I found a security vulnerability. What do I do?

**Do not open a public issue.** Email [security@geniusgroups.org](mailto:security@geniusgroups.org) with full details. See our [Security Policy](./SECURITY.md) for the complete process.

### How do I report a documentation error?

Open an issue using the [Documentation Issue template](https://github.com/GENIUS-GROUPS-SAS/.github/issues/new?template=documentation.yml). Documentation issues are treated with the same priority as code bugs.

### Can I contribute code?

Yes! Read our [Contributing Guide](./CONTRIBUTING.md) to get started. We welcome contributions of all sizes.

---

## Status Page

For real-time status of GENIUS GROUPS services (API availability, incidents, maintenance windows), visit our status page:

> **[status.geniusgroups.org](https://status.geniusgroups.org)** *(coming soon)*

---

> **GENIUS GROUPS SAS** — We build infrastructure you can rely on. When something breaks, we want to know.
