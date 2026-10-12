# Domain Model

Status: Accepted · 2026-10-11

Use these exact words in code, DB tables, API and UI copy. One concept, one name.

## Entities

```
User
 ├── Exercise (custom, owned)          Exercise (global, seeded, owner = null)
 ├── Routine
 │    └── RoutineExercise  (ordered)  → Exercise
 └── Session
      └── SessionExercise  (ordered)  → Exercise
           └── Set          (ordered)
```

| Entity | Key fields |
|---|---|
| **User** | email, `unit` (`kg` default, or `lb`) |
| **Exercise** | name, `tracking_type`, `muscle_group`, equipment, `owner_id` (null = global), `archived_at` |
| **Routine** | name, `owner_id` |
| **RoutineExercise** | exercise, `position`, target sets, target reps (min–max) or target duration, `group_id` (unused in v1) |
| **Session** | `started_at`, `finished_at` (null = active), optional `routine_id`, notes |
| **SessionExercise** | exercise, `position`, `group_id` (unused in v1) |
| **Set** | `position`, `kind` (`warmup` / `working`), `weight_kg`, `reps`, `duration_s`, `distance_m`, `completed_at` |

Every row is owned by one user (ADR-0001). All IDs are UUIDs created on the client (ADR-0004).

## Tracking types (ADR-0005)

| `tracking_type` | Required on a Set | Optional | PR in v1 |
|---|---|---|---|
| `weighted` | `weight_kg`, `reps` | — | heaviest weight at a rep count; best e1RM |
| `bodyweight` | `reps` | `weight_kg` (added weight) | most reps |
| `timed` | `duration_s` | `distance_m` | none |

Fields that don't apply to the type must be null. Enforced in the DB, not just the UI.

## Rules

### Routines
1. Order is manual (`position`). The app never reorders on its own.
2. **Smart insert:** a new exercise goes right after the last exercise with the same `muscle_group`. No match → at the end.

### Sessions
3. **A session is a snapshot** (ADR-0002). Starting from a routine copies its exercises. Later routine edits never change past sessions.
4. **One active session per user.** Starting a new one requires finishing or discarding the current one.
5. During a session, exercises can be added, removed, reordered or skipped freely.
6. Discarding a session deletes it. A finished session can be edited or deleted later.

### Sets
7. **Prefill:** same exercise, same set `position`, from the most recent *finished* session (any routine). No history → routine targets. Nothing → empty.
8. **Sanity warning** before saving when, versus this user's best for the exercise: weight ≥ 130%, or reps ≥ 2×. Only warns, never blocks. Thresholds are tunable.
9. Weight is stored in **kg with 3 decimals** and shown in the user's unit, rounded to 0.5 kg / 1 lb for display.

### Clocks (ADR-0003)
10. **Workout clock** = `now − session.started_at`. Counts up until the session is finished.
11. **Rest clock** = `now − completed_at of the last set in the session`. Logging a set *is* the lap. Before the first set, no rest clock.
12. Rest history is derived: the gap between consecutive `completed_at` values. Nothing is stored.
13. No pause in v1.
14. `started_at` and `completed_at` come from the **device clock** (what the user lived). Server-side `created_at` / `updated_at` are kept separately for audit and sync.

### PRs (ADR-0003)
15. PRs are **calculated from sets, never stored.** Editing or deleting a set fixes PRs instantly.
16. Only `working` sets count. A tie is not a PR.
17. A set is a PR when it beats every other working set for that exercise by this user that was completed before it.
18. **Estimated 1RM** uses Epley: `weight × (1 + reps / 30)`, only for 1–12 reps. For 1 rep, e1RM = weight.

### Exercises
19. An exercise used in any session or routine is **archived, never deleted.** Archived exercises are hidden from pickers but stay in history.
20. Users can't edit global exercises. They can create a custom copy.

## Not modelled in v1 (but not blocked)
- Supersets / circuits: `group_id` exists, unused.
- PRs for timed exercises, pause on the workout clock, trainers (ADR-0001).
