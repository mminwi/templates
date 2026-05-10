---
description: Create or revise structured product specification artifacts — CSV canvases, page mock-ups, interaction matrices, state tables, generated JSON, and concise review summaries
---

# /write-spec

## When to use

Invoke when:

- A brainstormed direction needs to become product/spec artifacts
- A page, database, workflow, behavior, or state model is changing
- Existing code needs to be reverse-engineered into spec canvases
- The user says *"update the spec," "spec this out," "document the page," "make the mock-up," "make the CSVs"*

Do **not** use when:

- The work is a tiny code edit with no behavior/product impact
- The user only wants drift assessment with no edits — use `/spec-drift-check`
- The request is pure troubleshooting — use `/debug`

## Core discipline — structured artifacts first

In Hodos, "the spec" means the structured product specification artifacts, not a large prose document.

Primary artifacts:

- `specs/database/{table}.csv`
- `specs/pages/{page}/interaction-matrix.csv`
- `specs/pages/{page}/mock-up.html`
- `specs/pages/{page}/state-transitions.csv` when state behavior matters
- Generated JSON views when useful for AI/code processing
- Concise Markdown summaries only when useful for review

Vision remains separate. Use `vision/` only when purpose, audience, rejected ideas, potential directions, or business direction changes.

## Spec canvas rules

- Use one CSV per database table by default.
- Group related tables as a logical data structure in surrounding notes or indexes, not one giant CSV.
- Page interaction matrices reference table CSVs instead of duplicating the whole database.
- Keep CSV rows/columns filled enough to remain structurally readable.
- Do not over-specify rigid CSV schemas up front; generate useful tables, let the user edit them, then interpret the edited tables.
- Keep mock-ups low fidelity. They are visual spec canvases, not production UI.
- Prefer state/transition CSVs over Mermaid flowcharts for behavior that must be implemented.

## Spec revision convention

Use stable current paths plus Git history and local changelogs.

- Keep the current artifact at its stable path.
- Use Git for exact file history.
- Record meaningful conceptual changes in a nearby `CHANGELOG.md`.
- Only create full versioned alternatives when comparing major options or preserving a major baseline.

Examples:

```text
specs/pages/contacts/mock-up.html
specs/pages/contacts/interaction-matrix.csv
specs/pages/contacts/CHANGELOG.md
specs/database/contacts.csv
specs/database/CHANGELOG.md
```

## Process

### Step 1 — Read context

Read:

- `CLAUDE.md`
- `hodos.md`
- Relevant `specs/`
- Relevant `vision/` if product direction is involved
- Existing code when reverse-engineering or validating current behavior
- Relevant notebook entry if one exists

### Step 2 — Decide artifact set

Identify which artifacts must be created or updated:

- Database table CSVs
- Page mock-ups
- Interaction matrices
- State/transition tables
- Generated JSON views
- Markdown review summaries
- Vision files

Do not produce artifacts that are not useful for the change.

### Step 3 — Generate or update canvases

For new work, generate the simplest useful canvas from the brainstorm direction.

For existing projects, inspect the code and reverse-engineer current behavior into canvases so the user can review without reading source.

### Step 4 — Semantic review

Check that the canvases preserve meaning:

- CSVs and mock-ups agree
- Interaction matrices reference the right data
- State transitions cover the behavior
- Generated JSON/Markdown views do not add meaning not present in the canvases
- Vision content has not leaked into specs

If user edits a CSV/mock-up, treat the edited artifact as the human intent source and resync generated views.

### Step 5 — Update notebook

For Tier 2/3 work, update the notebook with:

- What spec artifacts changed
- Notable decisions
- Non-obvious context
- Links to artifacts

Do not paste the full spec into the notebook.

### Step 6 — Handoff

When the spec canvases are aligned enough to implement, hand off to `/write-plan`.

## What you do NOT do

- Do not write implementation code.
- Do not write the implementation plan.
- Do not turn specs into large prose documents.
- Do not duplicate database detail into page matrices.
- Do not use flowcharts as the main behavioral artifact.
- Do not bury vision in specs.
