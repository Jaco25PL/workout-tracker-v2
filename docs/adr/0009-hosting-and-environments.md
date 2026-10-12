# ADR-0009: Hosting on Cloudflare Workers + Neon, at $0

Status: Accepted · 2026-10-12

## Context
Budget is $0 per month, permanently. The app must not sleep for minutes, and the database must not expire. Users are near Uruguay.

## Decision
- **Cloudflare Workers (free plan)** runs the Hono API and serves the built app as static assets, on **one domain**. Free plan: 100,000 requests/day (per account), static asset requests free and unlimited, 10 ms CPU per request.
- **Neon Postgres (free plan)** in **`aws-sa-east-1` (São Paulo)**. Free plan: 1 GB storage per project, 100 CU-hours/month per project, scales to zero after 5 minutes idle.
- **Environments:**
  - **Local:** Postgres in Docker Compose + Workers dev server.
  - **Preview:** one deploy per PR, with its own Neon branch.
  - **Production:** main branch only.
- How the Worker connects to Postgres (Neon serverless driver or a pooled `pg` connection) is decided in step 6, so local Docker Postgres and Neon work the same way.

## Options rejected
- **AWS free plan:** since July 2025 it lasts 6 months (or until credits run out), and we'd manage the server ourselves.
- **Render free:** web services sleep after 15 minutes (about 1 minute to wake), and free Postgres is deleted after 30 days.
- **Vercel Hobby:** personal, non-commercial use only.
- **Fly.io / Railway:** no lasting free tier.
- **Docker in production:** free container hosts sleep. Docker stays for local dev and CI.

## Consequences
- $0 with no sleeping app server. The first query after 5 idle minutes waits for Neon to wake. Measure in step 7.
- Same domain means no CORS and first-party cookies (ADR-0007).
- 10 ms CPU per request is the main constraint: heavy work never runs in a request.
- Free tiers can change. Mitigation: portable stack (Hono runs on Node, standard Postgres), so moving costs a config change. Review provider limits before launch (step 10).
- Other apps should get their own Worker and Neon project. The Workers request limit is shared per account.

## Sources (checked 2026-10-12)
- https://developers.cloudflare.com/workers/platform/pricing/
- https://neon.com/docs/introduction/plans
- https://neon.com/docs/introduction/regions
- https://render.com/docs/free
- https://vercel.com/docs/plans/hobby
- https://aws.amazon.com/about-aws/whats-new/2025/07/aws-free-tier-credits-month-free-plan/
