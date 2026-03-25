# Payload + Vercel Deployment Guide (Safe Workflow)

[![Vercel](https://img.shields.io/badge/Deploy-Vercel-black?logo=vercel)](https://vercel.com)
[![Payload CMS](https://img.shields.io/badge/CMS-Payload-blue)](https://payloadcms.com)
[![Next.js](https://img.shields.io/badge/Next.js-13+-black?logo=next.js)](https://nextjs.org)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

These are my observations and consolidated notes for launching a Payload CMS site using the Vercel one-click starter — without running into migration or database drift issues. I developed this process with a lot of help from ChatGPT while launching my personal website at www.spinkscorners.com as a test project using Payload starting with the Vercel click to deploy.

---

## 🚨 The Golden Rule

> **Never run local Payload dev mode against your Vercel production database.**

Doing so causes schema drift and breaks migrations, often resulting in stalled Vercel builds.

---

## 🧭 Workflow Overview

| Environment | Database |
|------------|--------|
| Local dev  | Local Postgres (Docker) |
| Vercel     | Hosted Postgres |

| Step | Action |
|-----|-------|
| 1 | Develop locally |
| 2 | Generate migration |
| 3 | Commit migration |
| 4 | Deploy (Vercel runs migrations) |

---

## 🚀 Quick Start

```bash
git clone <your-repo>
cd <repo>
cp .env.example .env
docker-compose up -d
pnpm install
pnpm dev
```

Then open:

```
http://localhost:3000/admin
```

---

## 🔴 Critical Setup

In `.env`:

```
POSTGRES_URL=postgres://postgres@localhost:54320/mydb
```

**Do NOT use your Vercel database locally.**

---

## 🧬 Migrations (Most Important Step)

Before deploying:

```bash
pnpm payload migrate:create
pnpm exec tsc --noEmit
git add .
git commit -m "Add migration"
git push
```

---

## ⚙️ Vercel Configuration

Set Build Command:

```bash
payload migrate && pnpm build
```

---

## 🚀 Deploy

- Push changes OR click redeploy
- Then visit:

```
https://your-site.com/admin
```

Create your **production admin user**

---

## 🔒 Environment Separation

| Type | Notes |
|------|------|
| Local DB | Disposable sandbox |
| Production DB | Persistent and protected |

---

## 🧪 Troubleshooting

### Build stuck on migrations
Fix:
- create migration locally
- commit it
- redeploy

### `/admin` blank locally
Fix:
- use `localhost`, not `127.0.0.1`

### DATABASE_URL locked
Fix:
- remove old DB integration
- attach new one

---

## 📦 Project Structure

```
src/
├── app/
├── collections/
├── globals/
├── components/
└── payload.config.ts
```

---

## 📘 Documentation

- Full step-by-step workflow → see `/docs` or Issues
- Payload Docs: https://payloadcms.com/docs
- Vercel Docs: https://vercel.com/docs

---

## ✅ Checklist

- [ ] Local DB is NOT production
- [ ] Migration created and committed
- [ ] Build runs migrations
- [ ] Deployment succeeds
- [ ] Admin user created

---

## 🧠 Core Principle

> Local = sandbox  
> Production = protected  

This prevents 90% of deployment issues.

---

## 📄 License

MIT
