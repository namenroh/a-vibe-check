# Vibe Check — Scoring Methodology

## How Scoring Works

Each of the 30 checks has a severity weight. The score reflects how much of the total security surface is properly handled.

## Severity Weights

| Severity | Weight | Description |
|----------|--------|-------------|
| Critical | 10 | Exploitable now with minimal effort. Data breach, RCE, or full system compromise likely. |
| High | 7 | Dangerous vulnerability requiring a specific but achievable attack vector. |
| Medium | 4 | Bad practice that increases attack surface or enables future exploitation. |
| Low | 2 | Minor issue or missing best practice. Low direct risk but contributes to security posture. |

## Check Severity Assignments

| # | Check | Severity | Weight |
|---|-------|----------|--------|
| 1 | API keys in frontend code | Critical | 10 |
| 2 | .env files in git history | Critical | 10 |
| 3 | Weak JWT secret | Critical | 10 |
| 4 | No rate limiting on auth endpoints | High | 7 |
| 5 | JWTs in localStorage | Medium | 4 |
| 6 | Auth tokens that never expire | High | 7 |
| 7 | Sessions not invalidated on logout | Medium | 4 |
| 8 | Weak password hashing | Critical | 10 |
| 9 | Admin routes with frontend-only protection | Critical | 10 |
| 10 | Missing auth middleware on internal routes | Critical | 10 |
| 11 | IDOR on resource endpoints | Critical | 10 |
| 12 | SQL injection via string concatenation | Critical | 10 |
| 13 | XSS via unsafe HTML rendering | High | 7 |
| 14 | Missing input validation | High | 7 |
| 15 | Unsafe deserialisation / eval / exec | Critical | 10 |
| 16 | CORS wildcard configuration | High | 7 |
| 17 | Application running as root | High | 7 |
| 18 | Database port exposed publicly | Critical | 10 |
| 19 | No HTTPS enforcement | High | 7 |
| 20 | Debug mode in production | High | 7 |
| 21 | Stack traces in error responses | Medium | 4 |
| 22 | Supabase/Firebase RLS disabled | Critical | 10 |
| 23 | Payment webhook signature not verified | Critical | 10 |
| 24 | Hallucinated/unaudited dependencies | High | 7 |
| 25 | File uploads without MIME validation | High | 7 |
| 26 | Open redirects in callback URLs | Medium | 4 |
| 27 | Missing security headers | Low | 2 |
| 28 | CSP missing or permissive | Medium | 4 |
| 29 | No logging or monitoring | Medium | 4 |
| 30 | No backup or recovery plan | Medium | 4 |

**Maximum possible score: 220 points** (if all 30 checks apply)

Note: N/A checks are excluded from both the earned and possible totals. If a project has no database, checks 18 and 30 don't apply and the maximum is reduced accordingly.

## Calculating the Score

```
Score = (points earned / points possible) × 100
```

Where:
- Points earned = sum of weights for all PASSED checks
- Points possible = sum of weights for all APPLICABLE checks (excludes N/A)

## Grade Boundaries

| Grade | Score | Meaning |
|-------|-------|---------|
| A | 90-100 | Strong security posture. Minor improvements only. Ship with confidence. |
| B | 80-89 | Good foundation. A few gaps to close before production. |
| C | 70-79 | Significant vulnerabilities exist. Fix high/critical items before deploying. |
| D | 60-69 | Serious security gaps. Do not deploy without remediation. |
| F | <60 | Fundamental security failures. Stop and fix before anything else. |

## Scorecard Format

Include this visual scorecard in the report:

```
VIBE CHECK SCORECARD
═══════════════════════════════════════════════════
Score: XX/100  |  Grade: X  |  Passed: XX/XX

Category                          Status
───────────────────────────────────────────────────
A: Secrets & Credentials          [X/3]  ██░░░
B: Authentication & Sessions      [X/5]  █████
C: Authorisation & Access Control [X/3]  ███░░
D: Injection & Input Handling     [X/4]  ████░
E: Infrastructure & Deployment    [X/5]  █░░░░
F: Error Handling & Info Leakage  [X/1]  █████
G: BaaS & Third-Party            [X/3]  ███░░
H: File Handling & Uploads        [X/1]  █████
I: Transport & Browser Security   [X/3]  ██░░░
J: Operational Security           [X/2]  ░░░░░
───────────────────────────────────────────────────
Critical issues: X  |  High: X  |  Medium: X  |  Low: X
```

Use filled blocks (█) for passed checks and empty blocks (░) for failed checks in each category. This gives an instant visual read on where the gaps are.

## Comparison Context

For reference, typical scores we see across AI-built applications:

- First-time vibe coder, no security review: 15-30 (Grade F)
- Experienced developer using AI, no formal audit: 50-70 (Grade D to C)
- After running Vibe Check and fixing critical items: 75-90 (Grade C to A)
- Production-grade with security review: 85-100 (Grade B to A)

Most AI-built apps score between 20 and 40 on first audit. This is normal. The point is to find and fix the gaps, not to feel bad about the number.
