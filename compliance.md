# Vibe Check — Compliance Mapping

This document maps each Vibe Check finding to applicable compliance frameworks. Use this to help clients understand the regulatory implications of security gaps — especially useful for Australian businesses subject to the Privacy Act.

## How to Use This Mapping

After running the audit, include a "Compliance Impact" section in the report. For each failed check, note which frameworks are affected. This transforms a technical audit into a business risk conversation.

## Framework Overview

### Australian Privacy Act 1988
**APP 11 — Security of personal information:** An APP entity must take reasonable steps to protect personal information from misuse, interference, loss, unauthorised access, modification, or disclosure.

The word "reasonable" is doing heavy lifting here. What's reasonable depends on the sensitivity of the data, the consequences of a breach, and the cost of preventive measures. Most Vibe Check findings represent measures that are free or near-free to implement — making them hard to argue aren't "reasonable."

**Notifiable Data Breaches (NDB) Scheme:** If a breach is likely to result in serious harm, it must be reported to the OAIC and affected individuals. A Vibe Check failure that leads to a breach creates a notification obligation.

### OWASP Top 10 (2021)
The industry-standard classification of web application security risks. Most Vibe Check findings map directly to one or more OWASP categories.

### SOC 2 Type II
Relevant for SaaS companies and B2B services. Maps to Trust Service Criteria (TSC) for Security, Availability, Processing Integrity, Confidentiality, and Privacy.

### PCI DSS v4.0
Applies if the application processes, stores, or transmits cardholder data. Even if you use Stripe or a payment gateway, some requirements still apply.

### GDPR Article 32
Applies if the application handles personal data of EU residents. Requires "appropriate technical and organisational measures" for data security.

---

## Mapping Table

