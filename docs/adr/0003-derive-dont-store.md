# ADR-0003: Store facts, derive the rest (PRs, rest times, clocks)

Status: Accepted · 2026-10-11

## Context
PRs, rest times and live clocks could be stored as their own values. Typos are common at the gym (20 reps typed instead of 11). An earlier idea was a 20-minute delay before saving PRs, so mistakes could be fixed first.

## Decision
Store only facts: sets with their values and `completed_at`, and the session's `started_at` / `finished_at`. Everything else is **calculated when needed**:
- PRs: from the user's working sets.
- Rest time: the gap between consecutive `completed_at` values.
- Workout clock and rest clock: `now − timestamp`, never a counter running in the browser.

Typos are caught by a sanity warning before saving, and fixed by normal edit/undo.

## Consequences
- Fixing a typo fixes the PR instantly. No delay, no cleanup job, no stale data.
- Clocks stay correct after the phone locks, the tab reloads or the browser throttles timers.
- PR detection runs a query on each logged set. Fine at this size with an index on `(user_id, exercise_id)`. If it ever gets slow, add a cache that can be rebuilt from sets, never a source of truth.
- PR logic is pure functions, so it's covered by TDD unit tests.
