# ADR-0004: Client-generated IDs and idempotent writes

Status: Accepted · 2026-10-11

## Context
Quality bar: a bad gym signal never loses a set. The UI must update instantly, and writes must queue and retry in the background. A retry must never create a duplicate set.

## Decision
- Every entity ID is a **UUID created on the client** at the moment the user acts.
- Every write is **idempotent**: create = upsert by ID. Sending the same write twice has the same result as sending it once.
- Edits are last-write-wins, using the client's `updated_at`.

## Consequences
- The UI can show and reference a set before the server has seen it.
- The write queue can retry blindly until it gets a success. No duplicate sets.
- The server must still check ownership on every upsert (ADR-0001). A client-chosen ID is never trusted to belong to the caller.
- Last-write-wins is fine for one person on one or two devices. Real multi-device conflicts would need more; out of scope for v1.
- Use UUIDv7 (time-ordered) for better index performance.
