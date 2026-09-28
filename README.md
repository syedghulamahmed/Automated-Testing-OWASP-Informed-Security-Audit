# TalentBridge — Automated Testing & OWASP-Informed Security Audit

NeuroFive Solutions Week 5. This repository is a security-focused continuation of the TalentBridge Week 4 application, kept in its own repository.

## What is included

- Backend Vitest unit tests for business/security logic.
- Supertest API integration tests with an isolated mocked persistence layer.
- React Testing Library component tests for login validation, search/filter behavior, and failed API states.
- Login rate limiting with `express-rate-limit`.
- Explicit CORS allow-list configuration.
- Helmet security headers and production-safe configuration checks.
- Strict authentication/role/ownership checks for profile uploads.
- Prisma ORM queries with allow-listed sort fields; no raw SQL in application code.
- OWASP Top 10:2021 audit mapped to this application's actual attack surface, with a note on the current Top 10:2025 release.
- Before/after security evidence for rate limiting and public upload exposure.
- CI workflow and one-command test script.

## Setup

Backend: `cd backend && npm ci && npm test`

Frontend: `cd frontend && npm ci && npm test -- --run`

Full suite from the repository root: `npm test`

The integration tests use a fresh in-memory mocked repository per test file and never depend on development data. A real PostgreSQL test database can be substituted later by changing the repository adapter without changing HTTP assertions.

## Security configuration

Set `DATABASE_URL`, a strong `JWT_SECRET`, and `CORS_ORIGIN` in production. `CORS_ORIGIN` must be an explicit frontend origin; wildcard `*` is rejected in production. Uploaded files are stored outside the frontend web root with generated UUID filenames and are not exposed through an unauthenticated static route.

See `SECURITY-AUDIT.md` and `docs/THREAT-MODEL.md` for the audit and threat model.

## Evidence note

`docs/npm-audit.md` records the dependency-audit procedure and the result that should be captured from the exact lockfile after a clean `npm ci`. Runtime tests are designed to be CI-friendly; this repository does not claim a test execution result unless it is recorded in `docs/TEST-REPORT.md` by CI or a local run.
