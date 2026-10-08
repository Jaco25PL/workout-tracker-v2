# v1 Scope

Status: Accepted · 2026-10-08

## Problem
Logging a workout on a phone is slow and fiddly. Most apps hide the one thing you need mid-set: what did I do last time?

## Target user
A lifter who runs their own routine (3–5 gym days a week) and wants to log fast and see progress.
Multi-user from day one: anyone can sign up and has their own private data. All users have the same role, with no trainers or coaches (see ADR-0001). User #1 is the author.

## v1 features
1. **Auth.** Sign up / log in with email. Google login only if the chosen stack makes it cheap.
2. **Exercise library.** About 100 seeded common lifts, plus custom exercises per user.
3. **Routines.** Create and edit a routine: ordered exercises with target sets, reps and rest.
4. **Log a session.** Start from a routine or empty. Log weight × reps per set. Each set is prefilled with last session's values.
5. **Rest timer.** Starts automatically after a set is logged.
6. **History.** List of past sessions, plus history per exercise.
7. **PRs.** Heaviest weight at a rep count, and estimated 1RM. Flagged when hit.

Settings: kg/lb per user.

## Quality bars (must pass to ship)
- Logging a set takes **≤ 2 taps** when prefilled values are right.
- **A bad gym signal never loses a set.** UI updates instantly; writes queue and retry in the background.
- Works one-handed on a 375px-wide screen.

## Not in v1
- Native iOS/Android apps
- Trainer/coach accounts, roles, assigning routines to others
- Social: sharing, followers, feeds
- AI or generated programs
- Nutrition, body weight, wearables
- Supersets and circuits (the domain model must not block them later)
- Charts beyond one simple progress line per exercise
- Full offline mode (only queued writes are in scope)

## Rule
If a feature is not in the list above, it is not in v1. New ideas go to `docs/backlog.md`, not into code.
