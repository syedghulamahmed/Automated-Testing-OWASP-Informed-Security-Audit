# TalentBridge — Automated Testing & OWASP-Informed Security Audit

NeuroFive Solutions Week 5. This repository is a security-focused continuation of the TalentBridge Week 4 application, kept in its own repository.

## What is included

- Backend Vitest unit tests for risky business rules.
- Supertest API integration tests for register/login, protected routes, CORS, rate limiting and ownership.
- React Testing Library component tests for login validation, search/filter behavior and failed API states.
- Login rate limiting with express-rate-limit.
- Explicit CORS allow-list configuration.
- Helmet security headers and production-safe configuration checks.
- Strict authentication/role/ownership checks before file storage.
- Prisma ORM queries with allow-listed sort fields; no application raw SQL.
- OWASP Top 10:2021 audit mapped to this application, plus a check against the current 2025 release.
- Before/after evidence for two security fixes.
- CI workflow with one-command test and audit stages.

## Setup

Install backend dependencies: npm --prefix backend install

Install frontend dependencies: npm --prefix frontend install

Run the full test suite: npm test

Run dependency audits: npm run audit

The integration tests use fresh in-memory mocked persistence and never depend on development seed data. This is an isolated mock strategy permitted by the assignment. A PostgreSQL test stage can be substituted later without changing the HTTP assertions.

## Database

The Prisma schema, migration and 220-record seed are retained from the Week 4 application baseline. Set DATABASE_URL and run Prisma migrate deploy plus npm run seed inside backend when using PostgreSQL.

## Security configuration

Set DATABASE_URL, a strong JWT_SECRET, and CORS_ORIGIN in production. CORS_ORIGIN must be an explicit frontend origin; wildcard * is rejected in production. Uploaded files are stored outside the frontend web root with generated UUID filenames and restrictive filesystem permissions. There is no unauthenticated static upload route.

See SECURITY-AUDIT.md, docs/THREAT-MODEL.md, docs/before-after.md and docs/TEST-REPORT.md.

## Demo credentials

admin@example.com / DemoPass1!
company@example.com / DemoPass1!
student@example.com / DemoPass1!

## Evidence policy

The repository never fabricates test, npm audit, or database benchmark results. CI or a clean local run should be used to record actual execution results.
