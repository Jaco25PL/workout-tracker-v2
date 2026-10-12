# Engineering Guide

Status: Accepted · 2026-10-12 · see ADR-0010

How code is written in this repo. Claude Code writes most of it, so every rule here is either **enforced by a tool** or **shown in the reference slice**. If it's neither, it's a wish.

## 1. Methods
Only two:

### Spec first (SDD)
Every feature starts with a short spec in `docs/specs/NNN-feature-name.md` before any code:

```md
# Spec: Log a set
Goal: one sentence.
Rules: domain.md #7, #8, #15–17
Examples (input → output):
- best 100×5, log 100×5 → saved, not a PR (tie)
- best 100×5, log 102.5×5 → saved, PR
- best 100×5, log 140×5 → sanity warning, not saved until confirmed
Out of scope: anything not listed.
```

- Examples are concrete values, not prose. They become tests 1:1.
- No Gherkin, no Given/When/Then tooling.
- The spec is the contract with Claude Code: plan from it, then code.

### Test first (TDD), where it pays
- **Required:** `packages/domain` (PRs, 1RM, prefill, sanity warning, weekly goal, streak) and **every bug fix** (failing test first, then fix).
- **Not required:** UI components and plain glue code.
- Test names say the behaviour: `logSet: tie with best is not a PR`.

The shared vocabulary in `domain.md` is the only DDD we use: same words in docs, code, DB and UI.

## 2. Architecture
**Modular monolith, grouped by feature, functional core + imperative shell.**

```
packages/
  domain/      pure rules. No I/O, no DB, no React. Runs on phone and server.
  contract/    zod schemas shared by api and web.
apps/
  api/src/<feature>/   routes → use cases → repos
  web/src/<feature>/   hooks (logic) → components (render)
```

- **Group by feature** (`sessions/`, `routines/`, `exercises/`, `stats/`), never by type (`controllers/`, `models/`).
- **Dependency direction:** `web`/`api` → `contract`/`domain`. `domain` imports nothing from the app.

## 3. Design patterns
A few patterns, used the same way everywhere.

| Pattern | Where | Rule |
|---|---|---|
| **Functional core / imperative shell** | `packages/domain` vs `apps/*` | Rules are pure functions. I/O happens around them, never inside. |
| **Use case** | `apps/api/src/<feature>/use-cases/` | One function per user action (`logSet`, `finishSession`, `setWeeklyGoal`). Routes stay thin: parse → call use case → respond. |
| **Repository** | `apps/api/src/<feature>/repo.ts` | One per aggregate (`sessionRepo`, `routineRepo`). **Every query takes `userId` and scopes by it.** Use cases never touch the DB directly (ADR-0001). |
| **Strategy** | `packages/domain/tracking/` | One strategy per tracking type (`weighted`, `bodyweight`, `timed`) with `validate`, `isPr`, `prefill`. Look up by type; no repeated `switch`. |
| **Dependency injection (plain parameters)** | Use cases | Use cases receive `{ repo, clock, ids }`. Never call `Date.now()` or `crypto.randomUUID()` directly; tests pass a fake clock. No DI framework. |
| **Adapter** | `apps/api/src/infra/` | DB driver, email provider. Swapping a provider touches one file (ADR-0009). |
| **Command** | `apps/web/src/sync/outbox` | Every write is a serialisable command with a client UUID, replayed until confirmed (ADR-0004, ADR-0008). |
| **Result type** | Domain + use cases | Expected failures (`SESSION_ALREADY_ACTIVE`, `INVALID_SET`) are returned as values. Exceptions only for bugs. |

**Frontend:** one custom hook per feature holds the logic (`useActiveSession`, `useLogSet`); components only render props. The active session uses a reducer.

### Principles
- Composition over inheritance. No class hierarchies.
- Single responsibility: a file does one thing for one feature.
- **Rule of three:** no shared abstraction until the third copy.
- Validate at every boundary with zod (API input, data read from IndexedDB).

### Avoid
- Generic `BaseRepository<T>` or base classes.
- Singletons and global mutable state.
- Event buses / domain events (derive instead, ADR-0003).
- Abstractions "for later", `utils/` dumping grounds, `any`.

## 4. Reference slice
The walking skeleton (step 7) is built to follow every rule above. It becomes **the example Claude Code copies** for new features. New code should look like it.

## 5. Where each rule is enforced
| Place | Contents |
|---|---|
| `CLAUDE.md` | ~5 lines: spec first, test first for domain, link this file, link the reference slice. |
| Path rules (`.claude/rules/`) | `packages/domain/**`: pure, test first. `apps/api/**`: thin routes, repos scope by `userId`. `apps/web/**`: logic in hooks. |
| Hooks + CI | Typecheck, lint, tests, **import boundaries** (`domain` can't import DB/React/apps). Failing = blocked. |
| Skills | `/new-feature`: spec → tests → code → `/verify`. `/review` checks this guide. |

Built in step 6.
