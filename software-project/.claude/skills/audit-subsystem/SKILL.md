---
name: audit-subsystem
description: "Use this for a broad health check of a single subsystem (a module, a feature, a layer). Produces a Green/Yellow/Red report across categories — code quality, test coverage, spec alignment, dependencies, security, performance, observability, error handling. NOT for whole-codebase audits (too broad). NOT for fixing — assess only."
---

# audit-subsystem

Health check on one subsystem at a time. Produces a Green/Yellow/Red report across multiple categories so the user can triage what to fix.

## When this skill fires

Auto-fires on:

- *"Audit the X module"*
- *"How healthy is X"*
- *"Check the health of X"*
- *"Status of X"*

## What this skill does

For the named subsystem, evaluates and reports across categories:

- **Code quality** — readability, complexity, dead code, duplication
- **Test coverage** — what's tested, what isn't, test quality (do they actually test the behavior?)
- **Spec alignment** — does the spec describe what's actually here? (delegates to `spec-drift-check` for the deep scan)
- **Dependencies** — what does this subsystem depend on, are versions current, any deprecated APIs in use?
- **Security** — any obvious vulnerabilities, secrets leakage, auth gaps?
- **Performance** — any N+1 queries, blocking calls, obvious inefficiencies?
- **Observability** — logging, metrics, error reporting — can you tell when this is broken in prod?
- **Error handling** — what happens when things go wrong? Silent swallow, user-facing message, retry?

Each category gets:
- **🟢 Green** — healthy, no action needed
- **🟡 Yellow** — concerns, document and watch (or fix opportunistically)
- **🔴 Red** — broken or risky, needs deliberate fix

Report goes to `.claude/plans/{YYYY-MM-DD}-audit-{subsystem}.md`.

## Hard rule — assess, don't fix

This skill produces a report. The user triages and decides what to fix. Do NOT start refactoring or fixing during the audit — that's `refactor` / `execute-plan` / etc.

## Full instructions

Complete behavior contract — full category list, what counts as Green/Yellow/Red per category, report template — in [`../../commands/audit-subsystem.md`](../../commands/audit-subsystem.md).

## Related skills

- Often run before `refactor` (audit reveals what's worth refactoring)
- Uses `spec-drift-check` internally for the spec-alignment category
- Uses `backfill-tests` recommendations when test-coverage is yellow/red
