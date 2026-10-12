# ADR-0005: Exercise tracking types in one Set table

Status: Accepted · 2026-10-11

## Context
v1 must log weighted lifts, bodyweight moves (pull-ups, dips) and timed work (bike, plank). Each needs different fields.

## Decision
- Each Exercise has a `tracking_type`: `weighted`, `bodyweight` or `timed`.
- One `Set` table with nullable fields (`weight_kg`, `reps`, `duration_s`, `distance_m`).
- A DB check constraint enforces which fields are required or must be null for each type (see `docs/domain.md`).

Rejected: a separate table per type. More joins and more code paths for little gain.

## Consequences
- One simple query for history, prefill and the session screen.
- Nullable columns are only safe because the DB enforces the rules. The UI alone isn't enough.
- Adding a type later (e.g. `distance` for running) means a new enum value, a constraint update and a migration.
