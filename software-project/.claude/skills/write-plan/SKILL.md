---
name: write-plan
description: "Use this for Big-tier changes that need an implementation plan before code is written. Produces a numbered checkbox plan file at .claude/plans/{YYYY-MM-DD}-{task}.md with file paths, completion criteria, and any test scenarios. Skip for Small and Medium changes — the spec is the contract."
---

# write-plan

Produce a checkbox-driven implementation plan for a Big-tier change. The plan is a contract — once approved, `execute-plan` works through it without improvising.

## When this skill fires

Auto-fires on:

- *"Write the plan"*
- *"Let's plan this"*
- *"Plan this out"*
- *"Plan the implementation"*

Use only for Big-tier work (new subsystem, schema change, multi-spec change, new external integration). Do NOT generate plan files for Small or Medium changes — the spec covers them.

## What this skill does

- Reads the relevant spec(s) to understand the contract
- Produces a numbered checkbox plan at `.claude/plans/{YYYY-MM-DD}-{short-task-name}.md`:
  - Each step has a clear file path or operation
  - Each step has a completion criterion
  - Test scenarios are included where relevant (Given/When/Then-style for clarity)
- Calls out dependencies between steps (which must complete first)
- Notes any open questions that block execution

## Hard rule — plan as contract

Once approved, the plan is a contract. `execute-plan` does not improvise. If the plan is wrong, the plan gets revised first; code does not drift from plan.

Plan files are committed to git as a decision log. Never delete an old plan — mark it superseded if the approach changes.

## Full instructions

Complete behavior contract — plan template, completion criteria style, how dependencies are tracked — in [`../../commands/write-plan.md`](../../commands/write-plan.md).

## Related skills

- Usually invoked after `brainstorm` (design approved) and `write-spec` (contract defined)
- Often runs before `critic-review` (audit the plan before code)
- Hands off to: `execute-plan` (build phase)
