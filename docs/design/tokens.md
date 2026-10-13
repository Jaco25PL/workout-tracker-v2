# Design Tokens

Status: Accepted · 2026-10-13 · source: mockups canvas "Workout Tracker Mockups" · ADR-0011

**Highest design authority** (ADR-0011 order: these tokens → `emil-design-eng` → `frontend-design`). Implement as CSS custom properties; components never use raw hex values.

## Themes
Two independent user settings, so 4 combinations, all supported:

| Setting | Options | Default |
|---|---|---|
| `appearance` | `system` · `light` · `dark` | `system` (follows the phone) |
| `accent` | `blue` · `orange` | `blue` |

Implementation: `<html data-appearance="…" data-accent="…">`. `system` uses `prefers-color-scheme`. The last choice is cached locally so the first paint is right (no flash), then synced from the user's settings.

## Color

### Base
| Token | Light | Dark | Use |
|---|---|---|---|
| `--bg` | `#F2F2F7` | `#000000` | Screen background |
| `--surface` | `#FFFFFF` | `#1C1C1E` | Cards, grouped lists, inputs |
| `--surface-2` | `#EEEEF3` | `#2C2C2E` | Editable number cells, secondary buttons, empty day dots |
| `--text` | `#111114` | `#F5F5F7` | Primary text |
| `--text-2` | `#6B6B70` | `#A1A1A8` | Secondary text, captions, inactive tabs |
| `--separator` | `#E3E3E8` | `#2C2C2E` | Row dividers, tab bar border |
| `--control-border` | `#8E8E93` | `#8E8E93` | Unchecked set button outline (≥ 3:1) |
| `--inverse-bg` | `#111114` | `#F5F5F7` | Rest pill |
| `--inverse-text` | `#FFFFFF` | `#111114` | Text on inverse |
| `--tab-bg` | `rgba(249,249,251,0.94)` | `rgba(22,22,24,0.94)` | Tab bar |

### Accent
| Token | Blue · light | Blue · dark | Orange · light | Orange · dark | Use |
|---|---|---|---|---|---|
| `--accent-fill` | `#1D63D6` | `#1D63D6` | `#C4410E` | `#C4410E` | Primary buttons, done checks, trained days. Always white text on top |
| `--accent-text` | `#1D63D6` | `#6CA6FF` | `#C4410E` | `#FF9152` | Links, text buttons, highlights, active tab |
| `--accent-tint` | accent-text @ 10% | accent-text @ 20% | accent-text @ 10% | accent-text @ 20% | PR badge, icon circles |
| `--accent-row` | accent-text @ 6% | accent-text @ 12% | accent-text @ 6% | accent-text @ 12% | Current set row |
| `--on-accent` | `#FFFFFF` | | | | Text/icons on `--accent-fill` |

Why the accents are darker than Apple's stock blue/orange: white text on them must reach 4.5:1 (blue ≈ 5.5:1, orange ≈ 5.1:1). Dark mode uses a lighter `--accent-text` for text on dark surfaces.

Rules:
- Text ≥ 4.5:1 (≥ 3:1 at 24 px+). Controls and focus rings ≥ 3:1.
- Never convey state by color alone: done sets also show a check, PRs also show the "PR" label.

## Typography
Font: **Inter** (self-hosted, Latin subset), weights 400/500/600/700. Fallback `system-ui, sans-serif`. **`font-variant-numeric: tabular-nums` on the whole app** so clocks and weights don't jitter.

| Token | Size / weight / tracking | Use |
|---|---|---|
| `--type-large-title` | 34 / 700 / −0.025em | Screen titles ("Today") |
| `--type-title` | 26 / 700 / −0.02em | Hero names ("Pull Day"), finish title |
| `--type-clock` | 28 / 600 / −0.02em | Workout clock |
| `--type-stat` | 22 / 600 / −0.01em | Stat tiles, rest time (20) |
| `--type-headline` | 17–18 / 600 | Card titles, current exercise, primary buttons |
| `--type-body` | 16 / 500 | List rows, inputs |
| `--type-callout` | 15 / 400–600 | Secondary lines, text buttons, set values (17/600) |
| `--type-subhead` | 13 / 400–600 | Meta lines, helper text |
| `--type-caption` | 11–12 / 600 / +0.04–0.06em, uppercase | Section labels ("NEXT IN ROTATION", "SET") |
| `--type-tab` | 11 / 500 (600 active) | Tab bar labels |

Inputs are ≥ 16 px so iOS doesn't zoom on focus.

## Spacing
4-based scale: `4 · 6 · 8 · 10 · 12 · 14 · 16 · 20 · 24 · 28 · 32`.
- Screen side padding: 16. Gap between cards: 12–16. Card padding: 16–20.
- Grouped list rows: min height 56 (single line) / 72 (two lines with icon).

## Size & shape
| Token | Value | Use |
|---|---|---|
| `--touch-min` | 44 | Every tappable target |
| `--button-h` | 44 | Secondary buttons, header buttons |
| `--button-h-primary` | 54 | Start workout, Done |
| `--set-row-h` | 52 | Set rows |
| `--radius-sm` | 10 | Number cells, small buttons |
| `--radius-md` | 12 | Inputs |
| `--radius-lg` | 14 | Primary buttons, stat tiles (16) |
| `--radius-xl` | 20 | Cards |
| `--radius-full` | 999 | Pills, circles |
| `--tab-bar-h` | 84 (incl. safe area) | Bottom navigation |

Elevation: only floating elements get a shadow (`0 8px 24px rgba(0,0,0,0.18)`, rest pill). Cards are flat on `--bg`.

Icons: Lucide, 2 px stroke, 20–24 px (16–18 inside buttons), `currentColor`.

## Motion (from `emil-design-eng`, ADR-0011)
| Token | Value |
|---|---|
| `--ease-out` | `cubic-bezier(0.23, 1, 0.32, 1)` — UI enter/feedback |
| `--ease-in-out` | `cubic-bezier(0.77, 0, 0.175, 1)` — on-screen movement |
| `--ease-drawer` | `cubic-bezier(0.32, 0.72, 0, 1)` — sheets |
| `--dur-press` | 120 ms |
| `--dur-fast` | 160 ms (popovers, toasts) |
| `--dur-base` | 240 ms (collapse/expand, general UI ≤ 300) |
| `--dur-sheet` | 360 ms enter / 240 ms exit |
| `--press-scale` | 0.97 on `:active` |

- Logging a set: press feedback + check fill only. No layout animation on frequent actions.
- Delight only on rare moments: PR badge appear, finish screen, weekly goal met.
- Hover styles only under `@media (hover: hover) and (pointer: fine)`.
- `prefers-reduced-motion`: keep opacity/color, remove movement and scale.
