---
name: write-spec
description: "Use this to create or revise structured product specification artifacts: database CSVs, page interaction matrices, low-fidelity page mock-ups, state/transition CSVs, generated JSON views, and concise review summaries. This is the main 'update the spec' skill."
---

# write-spec

Create or revise the structured spec canvases that define product behavior. Keep CSVs/mock-ups/tables primary; use JSON/Markdown as generated or review views.

## When this skill fires

Auto-fires on:

- *"Update the spec"*
- *"Fix the spec for X"* / *"The spec is wrong about Y"*
- *"Spec out X"*
- *"Document the page"*
- *"Make the CSVs"*
- *"Make the mock-up"*
- *"Reverse engineer this into specs"*
- *"New page"* / *"Add a [page name] page"*
- *"Scaffold specs for X"*
- *"Bootstrap the spec canvases"*

Or when a product/page/data/behavior change needs spec canvases before implementation.

**Three modes:**
- **Bootstrap** — target has no spec canvases yet. Creates the full initial artifact set.
- **Targeted edit** — existing spec needs a small, focused update. Preserves structure/voice, proposes diffs before applying, trims rot, flags disagreements.
- **Full create/rewrite** — new spec canvases or existing spec has drifted too far for surgical fixes.

## What this skill does

- **Bootstraps** initial canvas sets for new pages, database entities, or workflows when none exist yet.
- **Targeted edits** to existing specs — proposes diffs, preserves structure, trims hardcoded values that rot, flags spec-vs-code disagreements for user decision.
- Creates or updates database CSVs, page mock-ups, interaction matrices, and state tables.
- Generates JSON/Markdown views only when useful.
- Reverse-engineers existing code into canvases when requested.
- Updates local spec changelogs for meaningful conceptual changes.
- Updates the notebook for Tier 2/3 work.
- Hands off to `write-plan` when specs are aligned.

## Hard rules

- CSVs/mock-ups/tables are primary.
- Do not turn specs into large prose documents.
- Use one CSV per database table by default.
- Page interaction matrices reference database CSVs instead of duplicating them.
- Keep mock-ups low fidelity.
- Keep vision separate unless purpose/direction changes.

## Full instructions

Complete behavior contract in [`../../commands/write-spec.md`](../../commands/write-spec.md).

## Related skills

- `brainstorm` — often comes first to choose direction and slices.
- `write-plan` — comes after spec canvases are aligned.
- `analyze` — includes spec drift checking as part of the full health check
