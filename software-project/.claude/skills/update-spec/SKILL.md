---
name: update-spec
description: "Use this for TARGETED edits to an existing spec file — preserve structure and voice, propose diffs before applying. Stops and redirects to write-spec if more than ~30% of the spec needs changing (that's a rewrite, not an update). NOT for creating new specs (that's write-spec) or scanning for drift (that's spec-drift-check)."
---

# update-spec

Targeted edits to an existing spec. Surgical, not rewrite. Preserve the spec's structure and voice; change only what's stale; propose diffs before applying.

## When this skill fires

Auto-fires on:

- *"Update the spec"*
- *"Fix the spec for X"*
- *"The spec is wrong about Y"*
- *"I just changed X, update the spec to match"*

Two common modes:

1. **User-driven** — user knows what changed and tells you
2. **Investigation-driven** — user suspects the spec is stale; you scan and find the drift

## What this skill does

- Identifies the target spec file
- Reads it fully (structure, voice, conventions) before touching anything
- Gathers what changed (user-driven or by scanning code)
- Counts how much of the spec is stale:
  - **<30%** — proceed with surgical edits
  - **≥30%** — STOP. The spec has drifted too far for surgical fixes. Recommend `write-spec` for clean rewrite.
- Proposes diffs (before/after) for each section needing edit
- Applies edits ONLY after user approval
- Replaces hardcoded enumerations (route lists, table lists, "currently 12 X") with pointers to source-of-truth files

## Hard rule — preservation, not rewrite

Don't reorganize the spec. Don't change voice. Don't rewrite accurate sections. If the spec needs structural changes, that's a rewrite — redirect to `write-spec`.

Architectural claims require verification: if you're editing a "Database" section, read the actual DB code. Don't make sweeping claims you can't verify from source.

## Full instructions

Complete behavior contract — process per mode, diff template, the 30%-rule, rot-pattern detection (replace counts/enumerations with pointers) — in [`../../commands/update-spec.md`](../../commands/update-spec.md).

## Related skills

- `write-spec` — for creating new specs OR full rewrites after >30% drift
- `spec-drift-check` — for scanning where specs are stale (no edits)
- `update-spec` is the **edit verb**; `write-spec` is the **create verb**; `spec-drift-check` is the **assess verb**
