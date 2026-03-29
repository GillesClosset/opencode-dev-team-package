---
name: security
description: Use this skill when reviewing auth flows, handling user input, building API endpoints, managing sessions, or working with any data that crosses a trust boundary — covers OWASP Top 10, auth patterns, and input validation.
---

# Security — OWASP Patterns, Auth, and Input Validation

## When to activate

Load this skill when:
- Reviewing or implementing authentication or authorization flows
- Handling user-supplied input (forms, query params, file uploads, headers)
- Designing or reviewing API endpoints (especially public-facing ones)
- Working with session management, cookies, or tokens
- Implementing file upload handling
- Any code path where data crosses a trust boundary (user → server, server → database, server → external service)

## Key questions before implementing

1. What trust boundary does this code cross?
2. Who can call this endpoint — anonymous, authenticated, or specific roles?
3. Is **authorization** checked (not just authentication)? Can user A access user B's data?
4. Is any user-supplied input reflected in output (XSS risk)?
5. Are secrets, credentials, or PII handled safely?

## OWASP Top 10 quick checklist

### Injection (A03)
- [ ] All database queries use parameterized queries or an ORM — never string interpolation in SQL.
- [ ] Shell commands, LDAP queries, and XML parsers are treated with the same care as SQL.
- [ ] File paths derived from user input are validated and sanitized.

### Broken Authentication (A07)
- [ ] Access tokens are short-lived (15 min–1 hr). Refresh tokens are rotated on use.
- [ ] Secure cookie flags set: `HttpOnly`, `Secure`, `SameSite=Strict` (or `Lax`).
- [ ] Password hashing uses bcrypt, scrypt, or Argon2 — never MD5, SHA-1, or plain SHA-256.
- [ ] Brute-force protection: rate limiting on login and token endpoints.

### Sensitive Data Exposure (A02)
- [ ] No secrets, credentials, or API keys in logs, error messages, or URLs.
- [ ] No PII (name, email, SSN) in URLs (use POST body or path parameters with authorization).
- [ ] Sensitive data encrypted at rest (database encryption, encrypted columns for PII).
- [ ] HTTPS enforced for all production traffic.

### IDOR — Insecure Direct Object Reference (A01)
- [ ] Every resource access checks authorization, not just authentication.
- [ ] User A cannot access User B's data by guessing an ID.
- [ ] Prefer opaque IDs (UUIDs) over sequential integers for sensitive resources.

### Security Misconfiguration (A05)
- [ ] CORS policy is explicitly set — not wildcard `*` in production.
- [ ] CSP (Content Security Policy) headers are configured.
- [ ] Directory listing is disabled on static file servers.
- [ ] Debug endpoints, admin panels, and verbose error messages are disabled in production.
- [ ] Dependency versions are pinned and scanned for known vulnerabilities (`npm audit`, `pip-audit`).

### XSS — Cross-Site Scripting (A03)
- [ ] All user-controlled values are escaped before rendering in HTML.
- [ ] Use framework-provided sanitization (React escapes by default; `dangerouslySetInnerHTML` requires explicit justification).
- [ ] CSP headers are configured as defense-in-depth.
- [ ] User-supplied markdown or HTML is sanitized with an allowlist (DOMPurify, bleach).

### CSRF — Cross-Site Request Forgery (A01)
- [ ] `SameSite=Strict` or `SameSite=Lax` cookies mitigate most CSRF risks.
- [ ] For state-changing requests from non-browser clients or older browsers: include a CSRF token.
- [ ] Verify the `Origin` or `Referer` header on state-changing requests if CSRF tokens are not used.

## Auth patterns

### JWT (JSON Web Tokens)
- **Access token:** short-lived (15 min). Contains claims. Signed with RS256 or ES256 (asymmetric).
- **Refresh token:** long-lived (days/weeks). Stored in `HttpOnly` cookie. Rotated on every use (refresh token rotation).
- **Validation:** always verify signature AND expiry. Check `iss` and `aud` claims.
- **Never store JWTs in localStorage** — use `HttpOnly` cookies. `localStorage` is accessible to JavaScript and vulnerable to XSS.

### OAuth2 / OIDC
- Use the **Authorization Code + PKCE** flow for web and mobile apps — not Implicit flow (deprecated).
- Validate the `state` parameter to prevent CSRF in the redirect.
- Validate the `id_token` signature and claims (`iss`, `aud`, `exp`, `nonce`).
- Use a well-maintained library (Auth.js, Passport.js, python-jose) — do not roll your own OAuth.

### API Key management
- **Store SHA-256 hash of the API key** — never store the plaintext key.
- Show the plaintext key only once (at creation time). If the user loses it, they must regenerate.
- Scope API keys to the minimum required permissions.
- Support key rotation without downtime (accept both old and new key briefly during rotation).

## Input validation

### Validate at the boundary
- Validate all input at the **controller or resolver level** — before it reaches business logic.
- Do not rely on database constraints as your primary validation layer.

### Schema validation
- Use a schema validation library: **Zod** (TypeScript), **Joi** (Node.js), **Pydantic** (Python), **express-validator**, etc.
- Validate type, format, length, and allowed values.
- **Reject unknown fields** (use `.strict()` in Zod, `additionalProperties: false` in JSON Schema).
- Whitelist allowed values for enum fields — do not rely on downstream checks.

### File uploads
- Validate file type by MIME type AND file extension — not just the `Content-Type` header (client-controlled).
- Scan uploaded files for malware in sensitive applications.
- Limit file size at the server level.
- Store uploaded files outside the web root or in object storage — never in a publicly served directory without access control.
- Generate a new random filename — never use the client-supplied filename directly.
