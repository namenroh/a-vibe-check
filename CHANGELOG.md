# Changelog

All notable changes to this project will be documented in this file.

## [2.0.0] - 2026-04-06

### Added
- **30 security checks** (expanded from 20). New coverage includes:
  - Weak password hashing detection (bcrypt, scrypt, argon2 validation)
  - Supabase and Firebase RLS misconfiguration detection
  - Unsigned payment webhook detection
  - Hallucinated dependency detection (packages that don't exist)
  - File upload MIME type validation
  - Open redirect detection
  - Security header analysis (HSTS, X-Frame-Options, X-Content-Type-Options)
  - CSP validation
  - Backup strategy verification

- **Severity scoring system**:
  - Critical: 10 points
  - High: 7 points
  - Medium: 4 points
  - Low: 2 points
  - Max possible score: 220 points normalized to A-F grading scale

- **Compliance framework mapping**:
  - Privacy Act & GDPR alignment
  - OWASP Top 10 categorization
  - SOC 2 control mapping
  - PCI DSS references for payment handling

- **Framework-specific detection**:
  - Express.js best practices
  - Next.js configuration validation
  - Supabase security checks
  - Firebase security checks
  - AWS credential detection

- **Vulnerable demo app**:
  - Fully functional Node.js + Express application
  - Intentionally fails 25+ checks
  - Real-world attack vectors
  - Included in repository for learning and testing

- **Enhanced CLI output**:
  - Structured scorecard format
  - Issue categorization by severity
  - Actionable remediation suggestions
  - Compliance mapping in results

### Changed
- Scoring algorithm redesigned for normalized 0-100 scale
- Grade thresholds: A (90–100), B (80–89), C (70–79), D (60–69), F (<60)
- Issue descriptions now include remediation examples
- Report output formatted for readability

### Improved
- False positive reduction through pattern refinement
- Better detection of obfuscated secrets
- Improved IDOR detection logic
- Enhanced CORS wildcard detection across configurations

## [1.0.0] - 2026-04-04

### Initial Release
- **20 core security checks**:
  - A: Secrets & Credentials (3 checks)
  - B: Authentication & Sessions (5 checks)
  - C: Authorization & Access Control (3 checks)
  - D: Injection & Input Handling (4 checks)
  - E: Infrastructure & Deployment (5 checks)

- **Basic CLI interface**:
  - Directory scanning
  - Pattern-based detection
  - Simple pass/fail output

- **Auto-trigger on deploy** via Claude Code integration
- **Support for**:
  - JavaScript/Node.js
  - Python
  - SQL files
  - Configuration files (.env, config.js, etc.)
