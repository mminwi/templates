---
name: review-work
description: "Compatibility/standalone final review. Normally folded into execute-plan as separate-agent verification. Audits finished work against the approved plan, spec canvases, and test plan."
---

# review-work

Final verification audit: catch drift between plan and implementation before declaring done. Normally this is a mandatory final gate inside `execute-plan`.

## When this skill fires

Auto-fires on:

- *"Review the work"*
- *"Does this match the plan"*
- *"Audit the diff"*
- *"Check the implementation"*

Normally invoked by `execute-plan` before declaring complete.

## What this skill does

- Reads the approved plan and the actual diff
- Adopts the reviewer persona — does the diff match the plan?
- Checks:
  - Does each plan checkbox correspond to a real change in the diff?
  - Were there silent deviations from the skeleton (Big-tier)?
  - Were any side effects introduced that weren't in the plan?
  - Are the verification claims (from verification-before-completion) actually tested by the diff?
- Produces a report:
  - **PASS** — diff matches plan, no notes
  - **PASS-WITH-NOTES** — minor deviations, list them
  - **NEEDS-FIX** — substantive drift, list issues by severity

## Hard rule — separate context preferred

Same as `critic-review`: highest leverage is running this in a separate AI instance with no knowledge of how the builder reasoned. The reviewer reads the diff cold.

If running same-session, prime the persona explicitly and have the reviewer ignore the conversation history when judging the diff.

## Full instructions

Complete behavior contract — review report template, persona priming, deviation severity — in [`../../commands/review-work.md`](../../commands/review-work.md).

## Related skills

- Runs inside `execute-plan` after implementation and tests
- Different from `critic-review` (which audits the *design* before code; this audits the *finished code*)
- If review surfaces issues, routes back to `execute-plan` (or back further if the plan was wrong)