| # | Check | OWASP Top 10 | Privacy Act (APP) | SOC 2 TSC | PCI DSS | GDPR |
|---|-------|-------------|-------------------|-----------|---------|------|
| 1 | API keys in frontend | A01 Broken Access Control | APP 11.1 | CC6.1 | 6.5.3 | Art 32(1)(b) |
| 2 | .env in git history | A02 Cryptographic Failures | APP 11.1 | CC6.1 | 2.3, 6.5.3 | Art 32(1)(b) |
| 3 | Weak JWT secret | A02 Cryptographic Failures | APP 11.1 | CC6.1 | 6.5.3 | Art 32(1)(a) |
| 4 | No rate limiting on auth | A07 Identification Failures | APP 11.1 | CC6.1, CC6.6 | 6.5.10 | Art 32(1)(b) |
| 5 | JWTs in localStorage | A01 Broken Access Control | APP 11.1 | CC6.1 | 6.5.7 | Art 32(1)(b) |
| 6 | Tokens never expire | A07 Identification Failures | APP 11.1 | CC6.1 | 8.2.5 | Art 32(1)(b) |
| 7 | Sessions not invalidated | A07 Identification Failures | APP 11.1 | CC6.1 | 6.5.10 | Art 32(1)(b) |
| 8 | Weak password hashing | A02 Cryptographic Failures | APP 11.1 | CC6.1 | 8.3.2 | Art 32(1)(a) |
| 9 | Frontend-only admin auth | A01 Broken Access Control | APP 11.1 | CC6.1 | 6.5.8 | Art 32(1)(b) |
| 10 | Missing auth middleware | A01 Broken Access Control | APP 11.1 | CC6.1 | 6.5.8 | Art 32(1)(b) |
| 11 | IDOR vulnerabilities | A01 Broken Access Control | APP 11.1, APP 6 | CC6.1 | 6.5.4 | Art 32(1)(b) |
| 12 | SQL injection | A03 Injection | APP 11.1 | CC6.1 | 6.5.1 | Art 32(1)(b) |
| 13 | XSS via unsafe rendering | A03 Injection | APP 11.1 | CC6.1 | 6.5.7 | Art 32(1)(b) |
| 14 | Missing input validation | A03 Injection | APP 11.1 | CC6.1 | 6.5.1 | Art 32(1)(b) |
| 15 | eval / exec / pickle | A08 Software Integrity | APP 11.1 | CC6.1 | 6.5.1 | Art 32(1)(b) |
| 16 | CORS wildcard | A05 Security Misconfig | APP 11.1 | CC6.1 | 6.5.8 | Art 32(1)(b) |
| 17 | Running as root | A05 Security Misconfig | APP 11.1 | CC6.1 | — | Art 32(1)(b) |
| 18 | DB port exposed | A05 Security Misconfig | APP 11.1 | CC6.1, CC6.6 | 1.3.1 | Art 32(1)(b) |
| 19 | No HTTPS | A02 Cryptographic Failures | APP 11.1 | CC6.1, CC6.7 | 4.1 | Art 32(1)(a) |
| 20 | Debug mode in production | A05 Security Misconfig | APP 11.1 | CC6.1 | 6.5.5 | Art 32(1)(b) |
| 21 | Stack traces in responses | A05 Security Misconfig | APP 11.1 | CC6.1 | 6.5.5 | Art 32(1)(b) |
| 22 | Supabase/Firebase RLS | A01 Broken Access Control | APP 11.1, APP 6 | CC6.1 | 6.5.4 | Art 32(1)(b), Art 25 |
| 23 | Webhook sig not verified | A08 Software Integrity | APP 11.1 | CC6.1 | — | Art 32(1)(b) |
| 24 | Unaudited dependencies | A06 Vuln Components | APP 11.1 | CC6.1, CC7.1 | 6.3.2 | Art 32(1)(b) |
| 25 | Upload without MIME check | A04 Insecure Design | APP 11.1 | CC6.1 | 6.5.8 | Art 32(1)(b) |
| 26 | Open redirects | A01 Broken Access Control | APP 11.1 | CC6.1 | 6.5.10 | Art 32(1)(b) |
| 27 | Missing security headers | A05 Security Misconfig | APP 11.1 | CC6.1 | 6.5.5 | Art 32(1)(b) |
| 28 | CSP missing/permissive | A05 Security Misconfig | APP 11.1 | CC6.1 | 6.5.7 | Art 32(1)(b) |
| 29 | No logging/monitoring | A09 Logging Failures | APP 11.1, APP 1 | CC7.2, CC7.3 | 10.1 | Art 33, Art 34 |
| 30 | No backup/recovery | — | APP 11.1 | A1.2 | — | Art 32(1)(c) |

## Report Format for Compliance Section

When including compliance impact in the report, use this format:

```
## Compliance Impact

### Australian Privacy Act 1988
This audit found [X] issues that may affect compliance with APP 11 
(security of personal information). The critical findings — 
[list critical check names] — represent gaps that would be difficult 
to defend as "reasonable steps" under the Act. If these vulnerabilities 
were exploited, the Notifiable Data Breaches scheme would likely require 
notification to the OAIC.

Affected APPs: [list]
Recommended: Fix all critical and high items before processing personal information.

### OWASP Top 10
Findings map to [X] of 10 OWASP categories:
- A01 Broken Access Control: Checks [X, Y, Z]
- A03 Injection: Checks [X, Y]
[etc.]

### [Other applicable frameworks]
[Similar format]
```

## When to Include Compliance Mapping

Always include the Australian Privacy Act mapping — Josh's clients are predominantly Australian businesses.

Include OWASP Top 10 for all reports — it's the universal reference.

Include SOC 2 if the client is a SaaS or B2B service.

Include PCI DSS only if the app processes payments.

Include GDPR only if the app handles EU user data or the client specifically asks.

## Disclaimer

This compliance mapping is informational guidance, not legal advice. It identifies which compliance requirements are likely affected by security findings. Clients should consult with their legal counsel or compliance advisor for definitive compliance assessment. The mapping is based on public framework documentation as of April 2026 and may not reflect recent amendments.
