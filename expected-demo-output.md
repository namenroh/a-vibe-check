# Vibe Check Report — Vulnerable Demo App

Date: 2026-04-04
Stack: Express.js, jsonwebtoken, cors, Node.js
Vibe Check Score: 23/100 (Grade F)
Checks Passed: 3/30
Checks Failed: 15/30
Checks N/A: 12/30

## Scorecard

```
VIBE CHECK SCORECARD
═══════════════════════════════════════════════════
Score: 23/100  |  Grade: F  |  Passed: 3/18 applicable (12 N/A)

Category                          Status (passed / total · N/A)
───────────────────────────────────────────────────
A: Secrets & Credentials          [0/3]        ░░░
B: Authentication & Sessions      [0/5]        ░░░░░
C: Authorisation & Access Control [0/3]        ░░░
D: Injection & Input Handling     [0/4]        ░░░░
E: Infrastructure & Deployment    [1/5] 2 N/A  █░░░░
F: Error Handling & Info Leakage  [0/1]        ░
G: BaaS & Third-Party             [0/3] 3 N/A  (all N/A)
H: File Handling & Uploads        [0/1] 1 N/A  (N/A)
I: Transport & Browser Security   [1/3] 2 N/A  █░░
J: Operational Security           [1/2] 1 N/A  █░
───────────────────────────────────────────────────
Critical issues: 6  |  High: 6  |  Medium: 3  |  Low: 0
```

## Critical (fix before shipping)

**Check #1 — API Keys in Frontend Code**
File: `vulnerable-demo-app.js`, line 30-35
Stripe secret key (`sk_live_...`), Supabase service role key, and OpenAI API key are all served via a public `/api/config` endpoint. Anyone who opens devtools or hits this endpoint directly gets full access to all three services.

```javascript
// VULNERABLE
app.get('/api/config', (req, res) => {
  res.json({
    stripeKey: 'sk_live_51ABC123DEF456GHI789JKL',
    supabaseServiceRole: 'eyJhbGci...',
    openaiKey: 'sk-proj-abc123def456ghi789',
  });
});

// FIX: Move to server-side env vars, proxy through backend
// These keys should NEVER appear in any client-accessible code or endpoint
```

**Check #3 — Weak JWT Secret**
File: `vulnerable-demo-app.js`, line 40
JWT secret is the literal string `"secret"`. This is on every wordlist. An attacker can forge any token in seconds.

```javascript
// VULNERABLE
const JWT_SECRET = 'secret';

// FIX
const JWT_SECRET = process.env.JWT_SECRET;
// Generate with: require('crypto').randomBytes(64).toString('hex')
```

**Check #8 — Weak Password Hashing (MD5)**
File: `vulnerable-demo-app.js`, lines 43-45, 52
Passwords are hashed with MD5. No salt. Rainbow tables crack MD5 in seconds. Every user's password is effectively stored in plaintext.

```javascript
// VULNERABLE
crypto.createHash('md5').update(password).digest('hex')

// FIX
const bcrypt = require('bcrypt');
const hash = await bcrypt.hash(password, 12);
const match = await bcrypt.compare(password, hash);
```

**Check #9 — Admin Routes with Frontend-Only Protection**
File: `vulnerable-demo-app.js`, lines 84-92
`/api/admin/users` and `/api/admin/users/:id` have `authenticate` middleware but no role check. Any authenticated user — not just admins — can list all users and delete any account.

```javascript
// VULNERABLE
app.get('/api/admin/users', authenticate, (req, res) => { ... });

// FIX
function requireAdmin(req, res, next) {
  if (req.user.role !== 'admin') return res.status(403).json({ error: 'Forbidden' });
  next();
}
app.get('/api/admin/users', authenticate, requireAdmin, (req, res) => { ... });
```

**Check #11 — IDOR on Resource Endpoints**
File: `vulnerable-demo-app.js`, lines 72-77
`/api/users/:id` returns any user's data to any authenticated user. Change the ID, get another user's email and role.

```javascript
// VULNERABLE
app.get('/api/users/:id', authenticate, (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  ...
});

// FIX
app.get('/api/users/:id', authenticate, (req, res) => {
  if (parseInt(req.params.id) !== req.user.userId) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  ...
});
```

**Check #12 — SQL Injection**
File: `vulnerable-demo-app.js`, lines 80-82
Search query is concatenated directly into SQL string. Classic injection — `'; DROP TABLE products; --` would work.

