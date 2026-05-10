---
name: skeleton-first
description: "Compatibility/standalone skeleton/framework pass. Normally folded into write-plan as a skeleton/framework phase for substantial work."
---

# skeleton-first

Write the **shape** of new code before any logic — file paths, function/class signatures, docstrings, empty bodies (`pass` / `TODO`). Normally this is a planned phase inside `write-plan`.

The user prefers the word **framework** verbally — same thing as skeleton.

## When this skill fires

Auto-fires on:

- *"Frame it up"*
- *"Write the framework"*
- *"Framework first"*
- *"Skeleton first"*
- *"Show me the structure first"*

Normally included by `write-plan` when work is substantial enough.

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

- Normally included as a phase in `write-plan`
- Executed by `execute-plan`
