---
name: security
description: Universal application security checklist — secrets handling, input validation, auth, common OWASP categories. Framework-agnostic.
estimated-tokens: 1600
used-by: [architect, engineer, reviewer, analyst]
---

# Security

## Secrets

- **Never** commit secrets, API keys, tokens, passwords, or credentials.
- Source secrets from environment variables, secret managers, or platform-provided injection — never from source files.
- `.env` is for local development only and must be `.gitignore`d.
- Rotate any secret that hits a commit, even if the commit is reverted.

## Input validation

Validate at the boundary — every external input is hostile until proven otherwise.

| Input type | Validate |
|---|---|
| User-submitted text | length, character set, encoding |
| Numbers | range, NaN, Infinity, integer-vs-float |
| Identifiers in URLs / queries | format, ownership (does this user own this resource?) |
| File uploads | size, MIME type, extension, virus scan if relevant |
| JSON / structured input | schema validation; reject extra fields by default |

## Injection — universal rules

- **SQL**: parameterized queries / prepared statements. No string concatenation. ORMs are not automatic protection — verify the underlying query.
- **Shell**: avoid `exec` / `system` with user input. If unavoidable, use the array form, never `shell=True` / string command.
- **Path**: canonicalize and check that resolved path is inside the allowed directory. Reject `..` and absolute paths from user input.
- **HTML**: contextual escaping (HTML body vs attribute vs URL vs JS context). Use the framework's auto-escape; don't bypass it.
- **NoSQL / template / LDAP / log injection**: same principle — never interpolate untrusted data into a query/template/log directly.

## Authentication

- Hash passwords with a slow KDF (Argon2id, bcrypt, scrypt). Never plain SHA-* / MD5.
- Use platform / framework session management; don't roll your own.
- Set cookies with `Secure`, `HttpOnly`, and `SameSite` attributes appropriately.
- Token TTLs short by default; refresh-token rotation when possible.
- Lockout / rate-limit on auth endpoints.

## Authorization

- Enforce on every state-changing endpoint, not just at the gateway.
- "Vertical" checks (role / permission) AND "horizontal" checks (does this user own this resource?). Most data leaks are missing horizontal checks.
- Default-deny: explicit allow per endpoint.

## Transport

- HTTPS everywhere; HSTS in production.
- Pin TLS versions to ≥ 1.2 (prefer 1.3).
- Don't trust `X-Forwarded-*` headers unless behind a known proxy.

## Data handling

- Encrypt sensitive data at rest (DB-level or column-level depending on threat model).
- PII / health data / payment data: minimize collection; keep retention bounded.
- Logs: never log full credentials, tokens, or sensitive PII. Redact at the logger level, not at the call site.

## CSRF / CORS

- CSRF: prefer SameSite cookies + same-origin checks. Add tokens for any state-changing endpoint accepting cookies.
- CORS: explicit allowlist of origins. Never reflect arbitrary `Origin` headers. `Access-Control-Allow-Credentials: true` requires concrete origin, never `*`.

## Common OWASP categories — quick sanity

| Category | Check |
|---|---|
| Broken access control | Horizontal authz on every resource access |
| Cryptographic failures | No DIY crypto; correct primitives; rotate keys |
| Injection | Parameterize everything |
| Insecure design | Threat-model new features |
| Security misconfiguration | Default-deny; minimal surface; dependencies pinned |
| Vulnerable components | Dependency scanner in CI; patch promptly |
| Identification & auth | Strong password hashing, MFA-ready, lockout |
| Software & data integrity | Verify package signatures; lockfile committed |
| Logging & monitoring | Audit log on auth + state changes; no sensitive data in logs |
| SSRF | Allowlist outbound destinations from server-side fetchers |

## Anti-patterns

- "We'll add authz later" — later never comes.
- Custom crypto routines.
- Trusting client-side validation alone.
- Stack traces exposed to end users.
- Symmetric secrets shared across environments.
