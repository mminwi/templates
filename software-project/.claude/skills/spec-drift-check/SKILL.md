---
name: spec-drift-check
description: "Use this to SCAN where specs and code disagree — produces a report only, NO edits. Categorizes findings as spec-wrong / code-wrong / both-wrong / accurate. Use when the user wants a health snapshot of the specs without editing anything yet."
---

# spec-drift-check

Scan a spec (or all specs) against code and report where they disagree. Pure assessment skill — no edits, no fixes, just a report.

## When this skill fires

Auto-fires on:

- *"Check for spec drift"*
- *"Is the spec still accurate"*
- *"Scan the specs"*
- *"What's stale in the specs"*

## What this skill does

- Reads the target spec(s)
- Reads the relevant code that backs the spec's claims
- Categorizes each finding:
  - **spec-wrong** — code is correct, spec describes obsolete behavior
  - **code-wrong** — spec describes intended behavior, code has drifted
  - **both-wrong** — neither matches a coherent design (needs design conversation)
  - **accurate** — spec and code agree (still worth listing — tells the user what they can trust)
  - **undocumented** — code does something the spec doesn't cover at all
- Writes the report to `.claude/plans/{YYYY-MM-DD}-drift-{spec-name}.md`
- Suggests next-step skills per finding (e.g., undocumented → `write-spec`; spec-wrong → `update-spec`)

## Hard rule — no edits

This skill produces a report only. Do NOT edit spec files. Do NOT fix code. The user looks at the report and decides which sibling skill to invoke for each finding.

## Full instructions

Complete behavior contract — report template, finding categories, suggested next-step routing — in [`../../commands/spec-drift-check.md`](../../commands/spec-drift-check.md).

## Related skills

- Usually invoked before `update-spec` (or `write-spec` for full rewrites) — assess first, edit second
- Pairs with `audit-subsystem` (spec-drift-check is spec-focused; audit-subsystem is broader subsystem health)
