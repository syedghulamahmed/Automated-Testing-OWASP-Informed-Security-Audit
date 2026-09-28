# Test Report

## Test pyramid
- Unit: 6 backend business-rule tests.
- Integration/API: register, login, protected-route rejection, malformed token, CORS, ownership, rate limiting, body-size and security-header tests.
- Component: 6 React Testing Library tests for validation, failed API state, debounced search, location filtering and visible behavior.
- E2E: intentionally omitted; the required risk is covered by the lower layers.

## Isolation
Backend API tests replace Prisma with fresh in-memory state and never read development seed data. Frontend tests use JSDOM and mocked fetch. This is a properly isolated mock strategy; a real PostgreSQL test stage can be added without changing HTTP assertions.

## AAA
Tests arrange isolated state, act through a service or user-visible API interaction, and assert the resulting behavior/status.

## Runtime claim policy
This document records the suite design, not a fabricated pass count. CI or a clean local run should be used to record actual results.
