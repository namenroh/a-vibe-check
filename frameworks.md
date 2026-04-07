# Vibe Check — Framework-Specific Detection Patterns

When the stack detection in Step 1 identifies a specific framework, use these additional patterns alongside the main checklist. These catch framework-specific footguns that AI generators introduce.

## Next.js

```bash
# Server Actions without auth checks (Next.js 13+)
grep -rn "use server" --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" app/ src/ 2>/dev/null
# Each "use server" file/function is a public API endpoint. Check every one for auth.

# API routes without middleware (Pages Router)
ls pages/api/**/*.ts pages/api/**/*.js 2>/dev/null
# Each file is an endpoint. Check for auth in each.

# Route Handlers without auth (App Router)
find app -name "route.ts" -o -name "route.js" 2>/dev/null
# Each is an endpoint. Check for auth.

# Middleware configuration
cat middleware.ts middleware.js src/middleware.ts src/middleware.js 2>/dev/null
# Check matcher config — does it cover all protected routes?

# Environment variable exposure
grep -rn "NEXT_PUBLIC_" .env* 2>/dev/null
# Everything prefixed NEXT_PUBLIC_ is shipped to the browser.
```

**Common Next.js AI-generated issues:**
- Server Actions that mutate data without checking `auth()`
- API routes that trust `req.body` without validation
- Middleware matcher that doesn't cover all routes
- `NEXT_PUBLIC_` prefix on secrets (Supabase service role key is the classic)
- `revalidateTag`/`revalidatePath` exposed without auth

## React (Create React App / Vite)

```bash
# dangerouslySetInnerHTML usage
grep -rn "dangerouslySetInnerHTML" --include="*.jsx" --include="*.tsx" src/ 2>/dev/null

# Direct DOM manipulation
grep -rn "\.innerHTML\|\.outerHTML\|document\.write" --include="*.jsx" --include="*.tsx" --include="*.js" --include="*.ts" src/ 2>/dev/null

# Auth state stored only in React context (no server validation)
grep -rn "AuthContext\|AuthProvider\|useAuth\|isAuthenticated\|isAdmin" --include="*.jsx" --include="*.tsx" src/ 2>/dev/null
# If auth state exists only in React context without server-side validation, it's bypassable.

# Environment variables
grep -rn "REACT_APP_\|VITE_" .env* 2>/dev/null
# All of these are embedded in the build and publicly visible.
```

## Express.js

```bash
# Routes without middleware
grep -n "app\.\(get\|post\|put\|patch\|delete\)\|router\.\(get\|post\|put\|patch\|delete\)" --include="*.js" --include="*.ts" routes/ src/ server/ api/ 2>/dev/null
# List all routes. Cross-reference against auth middleware.

# Global error handler leaking details
grep -n "app\.use.*err.*req.*res\|error.*handler" --include="*.js" --include="*.ts" . 2>/dev/null

# Body parser size limits
grep -rn "body.parser\|express\.json\|express\.urlencoded\|limit" --include="*.js" --include="*.ts" . 2>/dev/null
# No size limit = memory exhaustion attack vector.

# Helmet (security headers)
grep -rn "helmet" package.json 2>/dev/null
```

## Django / Flask / FastAPI (Python)

```bash
# Django DEBUG setting
grep -rn "DEBUG\s*=" --include="*.py" --include="*.env" . 2>/dev/null

# Django SECRET_KEY hardcoded
grep -rn "SECRET_KEY\s*=\s*['\"]" --include="*.py" settings/ . 2>/dev/null

# Flask debug mode
grep -rn "debug\s*=\s*True\|app\.run.*debug" --include="*.py" . 2>/dev/null

# SQL raw queries
grep -rn "raw(\|execute(\|cursor\.\|\.extra(" --include="*.py" . 2>/dev/null

# CSRF disabled
grep -rn "csrf_exempt\|WTF_CSRF_ENABLED.*False\|CSRF.*False" --include="*.py" . 2>/dev/null

# Pickle usage (arbitrary code execution)
grep -rn "pickle\.\|cPickle\." --include="*.py" . 2>/dev/null

# FastAPI endpoints without auth dependency
grep -rn "@app\.\(get\|post\|put\|delete\)\|@router\.\(get\|post\|put\|delete\)" --include="*.py" . 2>/dev/null
# Check for Depends(get_current_user) or equivalent in each.
```

## Supabase

