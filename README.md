# A Vibe Check — Security Audit + Expert Marketplace for AI-Built Apps

**[avibecheck.xyz](https://avibecheck.xyz) — Try the live scanner**

Two things in one project:

1. **Free scanner** — automated security audit that catches the dumb stuff AI code generators leave behind. Run it when you deploy. Get a score. Fix the holes.
2. **Expert marketplace** — when the scanner finds something you can't fix, or you hit a wall AI can't navigate, get matched with a vetted human who can. Security auditors, scaling specialists, frontend engineers, backend engineers, designers, and growth/launch specialists.

## What It Catches

**A: Secrets & Credentials**
- API keys hardcoded in frontend code
- `.env` files committed to git
- Weak or default JWT secrets

**B: Authentication & Sessions**
- No rate limiting on login endpoints
- JWTs stored in browser localStorage
- Tokens with no expiry
- Sessions that never get invalidated
- Weak password hashing (md5, sha1, plaintext)

**C: Authorization & Access Control**
- Admin auth checked only in frontend
- Missing auth middleware on protected routes
- Insecure Direct Object Reference (IDOR) patterns

**D: Injection & Input Handling**
- SQL injection vectors
- Cross-site scripting (XSS)
- Missing input validation
- Dangerous functions (eval, exec, pickle)

**E: Infrastructure & Deployment**
- CORS set to wildcard (`*`)
- Apps running as root
- Database ports exposed
- HTTP instead of HTTPS
- Debug mode enabled in production

**F: Error Handling**
- Stack traces leaking to clients

**G: BaaS & Third-Party**
- Supabase/Firebase RLS disabled
- Unsigned payment webhooks
- Dependencies that don't exist

**H: File Handling**
- File uploads without MIME type validation

**I: Transport & Browser Security**
- Open redirects
- Missing security headers
- No Content Security Policy

**J: Operational Security**
- No logging or audit trail
- No backup strategy

## Scoring

Each check has a severity level. Critical issues worth 10 points, High worth 7, Medium worth 4, Low worth 2. Max possible score is 220 points.

Your score is `(points earned / 220) × 100`. Grades break down like this:

- **A**: 90–100 (rare, congratulations)
- **B**: 80–89 (solid)
- **C**: 70–79 (acceptable)
- **D**: 60–69 (concerning)
- **F**: Below 60 (fix this before launch)

## Compliance Mapping

A Vibe Check aligns with:
- Privacy Act & GDPR (secrets, logging, data handling)
- OWASP Top 10 (injection, auth, crypto, headers)
- SOC 2 (access control, logging, incident response)
- PCI DSS (if handling payment data)

## Install

### Claude Code
1. Download the `.skill` file from [releases](https://github.com/namenroh/a-vibe-check/releases)
2. Open Claude Code
3. Install the skill (drag-and-drop or use the install dialog)
4. Run `a-vibe-check` on your project directory

### Cowork
Drag and drop the `.skill` file into your Cowork session.

## How It Works

A Vibe Check scans your codebase for patterns that indicate security issues. It runs as a CLI tool and outputs a structured report.

**Auto-Trigger**: When you deploy through Claude Code, A Vibe Check runs automatically. You get a score before your code ships.

**No False Positives**: Each check has a threshold—we're looking for real problems, not syntax style opinions.

## Try It — Demo App

We've included a deliberately vulnerable Node.js + Express app so you can see how A Vibe Check reports real issues.

```bash
# Clone the repo
git clone https://github.com/namenroh/a-vibe-check.git
cd a-vibe-check/demo-app

# Install and run
npm install
node app.js

# Scan it
a-vibe-check .
```

The demo app fails almost every check. Good learning tool.

## Example Output

Running A Vibe Check on the demo app produces this scorecard:

```
A VIBE CHECK SCORECARD
═══════════════════════════════════════════════════
Score: 23/100  |  Grade: F  |  Passed: 5/20

Critical issues: 7  |  High: 5  |  Medium: 3  |  Low: 0

FAILURES:
─────────────────────────────────────────────────
🔴 A1: API keys in frontend
   Found hardcoded API key in src/client.js:12

🔴 A2: .env file in git
   .env tracked in repository

🔴 B1: No rate limiting on auth
   /login endpoint accepts unlimited requests

🔴 B2: JWTs in localStorage
   Token stored client-side with no HttpOnly flag

🔴 C1: Frontend-only admin auth
   Admin check in JavaScript, missing backend validation

[... and 15 more failures ...]

RECOMMENDATIONS:
─────────────────────────────────────────────────
1. Move secrets to environment variables
2. Add rate limiting to auth endpoints
3. Move JWT to secure, HttpOnly cookies
4. Implement server-side authorization checks
```

## The Marketplace

AI gets you 80% of the way. Then it loops, burns tokens, or ships you something you don't trust. The scanner finds the wall. A human gets you over it.

**Six expert categories, all vetted:**

| Category | What they help with | Typical rate |
| --- | --- | --- |
| Security & Compliance | Real audits, pen-tests, RLS/auth, GDPR/SOC 2/PCI mapping | $150–400 / hr |
| Architecture & Scaling | Database design, caching, queues, infra cost, performance | $120–300 / hr |
| Frontend & UX Engineering | Accessibility, performance, state, animation, device coverage | $90–200 / hr |
| Backend & Integrations | API design, Stripe, webhooks, auth, background jobs | $90–250 / hr |
| Design & UX | UX audits, design systems, conversion UI, brand, landing | $80–200 / hr |
| Product, Growth & Launch | Positioning, launch, pricing, content, SEO | $70–200 / hr |

### How it works

1. **Scan or describe.** Run A Vibe Check on your repo — or just tell us where you're stuck. Your scan results pre-fill the brief automatically.
2. **We match, not list.** No endless profiles. We hand-pick 1–3 specialists who've solved your exact problem, with a fixed-scope quote up front.
3. **Ship with confidence.** Work together in whatever fits — Claude Code, GitHub, Slack, a call. Pay on milestones. You own the code.

### Scan-aware matching

When the web scanner runs, the post-scan panel recommends a specialist based on your failed checks:

- Mostly auth / secrets / injection failures → matched with a **security specialist**
- Mostly infra / ops / monitoring failures → matched with an **architecture & scaling specialist**
- Mostly transport / headers / frontend failures → matched with a **frontend engineer**
- Mostly error handling / file / integration failures → matched with a **backend engineer**
- Clean score (80+) → matched for **design / growth / launch** if you want to level up

### Applying as an expert

Click "Apply to the pool" on the site. We review weekly, vet for quality (not quantity), and pair you with paying projects from vibe coders who've already scanned their repo and know where they're stuck. Set your own rate, pick the work you want.

## Contributing

Found a check we're missing? Have a false positive? Open an issue or PR.

We're looking for:
- **Scanner checks** that catch real problems in AI-generated code (security theater doesn't help)
- **Expert applicants** across all six marketplace categories
- **Vibe coders** who want to shape the marketplace MVP — request a match or give us feedback on what's missing

## License

MIT License. Copyright 2026 Josh Horneman.
