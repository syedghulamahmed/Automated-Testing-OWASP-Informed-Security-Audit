# Before / After Security Evidence

| Finding | Before | After | Evidence |
|---|---|---|---|
| Login brute force | No endpoint-specific limiter | 5 requests / 60 seconds on /api/auth/login | security.integration.test.ts expects 429 |
| Upload authorization/exposure | Week 4 wrote uploads before ownership validation and served the upload directory statically | Ownership middleware runs before multer; files use UUID names, mode 0600, private storage and no public static route | authorization.integration.test.ts + route ordering |

The before state refers to the immediately preceding Week 4 repository and is documented without weakening the Week 5 code to reproduce the vulnerability.
