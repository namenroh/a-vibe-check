# Deployment Guide — A Vibe Check

## Step 1: Create the GitHub Repo

```bash
cd a-vibe-check-repo

git init
git add .
git commit -m "A Vibe Check — security audit + expert marketplace for AI-built apps"
gh repo create namenroh/a-vibe-check --public --source=. --push
```

Or if you prefer to create the repo on GitHub first:
1. Go to https://github.com/new
2. Name: `a-vibe-check`
3. Public, no template, no README (we have one)
4. Then push:

```bash
git init
git add .
git commit -m "A Vibe Check — security audit + expert marketplace for AI-built apps"
git remote add origin https://github.com/namenroh/a-vibe-check.git
git branch -M main
git push -u origin main
```

## Step 2: Deploy Website to Vercel

### Option A: Vercel CLI (fastest)

```bash
cd website
npx vercel --prod
```

When prompted:
- Set up and deploy: **Y**
- Scope: your account
- Link to existing project: **N**
- Project name: `a-vibe-check`
- Directory: `./`
- Override settings: **N**

### Option B: Vercel Dashboard

1. Go to https://vercel.com/new
2. Import Git Repository → select `namenroh/a-vibe-check`
3. Root Directory: `website`
4. Framework Preset: Other
5. Build Command: (leave empty)
6. Output Directory: `.`
7. Deploy

## Step 3: Connect Custom Domain (not yet active)

The custom `avibecheck.xyz` domain is not wired up yet. The live site currently serves from the Vercel subdomain (`a-vibe-check.vercel.app`). When ready to cut over:

1. In Vercel dashboard → your project → Settings → Domains
2. Add `avibecheck.xyz`
3. Vercel will give you DNS records to set:
   - If using Vercel nameservers: point your domain registrar's nameservers to Vercel's
   - If using A/CNAME: add the records at your registrar
   - Typically: `A` record → `76.76.21.21` and `CNAME` for `www` → `cname.vercel-dns.com`
4. SSL is automatic once DNS propagates

## Step 4: Verify

- The Vercel deployment URL (currently `https://a-vibe-check.vercel.app`) should load the site
- Once the custom domain is live, `https://avibecheck.xyz` should also resolve
- Test the scanner with a public repo
- Check security headers are applied (vercel.json)

## File Structure in Repo

```
a-vibe-check/
  README.md          ← GitHub storefront
  LICENSE            ← MIT
  CHANGELOG.md       ← Version history
  SKILL.md           ← The actual 30-point audit skill
  scoring.md         ← Scoring methodology
  compliance.md      ← Compliance framework mapping
  frameworks.md      ← Framework-specific patterns
  expected-demo-output.md
  website/
    index.html       ← Landing page + live scanner
    vercel.json      ← Vercel config + security headers
```
