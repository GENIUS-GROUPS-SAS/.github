# Security Policy

## Supported Versions

We provide security updates for the following versions of our products. If you are running an unsupported version, please upgrade to a supported version before reporting a vulnerability.

| Product | Supported Versions | Support Status |
|---|---|---|
| **GeniusPay™** | `>= 1.0.x` | ✅ Active support |
| **GeniusPay™** | `< 1.0.0` | ❌ End of life |
| **Genius Invoice™** | `>= 1.0.x` | ✅ Active support |
| **Genius Invoice™** | `< 1.0.0` | ❌ End of life |
| **Genius Docs Sign™** | N/A | 🔵 Not yet released |
| **Genius Identity™** | N/A | 🔵 Not yet released |
| **Genius AI™** | N/A | 🔵 Not yet released |
| **Genius Work™** | N/A | 🔵 Not yet released |
| **Genius Runtime™** | N/A | 🔵 Not yet released |
| **Genius Cloud™** | N/A | 🔵 Not yet released |
| **Open-source libraries** | Latest minor of current major | ✅ Active support |
| **Open-source libraries** | Previous major | ⚠️ Critical fixes only |
| **Open-source libraries** | Two majors ago | ❌ End of life |

> Security fixes are backported to the latest minor of the current major version. We do not backport to previous major versions except in exceptional circumstances.

---

## Reporting a Vulnerability

### Do NOT Open a Public Issue

If you discover a security vulnerability in any GENIUS GROUPS product, **do not open a GitHub issue**. Public disclosure of unpatched vulnerabilities puts our users at risk.

### How to Report

Send an email to **[security@geniusgroups.org](mailto:security@geniusgroups.org)** with the following information:

