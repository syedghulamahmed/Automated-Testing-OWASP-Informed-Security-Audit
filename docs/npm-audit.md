# Dependency Audit Procedure

Run from a clean checkout:

npm install
npm --prefix backend install
npm --prefix frontend install
npm --prefix backend audit --audit-level=high
npm --prefix frontend audit --audit-level=high

The repository intentionally does not claim a vulnerability count without actually running the audit against the installed dependency graph. Any high/critical finding should be upgraded or explicitly justified here with package, advisory, impact, and compensating control.
