# ADR-0006: TypeScript end to end, own API, managed Postgres

Status: Accepted · 2026-10-12

## Context
Priority is a solid app over a fast one, at a $0 budget. Domain rules (PRs, prefill, sanity warning, weekly goal) must run on the phone for instant, offline feedback, and on the server to validate data.

## Decision
- **Language:** TypeScript everywhere.
- **Monorepo (pnpm):** `apps/web`, `apps/api`, `packages/contract` (zod schemas), `packages/domain` (pure rules, built test-first).
- **API:** Hono. Our own API owns the domain rules, ownership checks (ADR-0001) and idempotent upserts (ADR-0004).
- **Frontend:** React + Vite single-page app, installable as a PWA.
- **Database:** Postgres (managed by Neon, see ADR-0009). Drizzle ORM with plain SQL migration files.
- **Contract:** zod schemas shared by API (validation) and web (types).

## Options rejected
- **Supabase called directly from the browser:** rules would be split between client code and DB policies. Harder to test in one place.
- **Python (FastAPI):** a great backend, but the domain rules would have to be written twice (Python on the server, TypeScript on the phone) and kept in sync forever.
- **Next.js:** its strengths are server rendering, SEO and public pages. This app is behind login, offline-first and client-heavy, so we'd pay its complexity without using its strengths.

## Consequences
- One `packages/domain` is tested once and used by both sides.
- Hono and Postgres are portable: the API can move from Workers to a Node container without a rewrite.
- More code to own than a Backend-as-a-Service. Accepted for control and testability.
