# Beginner Threat Model

## Assets
Credentials, JWTs, student resumes, company logos, ownership relationships, and internship search data.

## Actors
- Anonymous attacker: public API access.
- Authenticated student: horizontal-access attempt against another student.
- Authenticated company: horizontal-access attempt against another company.
- Credential-stuffing operator: repeated login attempts.
- Malicious uploader: oversized, spoofed, or path-manipulation files.

## Entry points
POST /api/auth/register, POST /api/auth/login, GET /api/internships, GET /api/me, POST /api/students/:id/resume, POST /api/companies/:id/logo.

## Trust boundaries
Browser to API; API to PostgreSQL through Prisma; API to private filesystem.

## High-value abuse cases and controls
- Password guessing → endpoint-specific rate limiting, generic credential errors, bcrypt cost 12, short JWT lifetime.
- IDOR → authentication, role checks, and ownership checks before upload storage.
- Malicious files → MIME allow-list, size limits, magic-byte checks, UUID filenames, private storage and restrictive permissions.
- Injection → Zod validation, Prisma query objects, allow-listed sort fields and no application raw SQL.
- Unsafe defaults → Helmet, explicit CORS, disabled x-powered-by, bounded JSON body size, production secret/CORS checks.
- Dependency compromise → npm audit in CI and documented remediation procedure.
