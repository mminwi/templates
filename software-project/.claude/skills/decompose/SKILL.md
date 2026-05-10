---
name: decompose
description: "Compatibility/standalone decomposition. Normally folded into brainstorm. Use directly only when the user explicitly asks for a decomposition artifact."
---

# decompose

Break a large task into slices. Normally this happens inside `brainstorm` with composer guidance, file ownership, and parallel/series decisions.

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

- Normally replaced by `brainstorm`
- If used standalone, hand off to `write-spec` or `write-plan`
