---
name: execute-plan
description: "Use this AFTER a plan is approved. Executes the plan checkbox by checkbox, including skeleton/framework phases, tests, notebook closeout, and mandatory separate-agent verification before completion. If the plan is wrong, stop and revise it — do not drift."
---

# execute-plan

Implement an approved plan, step by step, without deviation. Skeleton, testing, and verification are phases inside the plan.

## When this skill fires

Auto-fires on:

- *"Execute the plan"*
- *"Build it"*
- *"Run the plan"*
- *"Implement this"*

Or after `write-plan` is approved.

## What this skill does

- Reads the approved plan from `plans/{YYYY-MM-DD}-{task}.md`
- Reads linked spec canvases, notebook entry, and `tests/TEST_PLAN.md`
- Works through each checkbox in order
- Marks each item complete as it's done (in the plan file or in conversation)
- Writes code that matches the plan's file paths and completion criteria
- Executes skeleton/framework phases if the plan includes them
- Runs required tests/manual checks
- Updates notebook with result/surprises/follow-up
- Requires separate-agent verification before any "done" claim

## Hard rule — plan is contract

The plan is the contract. If the plan is wrong, the answer is **stop and revise the plan first, then resume execution**. Drifting off-plan mid-execution is the #1 anti-pattern Hodos exists to prevent.

If something during execution reveals the plan is incomplete (missing step, wrong order, unclear criterion), stop and propose a plan revision before continuing.

## Full instructions

Complete behavior contract in [`../../commands/execute-plan.md`](../../commands/execute-plan.md).

## Related skills

- Input: approved `write-plan` output.
- Includes skeleton/framework execution if planned.
- Includes final separate-agent verification; `verify`/`review-work` are not separate normal user-called steps.
