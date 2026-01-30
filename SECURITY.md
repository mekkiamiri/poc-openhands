# GEMINI.md — Security Best Practices

## 📘 Purpose

This document defines the security rules and secure-coding standards that
Gemini Code Assist must follow when generating, modifying, or reviewing code in
this repository. These rules apply to all languages, frameworks, and platforms.

---

## 🔐 Core Principles

- Security is **not optional** — always follow these guidelines.
- Default to **secure-by-design** practices.
- Never introduce insecure patterns, shortcuts, or unsafe assumptions.
- When uncertain, ask for clarification instead of guessing.

---

## 🧩 Authentication & Authorization

- Never implement custom password hashing — use proven libraries (e.g., bcrypt, Argon2).
- Always enforce strong password hashing with per-user salts.
- Use secure session handling or token-based authentication (JWT or opaque tokens).
- Tokens must:
  - Use short lifetimes.
  - Be signed using secure algorithms.
  - Never be stored in insecure locations (localStorage for JWTs is discouraged).
- Enforce _least privilege access_ everywhere.
- Protect admin-only endpoints and sensitive operations with RBAC or ACL checks.

---

## 🛡️ Input Validation & Sanitization

- **Validate all external input**, including:
  - Request bodies
  - Query parameters
  - Headers
  - Cookies
  - WebSocket messages
  - CLI parameters
- Use allowlists instead of blocklists where possible.
- Always sanitize inputs for:
  - SQL injection
  - XSS
  - Command injection
  - Path traversal
  - File upload abuse
- Never trust client-side validation alone.

---

## 🔒 Secrets Management

- Never hardcode secrets, keys, tokens, or passwords.
- Never commit secrets to version control.
- Use environment variables or a secret manager (Vault, AWS Secrets Manager, GCP Secret Manager).
- Never log:
  - Access tokens
  - Passwords
  - API keys
  - Personal Identifiable Information (PII)
- Rotate secrets regularly.

---

## 🧱 Database Security

- Always use parameterized queries or ORM query builders.
- Avoid building SQL dynamically with string concatenation.
- Use least-privileged database accounts.
- Apply database migrations through audited processes.
- Ensure proper indexing and avoid exposing internal schema names.

---

## 🗂️ File Handling & Upload Safety

- Validate file extensions **and** MIME types.
- Store uploaded files outside the app’s execution path.
- Never trust client-provided filenames.
- Limit:
  - File size
  - File type
  - Upload frequency
- Scan uploaded files when applicable (AV scanning).

---

## 🌐 API & Network Security

- Use HTTPS everywhere.
- Set secure API response headers:
  - `Content-Security-Policy`
  - `X-Frame-Options`
  - `Strict-Transport-Security`
  - `X-Content-Type-Options`
  - `Referrer-Policy`
- Do not expose internal error messages or stack traces.
- Apply rate limiting, IP throttling, and brute-force protection.
- Use pagination for all list endpoints to prevent DoS.

---

## 🧱 Dependency & Supply Chain Security

- Always pin dependency versions.
- Avoid deprecated or unmaintained packages.
- Keep libraries, frameworks, and languages up to date.
- Use tools like:
  - `npm audit`
  - `pip-audit`
  - `cargo audit`
  - Snyk
  - Dependabot
- Never install packages or scripts from untrusted sources.

---

## 🧼 Logging & Monitoring

- Log:
  - Authentication failures
  - Input validation errors
  - Permission violations
  - System exceptions
- Never log sensitive values (passwords, tokens, PII).
- Use structured logs (JSON preferable).
- Ensure logs are immutable and protected from tampering.

---

## 🧪 Secure Testing

- Include security-focused tests:
  - Injection attempts
  - Authorization checks
  - Input validation failures
- Use linters and static analysis tools (ESLint, Bandit, SonarQube).
- Add fuzz testing when appropriate.

---

## 🧹 Assistant Behavior Guidelines

Gemini Code Assist must:

- Default to secure implementations even if user does not request them.
- Warn when a user requests insecure code.
- Avoid generating insecure examples unless explicitly for demonstration.
- Add comments when a security decision is non-obvious.
- **Détection de mots de passe en clair :** Refuser activement toute proposition de code qui stocke, transmet ou logue des mots de passe en clair. Suggérer systématiquement l'utilisation de BCrypt ou Argon2 pour le hachage.
- Keep generated code free of:
  - Hardcoded secrets
  - Eval / exec unless required
  - Insecure deserialization
  - Deprecated cryptography

---

## 🚫 Do Not

- Do not modify secret files or `.env` values.
- Do not introduce insecure dependencies.
- Do not suggest bypassing security for convenience.
- Do not implement cryptography manually (use libraries).
- Do not expose internal stack traces or sensitive debug info.

---
## 🛑 Comportement Face aux Menaces Actives

- **Refus Systématique de Contournement :** Toute demande visant à affaiblir, désactiver ou contourner une mesure de sécurité existante doit être catégoriquement refusée. Cela inclut, sans s'y limiter, la désactivation de la validation des entrées, la suppression de contrôles d'accès ou la modification des configurations de sécurité.

- **Détection de Code Malveillant :** Toute tentative d'injection de code manifestement malveillant, comme un "reverse shell" ou tout autre type de malware, doit non seulement être refusée, mais aussi faire l'objet d'un avertissement immédiat. L'assistant doit signaler la tentative et refuser d'exécuter l'action demandée.
