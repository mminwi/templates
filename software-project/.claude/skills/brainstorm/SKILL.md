---
name: brainstorm
description: "Use this as the front door for non-trivial product/software work. Classifies the request, recommends one strong path, decomposes into small slices when needed, defines composer/parallelism guidance, updates the notebook for Tier 2/3 work, and hands off to write-spec or write-plan. No code."
---

# brainstorm

Open non-trivial work, decide the workflow tier, converge on direction, and decompose small enough that later agents can work with focused context.

## When this skill fires

Auto-fires on:

- *"Let's brainstorm…"*
- *"What could we do about X?"*
- *"I'm thinking about X but I'm not sure…"*
- *"Help me figure out the approach for X"*
- *"How should we handle X?"*
- *"Break this up"*
- *"How should we divide this work?"*

Plus: any wide opening of feature/product work where multiple implementations could be reasonable.

## What this skill does

- Classifies the request and recommends the correct tier.
- Reads project context before asking questions.
- Asks one clarifying question at a time only when needed.
- Gives one strong recommendation unless multiple real paths exist.
- Decomposes big work into small coherent slices.
- Defines composer guidance, file/page ownership, interfaces, and parallel/series decisions.
- Creates or updates a notebook entry for Tier 2/3 work.
- Hands off to `write-spec` or `write-plan`.

## Hard rules

- No code.
- No implementation plan.
- No padded option lists.
- Prefer small slices.
- Shared writable files mean series, not no decomposition.

## Full instructions

Complete behavior contract in [`../../commands/brainstorm.md`](../../commands/brainstorm.md).

## Related skills

- Usually hands off to `write-spec`.
- May hand off directly to `write-plan` if spec canvases are already current.
- Maintenance work may route to `systematic-debug` or `refactor` instead.
