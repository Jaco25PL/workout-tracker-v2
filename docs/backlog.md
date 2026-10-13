# Backlog (not in v1)

Ideas we want, parked so they don't leak into v1. Each needs its own design pass (and usually an ADR) before building.

## v1.1 — Points (gamification)
Goal: make the app reward showing up, so people don't leave the gym. Something different from common trackers.

Earning ideas (author's list, numbers TBD after 2 weeks of real use):
- Points for each completed session (with at least some working sets).
- More points for completing all planned exercises of the day; fewer when exercises are skipped.
- Points for PRs.
- Points for a verified gym visit (see location check-in below).
- Weekly points target, pre-calculated from the weekly goal (days) and a full, all-exercises week with some PRs.
- Bonus points when the weekly target is reached.

Design notes to resolve:
- Points must be **derived** from sessions, never stored as the truth (ADR-0003), so fixing a typo fixes points.
- PR points reward typos and ego lifting. Needs a guard (e.g. capped per session, only after the sanity warning is accepted).
- Skipping is sometimes right (pain, a busy machine). Keep the penalty small.
- A target equal to the maximum possible feels like failure. Consider a target below the max.

## v1.1 — Progression hint ("ready to go heavier")
Goal: tell the user when they've earned a weight increase, without changing their numbers for them.

- Rule idea (double progression): if **every working set** of an exercise last time hit the **top of the routine's rep range** (e.g. 3 × 12 on an 8–12 target), the next session flags it as ready to progress.
- **Prefill stays the same** (rule 7 is unchanged: 28 kg stays 28 kg). The user decides.
- UI: the prefilled weight is shown in a **success color with a small ↑ icon**; tapping it shows "Ready for 30 kg?" with a one-tap accept. Color is never the only signal (tokens rule); needs a new `--success` token with ≥ 4.5:1 contrast in all 4 themes.
- Suggested step: +2.5 kg (or +5 lb) by default; per-exercise step to decide later (dumbbells often jump by 2 kg).
- Derived, never stored (ADR-0003): computed from the last session's sets and the routine's targets.
- Open questions: exercises with no rep range (empty workouts), bodyweight and timed exercises, warm-up sets excluded.

## v2 — Friends and healthy competition
- Add friends, see their weekly progress, points and streaks. Compete to keep each other going.
- **Changes ADR-0001** ("users never see each other's data"). Needs a new ADR: friendship model, what is shared (points/streaks only? sessions?), privacy controls.
- With competition, cheating starts to matter, which is what justifies verification (below).

## v2 — Location check-in
- User marks their gym once. When a session starts, the app checks location; it checks again ~30 minutes later. Both inside the gym area → verified visit: points and automatic calendar completion.
- Feasible on the web **only while the app is open** (e.g. checks happen when a set is logged). Browsers have no background location or geofencing.
- Location is sensitive personal data: explicit opt-in, store only "verified yes/no" per session (not coordinates), comply with privacy law.
- GPS can be faked; acceptable for friends, not for prizes.

## Dropped for now
- Photo at the gym as proof. Metadata is easy to fake and often stripped on upload; storage cost and privacy.