```bash
# Check if service_role key is exposed to client
grep -rn "service_role\|SERVICE_ROLE\|supabase.*service" --include="*.js" --include="*.ts" --include="*.jsx" --include="*.tsx" --include="*.env" src/ app/ public/ 2>/dev/null
# The service_role key bypasses RLS. It must NEVER be in client code.

# Check RLS status on tables
find . -name "*.sql" -exec grep -l "CREATE TABLE\|create table" {} \; 2>/dev/null
# For each table, check if RLS is enabled and policies exist.

# Check for direct table access without RLS
grep -rn "supabase.*from(\|\.from(" --include="*.js" --include="*.ts" src/ app/ 2>/dev/null
# If called from client code, RLS must be protecting these tables.

# Storage bucket policies
grep -rn "createBucket\|storage.*from\|storage.*upload" --include="*.js" --include="*.ts" src/ app/ 2>/dev/null
# Check if storage buckets have proper policies.
```

## Firebase

```bash
# Check security rules
cat firestore.rules 2>/dev/null
cat database.rules.json 2>/dev/null
cat storage.rules 2>/dev/null
# Look for: allow read, write: if true; (wide open)
# Look for: missing auth checks on sensitive collections

# Admin SDK in client code
grep -rn "firebase-admin\|admin\.firestore\|admin\.auth" --include="*.js" --include="*.ts" --include="*.jsx" --include="*.tsx" src/ app/ public/ 2>/dev/null
# Admin SDK must NEVER run in client code.

# API key restrictions
grep -rn "firebase.*apiKey\|firebase.*config" --include="*.js" --include="*.ts" --include="*.jsx" --include="*.tsx" src/ app/ 2>/dev/null
# Firebase API keys are designed to be public, but should be restricted by HTTP referrer in the Firebase Console.
```

## Docker / Docker Compose

```bash
# Running as root (no USER directive)
grep -n "USER" Dockerfile* 2>/dev/null
# If no USER directive exists, the container runs as root.

# Secrets in build args
grep -rn "ARG.*SECRET\|ARG.*KEY\|ARG.*PASSWORD\|ARG.*TOKEN" Dockerfile* 2>/dev/null
# Build args are visible in image history. Use runtime env vars or secrets.

# Privileged mode
grep -rn "privileged.*true\|cap_add\|security_opt" docker-compose* 2>/dev/null

# Published ports that should be internal
grep -A3 "ports:" docker-compose* 2>/dev/null
# Database ports should use "expose" (internal only), not "ports" (host-mapped).

# .env file referenced in docker-compose
grep -rn "env_file\|\.env" docker-compose* 2>/dev/null
# Make sure the .env file exists in .gitignore.
```

## Vercel / Netlify / Railway / Fly.io

```bash
# Check deployment config for environment variable handling
cat vercel.json netlify.toml fly.toml railway.json render.yaml 2>/dev/null

# Vercel: check for exposed serverless function routes
ls api/ app/api/ pages/api/ 2>/dev/null

# Netlify: check redirect rules for open redirects
grep -rn "redirect\|force\|from\|to" netlify.toml _redirects 2>/dev/null

# Check if build output includes source maps
grep -rn "sourceMap\|source-map\|devtool\|productionSourceMap" next.config.* vite.config.* webpack.config.* tsconfig.json 2>/dev/null
```

## Prisma ORM

```bash
# Check for raw queries (bypasses Prisma's built-in parameterisation)
grep -rn "\$queryRaw\|\$executeRaw\|\.raw(" --include="*.ts" --include="*.js" . 2>/dev/null
# Raw queries can be SQL injected. Ensure they use Prisma.sql template tag.

# Check schema for sensitive field exposure
cat prisma/schema.prisma 2>/dev/null
# Look for: password fields without @omit or select exclusion
# Look for: missing @@index on frequently queried fields (performance, not security)
```

## Stripe Integration

```bash
# Webhook signature verification
grep -rn "constructEvent\|webhookSecret\|STRIPE_WEBHOOK\|stripe.*webhook" --include="*.js" --include="*.ts" --include="*.py" . 2>/dev/null
# Must use stripe.webhooks.constructEvent() — not raw JSON.parse.

# Secret key exposure
grep -rn "sk_live\|sk_test" --include="*.js" --include="*.ts" --include="*.jsx" --include="*.tsx" src/ app/ public/ client/ 2>/dev/null
# Stripe secret keys must NEVER appear in client code.

# Client-side price manipulation
grep -rn "amount\|price\|cost\|total" --include="*.js" --include="*.ts" src/ app/ 2>/dev/null
# Prices must be set server-side (in Stripe Products/Prices or server code), never from client input.
```
