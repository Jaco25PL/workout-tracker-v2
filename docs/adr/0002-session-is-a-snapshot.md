# ADR-0002: A session is a snapshot of its routine

Status: Accepted · 2026-10-11

## Context
A session can start from a routine. Routines change over time (exercises added, removed, reordered).

## Decision
Starting a session **copies** the routine's exercises into `SessionExercise` rows. The session keeps an optional `routine_id` only as a reference ("started from Push Day"). It never reads the routine again.

## Consequences
- History is stable: editing a routine never rewrites past workouts, so PRs and progress stay true.
- The user can change a live session (skip, add, reorder) without touching the routine.
- Some data is duplicated (exercise list per session). That's cheap and intended.
- Deleting a routine sets `routine_id` to null on its sessions. Sessions are never deleted with it.
