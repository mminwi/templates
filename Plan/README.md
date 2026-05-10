# Plan Index

This folder contains planning artifacts for changes to the root `templates` repository, especially changes being considered for `software-project/`.

## Status Rules

- A plan is considered active only if it is listed under **Active Plans** in this index.
- Each plan file should also have its own `## Status` section.
- If a plan is replaced, keep the old file and mark it superseded.
- Do not delete old plan artifacts unless the user explicitly asks.
- If this index and an individual plan disagree, inspect both and update the stale one before proceeding.

## Active Plans

| Plan | Status | Scope | Notes |
|---|---|---|---|
| [`2026-05-10-software-project-specification-convergence-v4.md`](2026-05-10-software-project-specification-convergence-v4.md) | Active | Product specification model | Current product specification plan for vision triage, spec canvases, CSV/JSON round-tripping, semantic diff, database canvases, page mock-ups, and page interaction matrices. |
| [`2026-05-10-software-project-workflow-plan-v1.md`](2026-05-10-software-project-workflow-plan-v1.md) | Active draft | Workflow model | Defines how AI sessions should move through the product specification artifacts during new-project, existing-project, page-change, and vision-change work. |

## Superseded Plans

| Plan | Superseded By | Notes |
|---|---|---|
| [`2026-05-10-software-project-specification-hole.md`](2026-05-10-software-project-specification-hole.md) | [`2026-05-10-software-project-specification-convergence-v3.md`](2026-05-10-software-project-specification-convergence-v3.md) | Incomplete scratch artifact retained for history. |
| [`2026-05-10-software-project-specification-convergence-v2.md`](2026-05-10-software-project-specification-convergence-v3.md) | [`2026-05-10-software-project-specification-convergence-v3.md`](2026-05-10-software-project-specification-convergence-v3.md) | Intermediate artifact retained for history. |
| [`2026-05-10-software-project-specification-convergence-v3.md`](2026-05-10-software-project-specification-convergence-v3.md) | [`2026-05-10-software-project-specification-convergence-v4.md`](2026-05-10-software-project-specification-convergence-v4.md) | Prior active concept artifact superseded by the complete v4 plan. |

## Scratch / Example Files

| File | Purpose | Notes |
|---|---|---|
| [`sample-database-spec.csv`](sample-database-spec.csv) | CSV table-editing experiment | Fake data used to test whether CSV/table editing is a usable spec canvas workflow. |

## Note About `software-project/`

The `software-project/` template currently documents project plans as living in lowercase `plans/` inside a seeded project. This root `Plan/` folder is for planning changes to the template repository itself, not for plans inside projects created from the template.
