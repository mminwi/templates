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
- The user only wants drift assessment with no edits — use `/analyze` (includes spec drift)
- The request is pure troubleshooting — use `/troubleshoot`

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

## Bootstrap mode — new page, feature, or module

When the work is **new** (not updating existing specs), bootstrap the full initial canvas set before doing anything else. This gives you and the user something concrete to review and edit immediately.

### When bootstrap fires

- *"New page,"* *"add a contacts page,"* *"spec out a new feature,"* *"scaffold specs for invoicing"*
- Brainstorm output identifies a new page, entity, or module that has no spec canvases yet
- The relevant `specs/` subdirectory does not exist or is empty

### What bootstrap creates by type

**New page:**

```text
specs/pages/{page}/mock-up.html          ← low-fidelity HTML layout
specs/pages/{page}/interaction-matrix.csv ← columns: Element, Action, Result, Data Source, Notes
specs/pages/{page}/state-transitions.csv  ← only if the page has modal/state behavior
specs/pages/{page}/CHANGELOG.md          ← empty, ready for revision notes
```

**New database entity:**

```text
specs/database/{table}.csv               ← columns: Column, Type, Constraints, Default, Notes
specs/database/CHANGELOG.md              ← update existing or create
```

**New workflow / service / background process:**

```text
specs/{feature}/state-transitions.csv    ← columns: Current State, Event, Next State, Side Effects, Notes
specs/{feature}/CHANGELOG.md
```

If the new thing spans multiple types (e.g., a new page that also introduces a new table), create all relevant canvases.

### Bootstrap rules

- Generate the **simplest useful version** from the brainstorm direction or user description. Do not over-specify.
- Mock-ups are low fidelity — plain HTML, no styling framework, no pixel polish.
- CSV canvases should have real column headers and 3–5 representative rows so the user can see the shape and edit.
- Interaction matrices should reference database CSVs by table name, not duplicate column definitions.
- Do not create canvases that are not useful for the thing being specced (e.g., no state-transitions.csv for a static display page).
- After creating the canvases, tell the user what was created and invite them to edit before proceeding.

### After bootstrap

The canvases are now the working surface. Continue with the normal write-spec process (review, semantic check, handoff to write-plan) or pause for user edits.

---

## Targeted edit mode — updating existing specs

When the work is a **small, focused change** to an existing spec (a field changed, a route was renamed, a behavior was updated), use targeted edit mode instead of a full rewrite.

### When targeted edit fires

- *"Update the spec," "fix the spec for X," "the spec is wrong about Y," "I changed X, update the spec"*
- A known small change needs to be reflected in existing canvases
- Investigation reveals a spec is partially stale but mostly accurate

### Targeted edit rules

1. **Preserve structure and voice.** Keep the existing section order, heading style, formatting, and tone. If the spec uses tables, edits stay in tables.
2. **Change only what's stale.** Accurate sections stay untouched.
3. **Propose before applying.** Show the user the diff — before/after for each section needing a change. Let them approve, reject, or redirect.
4. **Flag disagreements.** If the spec and code genuinely disagree and neither is obviously right, report the disagreement and let the user decide. Do not resolve it unilaterally.
5. **Trim rot.** Replace hardcoded values that will naturally drift with pointers to the source of truth:
   - Row counts, data volumes ("3,906 companies") — remove or convert to "approximate as of {date}"
   - Exhaustive lists that live in code (all routes, all columns, all flags) — replace with a pointer to the source file. Example: "Routes: see `src/routes/` for the current list."
   - A spec that enumerates things will always drift. A spec that points to the source of truth cannot.
6. **Verify architectural claims.** Any edit that makes or modifies a sweeping claim ("single database," "three services," "monolithic app") must be verified by reading at least one file in the relevant code path. If you can't verify, say so: "Based on existing spec text (unverified)."

### Scope threshold

If more than ~30% of a spec needs changing, stop. That's a rewrite, not a patch. Tell the user and proceed with full write-spec mode instead.

---

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
