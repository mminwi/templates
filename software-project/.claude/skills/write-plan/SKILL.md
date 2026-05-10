---
name: write-plan
description: "Use this after spec canvases are aligned for Tier 2/3 or multi-file work. Produces a checkbox plan, creates/links notebook entry, updates tests/TEST_PLAN.md, includes skeleton/framework phase when substantial, and runs a bounded critic loop automatically before execution."
---

# write-plan

Produce a checkbox-driven implementation plan from aligned spec canvases. The plan is a contract — once approved, `execute-plan` works through it without improvising.

## When this skill fires

Auto-fires on:

- *"Write the plan"*
- *"Let's plan this"*
- *"Plan this out"*
- *"Plan the implementation"*

Use for Tier 2/3 work, multi-file changes, multi-agent work, sequenced slices, or whenever the user asks for a plan. Skip only for genuinely tiny or obvious changes.

## What this skill does

- Reads the relevant spec canvases to understand the contract
- Produces a numbered checkbox plan at `plans/{YYYY-MM-DD}-{short-task-name}.md`:
  - Each step has a clear file path or operation
  - Each step has a completion criterion
- Execution model names owners, writable files, interfaces, and parallel/series mode
- Creates or updates `notebook/{YYYY-MM-DD}-{short-topic}.md`
- Creates or updates `tests/TEST_PLAN.md`
- Includes skeleton/framework tasks when work is substantial
- Runs bounded critic review automatically for non-trivial plans
- Calls out dependencies between steps
- Notes any open questions that block execution

## Hard rule — plan as contract

Once approved, the plan is a contract. `execute-plan` does not improvise. If the plan is wrong, the plan gets revised first; code does not drift from plan.

Plan files are committed to git as a decision log. Never delete an old plan — mark it superseded if the approach changes.

Critic review is part of planning. The user should not have to manually request it.

## Full instructions

Complete behavior contract — plan template, completion criteria style, how dependencies are tracked — in [`../../commands/write-plan.md`](../../commands/write-plan.md).

## Related skills

- Usually invoked after `brainstorm` and `write-spec`.
- Includes critic review internally.
- Hands off to `execute-plan`.
