---
name: write-spec
description: "Use this when a feature, route, event handler, or subsystem needs a spec and none exists yet, OR when an existing spec is so stale (>30%) that update-spec recommended a full rewrite. Produces a JSON spec by default, Markdown on request. Software-defining content only — vision/marketing/audience content goes elsewhere."
---

# write-spec

Create a new spec file describing **how the software is built**: schema, routes, runtime behavior, data shapes, API contracts. Software-defining content only.

## When this skill fires

Auto-fires on:

- *"Spec out X"*
- *"Document how X works"*
- *"Write the spec for Y"*
- *"We need a spec for this"*

Or when a feature/event/route/subsystem needs documentation and no existing spec covers it.

## What this skill does

- Confirms the scope in 1-2 sentences before writing anything
- Reads existing specs to understand cross-references
- Drafts the new spec section by section, with user approval after each section for Big specs
- Saves to `specs/<feature-or-subsystem>.json` (or `.md` on request)
- Validates JSON parsing after writing
- Cross-references other specs by file path; never duplicates content that lives elsewhere

## Hard rule — software-defining content only

The test from `specs/README.md`: *"If this content disappeared, would the code break?"* Yes → spec. No → vision (do not include here).

Common contamination to avoid: the user explains *why* a feature exists or *who* it's for while specifying *how* it should work. Capture the *how* here. The *why* and *who* go in `vision/` (or don't get written down at all if they're just reasoning).

## Full instructions

Complete behavior contract — JSON template, MD template, process, rules, sibling-skill differences — in [`../../commands/write-spec.md`](../../commands/write-spec.md).

## Related skills

- `update-spec` — for editing an existing spec (NOT for creation)
- `spec-drift-check` — for finding where specs are stale (NOT for editing)
- `decompose` — run first if the spec covers too much to write as one unit
