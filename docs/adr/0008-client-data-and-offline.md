# ADR-0008: Client data — optimistic UI with an IndexedDB outbox

Status: Accepted · 2026-10-12

## Context
Quality bar: a bad gym signal never loses a set, and logging feels instant. ADR-0004 makes writes safe to retry.

## Decision
- **Writes** go to an **outbox in IndexedDB** first, then the UI updates immediately. A background loop sends them in order and retries until the server confirms. Survives reloads and closed tabs.
- **Reads** use **TanStack Query**, with cached data shown while fresh data loads.
- A **service worker** caches the app shell, so the app opens even with no signal.
- Domain rules from `packages/domain` run on the client for instant feedback (PR flag, prefill, sanity warning) and again on the server as the source of truth.

## Options rejected
- **Full offline sync engine** (local-first database): out of v1 scope, heavy for the need.
- **Writes straight to the API:** fails the quality bar on bad signal.

## Consequences
- The outbox is custom code, but small, and it's the heart of the quality bar, so it's covered by tests.
- The UI must show sync state clearly (e.g. a small "3 sets waiting to sync" badge).
- Not full offline: browsing history with no signal shows only what's cached.
