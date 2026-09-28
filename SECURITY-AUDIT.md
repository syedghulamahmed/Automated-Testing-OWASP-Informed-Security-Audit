# SECURITY-AUDIT.md — TalentBridge Week 5

## Scope and methodology

The review covers authentication, authorization, uploads, search/filter query construction, Express configuration, Prisma access, frontend request behavior, dependency management, and test isolation. Threat actors considered are anonymous attackers, credential-stuffing operators, authenticated users attempting horizontal privilege escalation, malicious uploaders, and attackers sending malformed input.

The assignment asks for OWASP Top 10:2021. OWASP currently lists Top 10:2025 as the latest released version, so this report preserves the required 2021 taxonomy and separately records the 2025 change. citeturn0search2turn0search12

## OWASP Top 10:2021 mapping

| Category | Finding | Fix / verification |
|---|---|---|
| A01 Broken Access Control | Profile upload endpoints accept a profile ID and require an IDOR defense. | Authentication, role checks, and ownership middleware run before Multer storage. Supertest verifies a cross-company upload receives 403. |
| A02 Cryptographic Failures | Passwords and authentication tokens are high-value secrets. | bcrypt cost 12; JWT expires in 15 minutes; production requires a 32+ character JWT secret. |
| A03 Injection | Search, filter and sort inputs are user-controlled. | Prisma query objects are used; sort is an enum mapped to fixed fields. No raw SQL or shell execution is used by application routes. OWASP recommends parameterized queries and allow-lists for structural SQL choices. citeturn2search0turn2search1 |
| A04 Insecure Design | Login abuse and malicious upload are explicit abuse cases. | Threat model, login rate limit, generic authentication errors, ownership-before-storage, and file validation. |
| A05 Security Misconfiguration | Unsafe defaults can expose headers, CORS, upload storage or excessive request bodies. | Helmet, disabled x-powered-by, explicit CORS, 100KB JSON limit, production secret/CORS validation, and no public upload route. citeturn2search6 |
| A06 Vulnerable and Outdated Components | Dependency vulnerabilities can become application vulnerabilities. | CI runs npm audit with a high-severity threshold for both packages. No unverified vulnerability count is claimed. |
| A07 Identification and Authentication Failures | Login brute force and weak session duration are material risks. | Endpoint-specific rate limiter, generic invalid-credential response, bcrypt cost 12, 15-minute JWT. OWASP recommends login throttling and generic authentication responses. citeturn0search1 |
| A08 Software and Data Integrity Failures | Uploaded bytes cross a trust boundary. | Memory buffering, MIME allow-list, magic-byte validation, size limits, generated UUID names, mode 0600, private filesystem and no public static route. citeturn2search9turn2search5 |
| A09 Security Logging and Monitoring Failures | Minimal logging is useful for errors but insufficient for production incident response. | Unexpected errors are logged; rate-limit responses are generic. Residual hardening: centralized structured security logging and alerting. |
| A10 Server-Side Request Forgery | Current API does not fetch arbitrary user-supplied URLs server-side. | Not applicable to current routes. Recheck this invariant if outbound URL-fetch features are added. |

## OWASP Top 10:2025 update check

The current OWASP release is Top 10:2025. Its categories are A01 Broken Access Control, A02 Security Misconfiguration, A03 Software Supply Chain Failures, A04 Cryptographic Failures, A05 Injection, A06 Insecure Design, A07 Authentication Failures, A08 Software or Data Integrity Failures, A09 Security Logging & Alerting Failures, and A10 Mishandling of Exceptional Conditions. citeturn0search0turn0search5

The 2021 taxonomy remains the grading baseline for this assignment. The 2025 taxonomy reinforces the dependency, exception-handling and security-misconfiguration controls identified here.

## Threat model

Assets: credentials, JWTs, student resumes, company logos, ownership relationships and internship data.

Trust boundaries: browser → Express API; Express → PostgreSQL through Prisma; Express → private filesystem.

Threats: credential stuffing, token theft, horizontal IDOR, malicious uploads, oversized requests, SQL injection, CORS abuse and dependency compromise.

Controls: authentication, role/ownership checks, short token lifetime, login throttling, generic auth errors, Zod validation, Prisma parameterization, allow-listed sort fields, magic-byte checks, size limits, UUID filenames, private storage, Helmet, explicit CORS, automated tests and CI dependency auditing.

## Before/after evidence

### Fix 1 — login brute force

Before: the Week 4 baseline had no endpoint-specific login rate limiter.

After: express-rate-limit protects /api/auth/login with a 5 requests / 60 seconds demo policy and standard RateLimit headers. The security integration test repeatedly calls the endpoint and asserts 429.

### Fix 2 — upload authorization and exposure

Before: the Week 4 baseline served /uploads statically and performed the ownership check after upload middleware had already processed/stored the bytes.

After: ownership middleware executes before Multer. Uploaded data is stored under a private directory, with crypto.randomUUID names, fixed server-selected extensions and mode 0600. There is no unauthenticated static /uploads route. The authorization integration test verifies the ownership violation returns 403.

## SQL injection review

No current application route uses Prisma raw query APIs, string-concatenated SQL, shell commands, or user-controlled table/column names. Search/filter values are data values in Prisma where objects, and sort is an enum mapped to a fixed order object. This is the explicit SQL-injection audit conclusion for the current source tree. OWASP recommends parameterized query interfaces and allow-listing for structural query choices. citeturn2search0

## Test strategy

The test pyramid prioritizes business-rule unit tests, then HTTP integration tests, then user-visible React component tests. React Testing Library is designed to exercise components through user-facing behavior rather than implementation details. citeturn1search10turn1search8

The backend API tests use Supertest, which can exercise an Express application without starting a persistent listener. citeturn1search2

## Residual risks

1. Production multi-instance deployments should replace the in-memory rate-limit store with a shared store.
2. Production should add centralized structured security logging and alerting.
3. Production file workflows should consider malware scanning/CDR where the deployment threat model requires it.
4. CI must record the actual npm audit result from the installed dependency graph; no unverified zero-vulnerability claim is made.
5. HTTPS/TLS is an infrastructure requirement for production authentication traffic.
