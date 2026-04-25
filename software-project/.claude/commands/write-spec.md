---
description: Write a new spec for a feature, event, route, or subsystem. JSON by default, Markdown on request. Replaces /spec-page and /spec-event from earlier Hodos versions.
---

# /write-spec

## When to use

Invoke when:

- A feature, route, event handler, or subsystem needs a spec and none exists yet
- An existing spec is so stale (>30%) that `/update-spec` recommended a full rewrite
- The user says *"spec out X," "document how X works," "write the spec for Y," "we need a spec for this"*

Do **not** use when:

- The spec already exists and only needs a targeted edit — that's `/update-spec`
- You just want to find where specs are stale — that's `/spec-drift-check`
- The work is too big to spec as one unit — run `/decompose` first

## Core discipline — software-defining content only

The spec describes **how the software is built**: schema, routes, runtime behavior, data shapes, API contracts, configuration the runtime depends on. Apply the test from `specs/README.md`:

> *If this content disappeared, would the code break?*
> Yes → goes in this spec.
> No → goes in `vision/` instead, or doesn't get written down at all.

Common contamination to avoid: the user explained *why* a feature exists or *who* it's for while specifying *how* it should work. Capture the *how* here. The *why* and *who* go in `vision/`. See CLAUDE.md `Specs describe how the software is built. Vision describes why it exists.`

## Format

- **JSON is the default** — schema-heavy specs, route maps, plan matrices, anything where structure matters and contradictions across files need to be catchable.
- **Markdown on request** — when the user wants to read and comment, render the spec as MD. Edits or comments come back; sync to JSON.

Validate JSON specs after writing:

```bash
node -e "JSON.parse(require('fs').readFileSync('specs/<file>.json','utf8')); console.log('ok')"
```

## What you must produce

A new spec file at `specs/<feature-or-subsystem>.json` (or `.md` if the user asked for Markdown). Structure:

### JSON template

```json
{
  "meta": {
    "name": "<spec name>",
    "version": "1.0",
    "generated": "YYYY-MM-DD",
    "description": "One paragraph describing what this spec covers and what part of the program it defines."
  },
  "<top-level-section>": {
    "description": "What this section defines.",
    "<subkey>": "..."
  },
  "<another-section>": {
    "..."
  }
}
```

Top-level sections depend on what's being specced. Common patterns:

- **Feature spec:** `meta`, `dataModel`, `behavior`, `apiSurface`, `constraints`, `errorHandling`
- **Event handler spec:** `meta`, `trigger`, `flow`, `sideEffects`, `constraints`, `errorHandling`
- **Route spec:** `meta`, `routes` (with method, path, request, response per route), `auth`, `errors`
- **Subsystem spec:** `meta`, `responsibilities`, `interfaces`, `dependencies`, `data`, `runtime`

Use the structure that fits the thing being specced. Don't force a feature spec into an event-handler shape.

### Markdown template

```markdown
# <Spec Name>

**Version:** 1.0
**Generated:** YYYY-MM-DD

## What this covers

One paragraph.

## <Section>

Body.

## <Section>

Body.
```

## Process

### Step 1 — Confirm the scope

Restate what you understood the spec needs to cover, in one or two sentences. Get user confirmation before writing.

### Step 2 — Identify the spec's place in the project

- Where does this file go in `specs/`?
- Are there existing specs it cross-references? (Read them.)
- Is anything in scope that already lives in another spec? (Don't duplicate; cross-reference.)

### Step 3 — Draft the spec in sections

Write top-level section by section, presenting each for user approval before moving on. Long specs are easier to validate this way.

For each section:

- Describe the structure and behavior in concrete terms
- Use code-shaped examples when the spec describes data shapes (table columns, JSON payloads, function signatures)
- Cross-reference other specs by file path (`See PNT Web Application Architecture.json apiDesign for…`)

### Step 4 — Validate and finalize

- For JSON specs: validate parsing.
- For MD specs: skim once for internal consistency.
- Check the cross-spec consistency — does this spec contradict anything in another spec? If yes, surface it before saving.

### Step 5 — Commit and hand off

Save the spec file. Note in the commit message what's new. Hand off to `/write-plan` (for implementation work) or `/critic-review` (if the design wants a separate-context audit before implementation).

## Rules

1. **Software-defining content only.** No marketing, no audience definitions, no purpose explanations. Those go in `vision/`.
2. **Concrete over abstract.** A spec that says "the system handles authentication" is useless. The spec must describe *which routes*, *what tokens*, *what flows*, *what storage*.
3. **Cross-reference, don't duplicate.** If another spec already defines something, point to it.
4. **Section-by-section approval for Big specs.** Don't dump 1,000 lines for the user to review at once.
5. **Don't promote reasoning to rules.** When the user explains *why* during the spec conversation, the reasoning is context, not text for the spec. See CLAUDE.md.
6. **JSON unless asked otherwise.** The default is the validatable format.

## Difference from sibling skills

| Skill | Purpose | Output |
|---|---|---|
| `/write-spec` | **Create** a new spec (or full rewrite after massive drift) | New spec file in `specs/` |
| `/update-spec` | **Targeted edit** of an existing spec | Diff proposals, structure preserved |
| `/spec-drift-check` | **Scan** for spec/code divergence | Report only, no edits |

## What you do NOT do in this skill

- Do not write code (that's `/execute-plan` / build-phase skills)
- Do not write tests (that's `/backfill-tests` for current behavior, or part of build for new code)
- Do not write a plan (that's `/write-plan`)
- Do not include vision content (purpose, audience, market positioning) — those go in `vision/`
- Do not enumerate things that live in code as the source of truth — point at the source instead
