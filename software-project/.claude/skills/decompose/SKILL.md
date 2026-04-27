---
name: decompose
description: "Use this when a task is too big to plan as a single unit, has unclear scope, touches multiple modules, or covers multiple features. Produces a three-level tree (epic → feature → component) so the user only commits to one slice at a time. Do NOT use for single-file changes or bounded scope."
---

# decompose

Break a large task into a navigable tree (epic → feature → component) so the user can pick one slice to start with. The point is to *reveal scope*, not to pre-plan every component.

## When this skill fires

Auto-fires on:

- *"Let's decompose this"*
- *"Break X into pieces"*
- *"Slice this up"*
- *"This is too big"*

Or when the user's request is multi-feature (e.g., *"redocument the whole system"*), unclear in scope (*"make the dashboard better"*), or multi-module (touches DB + UI + integrations).

## What this skill does

- Produces a three-level decomposition tree, written to `plans/{YYYY-MM-DD}-{task-name}-decomposition.md`
- Recommends a starting point (defaults to smallest-and-riskiest — reveals surprises early)
- Calls out *out-of-scope* items explicitly to prevent scope creep later
- Lists open questions the user must answer before proceeding
- Three levels max — if a component looks big, asks if it should become its own feature

## Hard rule

Do NOT plan the whole tree in detail. The point is to reveal scope, not to pre-plan every component. Three levels deep, maximum.

## Full instructions

Complete behavior contract — process, rules, output template, what NOT to do — in [`../../commands/decompose.md`](../../commands/decompose.md).

## Related skills

- Often invoked after `brainstorm` when the design conversation reveals multi-slice work
- Hands off to: `write-plan` (for implementation work on the chosen slice) or `write-spec` (for documentation work)
