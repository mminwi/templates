---
name: critic-review
description: "Use this AFTER a design or plan is drafted but BEFORE code is written. A ruthless QA reviewer persona audits the artifact for contradictions, missing edge cases, integration flaws. The two-AI variant (separate AI instance) is the highest-leverage technique in Hodos. Builder fixes flaws; loop until clean."
---

# critic-review

Audit a design or plan for flaws *before* code is written. Find contradictions, missing edge cases, integration flaws, vague specifications. The reviewer persona is "ruthless Senior QA Architect" — does NOT write code, only flaws and recommendations.

## When this skill fires

Auto-fires on:

- *"Run the critic"*
- *"Audit this plan"*
- *"Find flaws"*
- *"Review the design"*
- *"What could go wrong"*

Or after `write-plan` for Big-tier work — the plan should always be auditied before execution.

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

- Runs between `write-plan` (or `write-spec`) and `skeleton-first`/`execute-plan`
- Loops with the builder skill that produced the artifact (fix → re-review → fix) until clean
- Different from `review-work` (which audits the *finished* code, not the design)