1. **Product name** and version affected
2. **Severity** (Critical, High, Medium, Low) — see [CVSS](https://www.first.org/cvss/calculator/3.1) if unsure
3. **Description** of the vulnerability
4. **Reproduction steps** (detailed, step-by-step)
5. **Proof of concept** (if available — code, screenshots, or video)
6. **Impact assessment** — what could an attacker do?
7. **Suggested mitigation** (if you have one)
8. **Your contact information** for follow-up

### What to Expect

| Step | Timeline | Description |
|---|---|---|
| **Acknowledgment** | Within 24 hours | We confirm receipt of your report |
| **Initial Assessment** | Within 72 hours | We assess the severity and validity |
| **Status Update** | Every 7 days | We keep you informed of progress |
| **Fix Development** | Per severity (see below) | We develop and test a fix |
| **Patch Release** | Per severity (see below) | We release the fix |
| **Public Disclosure** | After patch release | We credit you (if desired) |

### Response Times by Severity

| Severity | Fix Target | Disclosure |
|---|---|---|
| **Critical** (CVSS 9.0-10.0) | 7 days | Coordinated, after patch |
| **High** (CVSS 7.0-8.9) | 14 days | Coordinated, after patch |
| **Medium** (CVSS 4.0-6.9) | 30 days | Coordinated, after patch |
| **Low** (CVSS 0.1-3.9) | Next release | Coordinated, after patch |

---

## Responsible Disclosure

### Our Commitment to You

- We will **acknowledge** your report within 24 hours.
- We will **investigate** every report thoroughly and in good faith.
- We will **keep you informed** of our progress.
- We will **credit you** in our security advisories (unless you prefer to remain anonymous).
- We will **not pursue legal action** against researchers who follow this policy in good faith.
- We will **coordinate disclosure** with you — we will not publish details before a fix is available.

### What We Ask of You

- **Report privately** — do not disclose the vulnerability publicly until a fix is released.
- **Provide sufficient detail** — we need enough information to reproduce and fix the issue.
- **Do not access or modify** data that does not belong to you.
- **Do not degrade** our services or disrupt other users.
- **Do not use social engineering** or physical attacks against our staff or facilities.
- **Give us reasonable time** to fix the issue before any public disclosure.

### Safe Harbor

GENIUS GROUPS SAS will not initiate legal action against any individual who:

1. Discovers and reports a vulnerability in good faith
2. Follows this responsible disclosure policy
3. Does not access, modify, or destroy data that is not their own
4. Does not disrupt or degrade our services or other users
5. Does not publicly disclose the vulnerability before a fix is released

---

## Security Measures

### What We Do

| Area | Measures |
|---|---|
| **Transport** | TLS 1.3 enforced on all endpoints. HSTS enabled. |
| **Authentication** | API keys via `X-API-Key` header. SSO via Genius Identity. MFA for admin access. |
| **Authorization** | RBAC (Role-Based Access Control) + ABAC (Attribute-Based Access Control). |
| **Data at Rest** | AES-256 encryption for sensitive data. Database-level encryption. |
| **Data in Transit** | TLS 1.3. No plaintext protocols. |
| **Webhooks** | HMAC-SHA256 signature verification. Timestamp validation. |
| **API Security** | Rate limiting. Idempotency keys. Input validation. Output encoding. |
| **Secrets Management** | Environment variables. No secrets in code. Secrets rotated regularly. |
| **Dependency Management** | Automated vulnerability scanning. Regular dependency updates. |
| **Logging** | Structured JSON logs. Security events logged with IP, timestamp, user. |
| **Monitoring** | Anomaly detection. Alerting on suspicious patterns. |
| **Compliance** | OWASP Top 10. OHADA compliance. Data protection (Côte d'Ivoire Loi 2013-450, Sénégal Loi 2008-12). |

### OWASP Top 10 Coverage

| OWASP Risk | Our Mitigation |
|---|---|
| **A01: Broken Access Control** | RBAC/ABAC enforcement on every endpoint. Server-side authorization checks. |
| **A02: Cryptographic Failures** | AES-256 at rest, TLS 1.3 in transit, HMAC-SHA256 for webhooks. |
| **A03: Injection** | Parameterized queries. ORM with prepared statements. Input validation. |
| **A04: Insecure Design** | Threat modeling. Security review in PRs. ADR for security decisions. |
| **A05: Security Misconfiguration** | Hardened Docker images. No default credentials. Security headers. |
| **A06: Vulnerable Components** | Automated dependency scanning. Regular updates. No unmaintained deps. |
| **A07: Auth Failures** | Rate limiting. MFA. Session management. Secure password policies. |
| **A08: Software & Data Integrity** | Signed webhooks. Subresource integrity. CI/CD pipeline integrity. |
| **A09: Logging & Monitoring** | Structured logging. Centralized logs. Alerting. Audit trail. |
| **A10: SSRF** | Allowlist for outbound requests. No user-controlled URLs in server-side requests. |

---

## Security Advisories

All security advisories are published on our [GitHub Security Advisories](https://github.com/GENIUS-GROUPS-SAS/.github/security/advisories) page and tagged with the appropriate CVE where applicable.

### Advisory Format

```
[SECURITY] <Product> <Version> — <Title>

Severity: <Critical|High|Medium|Low> (CVSS: <score>)
Affected: <version range>
Fixed: <version>
Reported by: <reporter (or "anonymous")>
Date: <YYYY-MM-DD>

Description:
<description of the vulnerability>

Impact:
<what an attacker could do>

Mitigation:
<how to fix or workaround>

Credits:
<reporter name, if they consented>
```

---

## Contact

| Purpose | Contact |
|---|---|
| Security reports | [security@geniusgroups.org](mailto:security@geniusgroups.org) |
| General security questions | [GitHub Discussions](https://github.com/orgs/GENIUS-GROUPS-SAS/discussions) with `security` label |
| PGP key | Available on request via email |

---

> **GENIUS GROUPS SAS** takes security seriously. If you've found a vulnerability, you're helping us protect millions of African businesses. Thank you.
