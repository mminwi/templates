---
name: skeleton-first
description: "Use this when introducing a meaningful new module — new API surface, new background job, new admin page. Write file paths + function signatures + empty bodies (or pass/TODO) FIRST. Wait for user approval before any logic is written. Lets the user spot a missing piece or wrong shape before code exists."
---

# skeleton-first

Write the **shape** of new code before any logic — file paths, function/class signatures, docstrings, empty bodies (`pass` / `TODO`). User reviews the shape; once approved, logic gets filled in.

The user prefers the word **framework** verbally — same thing as skeleton.

## When this skill fires

Auto-fires on:

- *"Frame it up"*
- *"Write the framework"*
- *"Framework first"*
- *"Skeleton first"*
- *"Show me the structure first"*

Or whenever introducing a meaningful new module (Medium or Big tier) — auto-applies even without explicit phrasing.

## What this skill does

- Writes file paths and module structure
- Writes function/class/method signatures with parameter types and return types
- Writes one-line docstrings describing each function's purpose
- Leaves bodies empty (`pass` / `TODO: implement` / equivalent for the language)
- Presents the skeleton to the user for approval BEFORE any logic is written
- After approval, hands off to `execute-plan` to fill in bodies

## Hard rule — wait for approval before logic

The skeleton is the visual review surface. The user (especially a non-classically-trained programmer) can spot a missing piece or wrong shape from a skeleton without reading implementation. Filling in logic before the skeleton is approved defeats the purpose.

## Full instructions

Complete behavior contract — skeleton format per language, what counts as "shape" vs "logic," docstring conventions — in [`../../commands/skeleton-first.md`](../../commands/skeleton-first.md).

## Related skills

- Usually invoked after `write-plan` (Big-tier) or directly after `write-spec` (Medium-tier)
- Hands off to: `execute-plan` (fill in bodies)
- Pairs with `critic-review` — critic can audit a skeleton before logic is written
