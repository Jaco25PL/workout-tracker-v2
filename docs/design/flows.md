# User Flows (v1)

Status: Accepted · 2026-10-13 · rule numbers refer to `docs/domain.md`

The flows decide the screens; the screens decide the API (step 5). Tap counts assume prefilled values are right.

## Navigation
Bottom tab bar, always visible except during an active workout:

**Home · History · Routines · Settings**

During an active workout, the workout screen takes over. A floating "Back to workout · 32:10" bar shows on other tabs so it's always one tap away.

## Key screens
1. **Home** — next routine, weekly progress, streak.
2. **Workout** (active session) — the core screen.
3. **Finish summary**
4. **History** — calendar + session list, session detail, exercise history.
5. **Routine editor**
6. **Settings** — unit, weekly goal, account.
Plus: **Login** (email code), **Exercise picker** (sheet).

---

## F1. First login
1. Enter email → receive 6-digit code → enter code. (ADR-0007)
2. First time only: pick **unit** (kg default) and **weekly goal** (3 default). Timezone is read from the device.
3. → Home.

## F2. Home
Top to bottom:
- **"Next: Pull Day"** card with a big **Start** button (rule 27). Secondary: "Choose another" · "Empty workout".
- **This week:** e.g. "2 of 4 days" with the 7 days of the week; trained days filled. Any days count (rule 23).
- **Streak:** e.g. "5 weeks". Tap → calendar.
- If a session is active, the top card becomes **"Continue workout · 32:10"** instead (rule 4).

## F3. Start a workout — 1 tap
1. Tap **Start** on the suggested routine → the session is created as a snapshot (rule 3) → Workout screen.
   - Alt: "Choose another" → routine list → tap one.
   - Alt: "Empty workout" → Workout screen with no exercises → add from picker.

## F4. Log a set — 1 tap (core loop)
Workout screen layout:
- **Header:** workout clock (counts up, rule 10), routine name, **Finish** button.
- **List of exercises.** The current one is expanded; others collapsed with a progress hint ("2/3 sets").
- Expanded exercise shows rows: set number · previous (last session) · weight · reps · ✓.

Loop:
1. Values are **prefilled** (rule 7). Tap **✓** → set saved locally, synced in background (ADR-0008). **1 tap.**
2. Wrong value? Tap the number → numeric keypad with ± steppers (e.g. ±2.5 kg, ±1 rep) → ✓. **2–3 taps.**
3. If the value trips the **sanity warning** (rule 8): inline "That's way above your best (100 kg). Correct?" → Yes / Fix. Never blocks silently.
4. After ✓, the **rest clock** appears (counts up from 0:00, rule 11) as a pill above the tab area. No countdown, no alarm.
5. Last set of an exercise done → that exercise collapses and the next one expands automatically.
6. A PR shows a small badge on the set row (rule 15–18). Celebration is saved for the finish screen.

Set row actions (swipe left or long-press): mark as warm-up, delete. Undo toast for 5 seconds after delete.

Per tracking type (ADR-0005): weighted = weight + reps · bodyweight = reps (+ added weight) · timed = duration (+ distance).

## F5. Change the workout on the fly (rule 5)
- **Add exercise:** "+ Add exercise" at the end of the list → picker sheet (search, recent first, filter by muscle) → added.
- **Reorder:** drag handle on collapsed exercises.
- **Skip:** swipe exercise → Skip. It stays in the list, greyed. Can be undone.
- **Add / remove a set:** "+ Set" under the rows (copies the last set's values).

## F6. Finish
1. Tap **Finish** → confirm sheet. If exercises are unfinished: "3 sets not done. Finish anyway?"
2. → **Finish summary:** duration, total sets, PRs hit (listed), "3 of 4 days this week", streak status. Small celebration if a PR or the weekly goal was hit.
3. Optional note → **Done** → Home.

Also: **Discard workout** (in the Workout header menu) → confirm → deleted (rule 6).

## F7. History
- **Calendar** (month view): trained days marked (rule 26). Week rows show goal met.
- Below: list of sessions, newest first. Tap → **session detail** (exercises, sets, PR badges, rest gaps derived from timestamps, rule 12). Edit or delete from there.
- Tap an exercise name anywhere → **exercise history**: past sessions, best sets, one simple progress line.

## F8. Routines
- List of routines **in rotation order** (rule 27); drag to reorder the rotation.
- **Create / edit:** name, then "+ Add exercise" → picker. Smart insert puts it after the last exercise of the same muscle (rule 2). Drag to reorder.
- Per exercise: target sets, target reps range (or duration), from simple steppers.
- Delete routine → confirm. Past sessions keep their data (ADR-0002).

## F9. Settings
Unit (kg / lb) · weekly goal (rule 24) · theme follows the system · email · log out · delete account.

## Offline / sync states (ADR-0008)
- No signal: everything in F3–F6 works. A small "Offline · 4 sets waiting" pill in the header.
- Back online: syncs silently; the pill disappears.
- Login (F1) is the only flow that needs the network.
