# ADR-0001: One user role, data owned by one user

Status: Accepted · 2026-10-08

## Context
v1 targets lifters who run their own routines. We considered trainer accounts that assign routines to clients.

## Decision
v1 has a single user role. Every routine, session, set and custom exercise belongs to exactly one user. No sharing between users.

## Consequences
- Simpler auth, schema and API: every query is scoped by `user_id`, no permission checks beyond "is this yours".
- Row-level ownership is the whole security model, so it must be enforced at the data layer (for example Postgres RLS or one shared query helper), not left to each endpoint.
- Adding trainers later means a new relationship (trainer ↔ client) and permission rules. Keeping `user_id` as the owner on every row means that change adds to the model instead of rewriting it.
