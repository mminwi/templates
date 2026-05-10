---
name: critic-review
description: "Compatibility/standalone critic pass. Normally folded into write-plan. Use directly only when the user explicitly asks for a critic/audit of a spec or plan before code is written."
---

# critic-review

Audit a design or plan for flaws *before* code is written. Normally this happens automatically inside `write-plan`.

## When this skill fires

Auto-fires on:

- *"Run the critic"*
- *"Audit this plan"*
- *"Find flaws"*
- *"Review the design"*
- *"What could go wrong"*

Normally invoked by `write-plan` for non-trivial work.

## What this skill does

- Reads the design/plan/spec being audited
- Adopts the "ruthless QA Architect" persona — focus on what's wrong, not what's right
- Produces a severity-sorted flaw report:
  - **CRITICAL** (must fix or design is broken)
  - **HIGH** (likely to cause real problems)
  - **MEDIUM** (worth addressing)
  - **LOW** (nice-to-fix)
- Calls out missing edge cases, contradictions, vague requirements, integration gaps
- Does NOT propose code fixes — recommends design changes back to the builder

## Hard rule — separate context for max value

The highest-leverage form of this skill runs in a **separate AI instance** with no knowledge of how the builder reasoned. The two-AI variant catches what same-context review cannot.

If running in the same session as the builder, prime the persona explicitly: "You are a ruthless Senior QA Architect. Do not be helpful. Find what's wrong."

## Full instructions

Complete behavior contract — review template, persona priming, severity definitions, two-AI workflow — in [`../../commands/critic-review.md`](../../commands/critic-review.md).

## Related skills

- Runs inside `write-plan` before `execute-plan`
- Loops at most two times before escalation
- Different from `review-work` (which audits the *finished* code, not the design)
