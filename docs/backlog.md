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
