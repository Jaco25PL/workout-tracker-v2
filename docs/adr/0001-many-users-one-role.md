# ADR-0001: Many users, one role (no trainers)

Status: Accepted · 2026-10-08

## Context
The app is multi-user: anyone can sign up, log in on several devices, and keep their own data.
We considered adding trainer accounts that assign routines to clients.

## Decision
- Many users, **one role**. Every account is the same kind of user. No trainer, coach or admin accounts in v1.
- Every routine, session, set and custom exercise belongs to exactly one user. Users never see each other's data.

## Consequences
- Simpler auth, schema and API: every query is scoped by `user_id`. The only permission check is "is this yours?"
- That ownership check is the whole security model, so it is enforced in one place at the data layer (for example Postgres RLS or one shared query helper), not left to each endpoint.
- Adding trainers later means a new trainer ↔ client relationship and permission rules. Keeping `user_id` as the owner on every row means that change adds to the model instead of rewriting it.
