# Login Rate-Limiting Research

OWASP's Authentication Cheat Sheet describes login throttling as a defense against password guessing and recommends considering attempts, observation windows, and lockout duration. OWASP's anti-automation guidance recommends separate per-IP and per-username controls for credential stuffing rather than one combined IP+username bucket. citeturn0search1turn0search3

This project adds an endpoint-specific express-rate-limit policy to /api/auth/login: 5 requests per 60 seconds per source IP in the demo configuration. A production multi-instance deployment should use a shared store and consider identity-bound throttling after analyzing account-lockout denial-of-service tradeoffs.

The limit response is generic 429. express-rate-limit supports standard RateLimit headers and custom stores. citeturn1search3
