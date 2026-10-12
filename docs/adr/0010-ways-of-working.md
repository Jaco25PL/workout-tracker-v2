# ADR-0010: Ways of working — spec first, test first, a few patterns

Status: Accepted · 2026-10-12

## Context
Claude Code writes most of the code; one human reviews it. Methods and patterns must help an AI produce correct, consistent code, and be checkable by a solo developer. Details in `docs/engineering.md`.

## Decision
- **Methods:** only two.
  - **Spec first (SDD):** each feature starts with a short spec listing the domain rules it touches and concrete input → output examples.
  - **Test first (TDD):** required for `packages/domain` and every bug fix. Spec examples become tests.
- **Architecture:** modular monolith grouped by feature, functional core (`packages/domain`) + imperative shell (`apps/*`).
- **Patterns:** use case, repository (scoped by `userId`), strategy (tracking types), dependency injection with plain parameters (incl. clock), adapter, command (outbox), result type for expected errors.
- **Enforcement:** tools over instructions. Typecheck, lint, tests and import boundaries run in hooks and CI. CLAUDE.md stays ~5 lines and links to the guide.
- **Reference slice:** the walking skeleton is the example new features copy.

## Options rejected
- **BDD with Gherkin:** the only readers are the author and Claude Code. Plain examples give the same precision without tooling.
- **Full DDD (aggregates, domain events, bounded contexts):** too much ceremony for one bounded context. We keep the shared vocabulary.
- **EDD (eval-driven):** for AI features. The app has none; revisit if one is added.
- **Full clean / hexagonal architecture with ports everywhere:** lots of boilerplate for Claude to generate and for a solo dev to review. Adapters only where a provider may change.
- **Everything in CLAUDE.md:** loaded every turn, dilutes attention, and can't enforce anything.

## Consequences
- Domain logic is fast to test and shared by phone and server.
- Consistency comes from one reference slice plus enforced boundaries, not from long prompts.
- UI and glue code have fewer unit tests; end-to-end tests cover the main flows.
- A spec per feature adds a few minutes up front and saves rework when Claude misreads intent.
