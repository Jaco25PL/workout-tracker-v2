# ADR-0011: UI direction — Apple-like, Emil Kowalski's skill has the final say

Status: Accepted · 2026-10-13

## Context
The app should feel like a polished native iPhone app, in light and dark mode, and look the same on every phone. Claude Code will build the UI with two design skills that can disagree:
- **Anthropic `frontend-design`:** overall visual direction, typography, avoiding a generic "AI look". Leans bold and distinctive.
- **Emil Kowalski `emil-design-eng`:** animation, easing and polish. Leans restrained.

## Decision
- **Precedence: `emil-design-eng` wins** over `frontend-design` whenever they conflict. Our own tokens (`docs/design/tokens.md`, after mockups) beat both. Order: our tokens → Emil → frontend-design.
- **Look:** Apple-like. Quiet surfaces, large titles, grouped lists, one accent color, generous touch targets (≥ 44 px).
- **Theme:** light and dark, following the phone setting.
- **Font: Inter, self-hosted**, the same on every phone. Emil's skill doesn't specify a font. Inter is open source (SIL OFL), screen-optimised, close to Apple's style, and has **tabular numbers** so weights and clocks don't jump. Cached by the service worker, so it works offline.
- **Icons: Lucide** (open source).
- **Not used:** Apple's SF Pro font and SF Symbols. Apple licenses them only for apps on Apple platforms, not websites.
- **Motion (from Emil's skill):**
  - Custom easing, never ease-in. UI: `cubic-bezier(0.23, 1, 0.32, 1)`. Sheets/drawers: `cubic-bezier(0.32, 0.72, 0, 1)`.
  - Durations: press feedback 100–160 ms, popovers 125–200 ms, sheets 200–500 ms, general UI under 300 ms. Exits faster than enters.
  - **Frequent actions barely animate.** Logging a set (tens of times per session) gets press feedback only. Delight is reserved for rare moments: a PR, finishing, hitting the weekly goal.
  - Press feedback: scale ~0.97 on `:active`. Hover effects only on devices that really hover.
  - **Reduced motion = fewer and gentler**, not zero: keep opacity/color, drop movement.
- **Haptics:** none. iOS doesn't support vibration from web apps.
- **Language:** English, i18n-ready from day one. All UI text through translation keys; dates and numbers via `Intl` for the user's locale.

## Consequences
- One clear tie-breaker for Claude Code, written in `.claude/rules/` for `apps/web/**` in step 6.
- Self-hosting Inter adds a small font download (subset to Latin to keep it light).
- Both skills are installed into `.claude/skills/` in step 6.
