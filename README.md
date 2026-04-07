# A Vibe Check — Security Audit for AI-Built Apps

**[avibecheck.xyz](https://avibecheck.xyz) — Try the live scanner**

Automated security scanning that catches the dumb stuff developers leave behind. Run it when you deploy. Get a score. Fix the holes.

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

## Contributing

Found a check we're missing? Have a false positive? Open an issue or PR.

We're looking for new checks that catch real problems in AI-generated code. Security theater doesn't help.

## License

MIT License. Copyright 2026 Josh Horneman.