```javascript
// VULNERABLE
const sql = `SELECT * FROM products WHERE name LIKE '%${query}%'`;

// FIX (using parameterised query)
const sql = `SELECT * FROM products WHERE name LIKE $1`;
const values = [`%${query}%`];
```

**Check #18 — Database Port Exposed** — N/A (no database in demo)

## High (fix before production)

**Check #4 — No Rate Limiting on Login**
File: `vulnerable-demo-app.js`, line 49
Login endpoint has no rate limiting. Bots can brute-force passwords at thousands of attempts per second.

**Check #6 — Auth Tokens Never Expire**
File: `vulnerable-demo-app.js`, line 58
JWT is signed without an `expiresIn` option. Token is valid forever.

**Check #13 — XSS via innerHTML**
File: `vulnerable-demo-app.js`, lines 95-105
`/api/render` endpoint reflects user input directly into HTML and JavaScript. Full XSS.

**Check #14 — Missing Input Validation**
File: `vulnerable-demo-app.js`, throughout
No validation library (zod, joi, etc.) is used. Every endpoint trusts `req.body` and `req.params` as-is.

**Check #16 — CORS Wildcard**
File: `vulnerable-demo-app.js`, line 18
CORS origin set to `*` with `credentials: true`. Any website can make authenticated requests.

**Check #20 — Debug Mode in Production**
File: `vulnerable-demo-app.js`, line 21
Express env set to `development`. Debug information exposed.

## Medium (fix soon)

**Check #5 — JWTs in localStorage**
File: `vulnerable-demo-app.js`, line 62
Server response instructs client to store token in localStorage.

**Check #7 — Sessions Not Invalidated on Logout**
File: `vulnerable-demo-app.js`, lines 66-69
Logout endpoint does nothing server-side. The token remains valid.

**Check #21 — Stack Traces in Error Responses**
File: `vulnerable-demo-app.js`, lines 108-117
Global error handler returns full stack trace, error message, path, and method to the client.

## Passed

- Check #17 — Not running as root (no Docker config present to assess, but no evidence of root)
- Check #26 — No redirect endpoints found
- Check #29 — console.log present (minimal, but exists)

## N/A

- Check #2 — No git repository in demo
- Check #10 — Only a few routes, all audited above
- Check #15 — No eval/exec/pickle usage found
- Check #18 — No database configuration present
- Check #19 — No deployment config (would depend on hosting)
- Check #22 — No Supabase/Firebase
- Check #23 — No payment integration (the Stripe key is exposed but no webhook handler)
- Check #24 — No package.json in demo (single file)
- Check #25 — No file upload handling
- Check #27 — Would need deployment to assess
- Check #28 — Would need deployment to assess
- Check #30 — No database to back up

## Compliance Impact

### Australian Privacy Act 1988
This application fails APP 11 (security of personal information) on all 15 failed checks. The combination of weak password hashing (MD5), IDOR vulnerabilities, SQL injection, and hardcoded API keys means personal information has essentially no protection. If this application processed real user data, a breach would trigger Notifiable Data Breaches scheme obligations.

### OWASP Top 10
Findings map to 5 of 10 OWASP categories:
- A01 Broken Access Control: Checks 9, 11
- A02 Cryptographic Failures: Checks 1, 3, 8
- A03 Injection: Checks 12, 13, 14
- A05 Security Misconfiguration: Checks 16, 20, 21
- A07 Identification and Authentication Failures: Checks 4, 5, 6, 7

## Recommended Fix Order

1. **Rotate all exposed API keys immediately** (Check #1) — 15 minutes
2. **Replace MD5 with bcrypt** (Check #8) — 30 minutes + password reset for all users
3. **Fix SQL injection** (Check #12) — 15 minutes per query
4. **Add role checking to admin routes** (Check #9) — 15 minutes
5. **Add ownership checks to resource endpoints** (Check #11) — 30 minutes
6. **Generate proper JWT secret** (Check #3) — 5 minutes
7. **Add token expiry** (Check #6) — 5 minutes
8. **Add rate limiting to login** (Check #4) — 15 minutes
9. **Sanitise HTML rendering** (Check #13) — 20 minutes
10. **Lock down CORS** (Check #16) — 5 minutes
11. **Fix error handling** (Check #21) — 15 minutes
12. **Set production mode** (Check #20) — 5 minutes
13. **Implement proper logout** (Check #7) — 20 minutes
14. **Move tokens to httpOnly cookies** (Check #5) — 45 minutes
15. **Add input validation with zod/joi** (Check #14) — 45 minutes

Total estimated effort: ~5 hours
