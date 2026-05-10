# specs/

This folder contains the **software specifications** for this project. Specs describe *how* the software is built — schema, routes, runtime behavior, data shapes, API contracts. Together, they should be detailed enough that a fresh AI session with no prior context could read them and regenerate the program with high fidelity.

> **The test for whether something belongs here:** *"If this content disappeared, would the code break?"*
> Yes → it's a spec. Goes here.
> No → it's vision. Goes in [`../vision/`](../vision/README.md).

---

## What goes here

- Database schema (tables, columns, constraints, indexes)
- Route maps (paths, HTTP methods, request/response shapes)
- Runtime behavior (how the session engine, event handlers, background jobs work)
- Authentication and authorization rules
- API contracts (external integrations, internal interfaces)
- Configuration that the runtime depends on (feature flags, env-var conventions)
- Anything else where the *exact behavior* is part of the program's correctness

## What does NOT go here

- Marketing copy, audience definitions, target market — `../vision/`
- Philosophical rationale, mission statements, "why this exists" — `../vision/`
- Use-case framing — *"this could also serve X"* — `../vision/`
- Subject matter content (course catalogs, product copy, sample data) — usually `../vision/`, occasionally `data/` if the runtime consumes it
- Reasoning the user worked through while making a decision — usually nowhere; ephemeral context, not policy

The most common contamination: the user explains *why* a feature exists while specifying *how* it should work, and the AI writes both into the spec. Don't. Capture the *what* (feature) here. The *why* (purpose) goes in vision, or doesn't get written down at all.

---

## Primary spec canvases

For Hodos workflow, the primary human-reviewable spec surface is structured canvases:

```text
specs/database/{table}.csv
specs/pages/{page}/mock-up.html
specs/pages/{page}/interaction-matrix.csv
specs/pages/{page}/state-transitions.csv
```

Use one CSV per database table by default. Related tables can be treated as a domain/data structure, but avoid one giant database CSV.

Page interaction matrices should reference relevant table CSVs instead of duplicating the whole database.

Mock-ups are low-fidelity HTML visual canvases. They are part of the spec, not disposable sketches.

State/transition CSVs replace flowcharts as the usual behavior artifact when state behavior matters.

Generated JSON and Markdown views may be useful, but they are secondary:

```text
CSV/mock-up/table canvases = human intent
JSON = normalized AI/code view
Markdown = review/report view
```

If a user edits a CSV or mock-up, treat that edit as the current human intent and resync generated views.

## Supporting spec documents

`specs/` may also hold complementary JSON contracts and Markdown inventories.

1. **JSON specs (`*.json`) — contracts.** What the platform *must* do. Behavioral rules, architectural invariants, runtime contracts (e.g., "the audit log is append-only," "no dialogue retention," "instruction layers are additive"). These change when intent changes, not when implementation drifts. They are the stable, opinionated documents.

2. **Markdown inventories (`*.md`) — current-state snapshots.** What the codebase *currently is*. The template ships skeletons of eight standard inventories that every project keeps:

   | File | Purpose |
   |---|---|
   | [`SCHEMA.md`](SCHEMA.md) | Per-table data-model inventory — every column, FK, index. |
   | [`ROUTES.md`](ROUTES.md) | Per-route API + server-actions inventory — every endpoint, method, audience, body, response. |
   | [`SITE_INVENTORY.md`](SITE_INVENTORY.md) | Per-page inventory — every page, audience, content, actions, status. |
   | [`COMPONENTS.md`](COMPONENTS.md) | Per-component inventory — every reusable component and where it's used. |
   | [`GLOSSARY.md`](GLOSSARY.md) | Domain glossary — every project-specific term. |
   | [`OPERATIONS.md`](OPERATIONS.md) | Solo-dev runbook — recipes for every operational task. |
   | [`ENVIRONMENT.md`](ENVIRONMENT.md) | Env-var inventory — every var the app reads. |
   | [`DECISIONS.md`](DECISIONS.md) | ADR log — every architectural decision and why. |
   | [`tooling-philosophy.md`](tooling-philosophy.md) | Universal AI-maintained-codebase philosophy. Bundled, not project-specific. |

   Inventories are denormalizable from the codebase. They exist so future-you and future-Claude don't have to re-grep the project to answer "what does this app actually contain?" Maintain them as you change code; treat them as part of the same edit.

**Together, the canvases + generated views + inventories are the regeneration source.** CSV/mock-up/table canvases are the primary review surface; JSON contracts and Markdown inventories support implementation and auditing.

### Precedence on conflict

If a CSV/mock-up/table canvas and a generated JSON/Markdown view disagree, **the canvas wins**; regenerate or update the generated view.
If a JSON spec and a markdown inventory disagree, **the JSON spec wins** for behavioral facts unless a canvas says otherwise; update the stale artifact to match.
If the codebase and a markdown inventory disagree, **the codebase wins** (the inventory is a snapshot — regenerate it).
If the codebase and a spec canvas/JSON contract disagree, **the spec wins** unless intent has changed — in which case update the spec first as part of the same change.

---

## File format

- **CSV** is the default human-editable format for database tables, interaction matrices, and state/transition tables.
- **HTML** is the default low-fidelity page mock-up format.
- **JSON** is the normalized AI/code view when structure matters and contradictions across files need to be catchable.
- **Markdown** is the format for inventories, summaries, and review reports.
- **When you want to review and comment**, ask the AI to render a JSON spec as Markdown. Edit or comment on the MD; the AI re-syncs to JSON.

Validate JSON specs after every edit:

```bash
node -e "JSON.parse(require('fs').readFileSync('specs/<file>.json','utf8')); console.log('ok')"
```

---

## Spec-first edits

When a design conversation produces a new direction:

1. **Classify the tier** — see CLAUDE.md Working Discipline.
2. **For Tier 2/3 changes:** update the relevant CSVs/mock-ups/interaction matrices/state tables first.
3. **For Tier 1 changes:** update spec/test only if behavior changes.
4. **For Tier 0 changes:** skip spec-first.
5. **Validate JSON if you touched generated or contract JSON.**
6. **Then plan and implement.**

Spec edits are not paperwork after the fact. They are the work for any change that's bigger than a typo.

---

## Cross-spec consistency

A change that affects schema, routes, or runtime behavior often touches multiple specs. Update all of them in the same change. Do not leave residue in one spec when another moves. Stale framing in any spec poisons the regenerate-the-program guarantee.

If two specs disagree, run `git log -p specs/<file>.json` to find which intent is current and which is leftover from a prior direction. Newer wins, but verify.

---

## Audit on request — and proactively

You should be able to produce a contradiction-and-completeness audit across all specs at any time. Look for:

- Same concept described differently in two specs
- Routes / schema columns / API endpoints referenced in one spec but missing from the others
- Vision-flavored content that crept into specs (purpose, audience, market positioning)
- Behavior in code that isn't documented in any spec

The `spec-drift-check` skill (see [`../hodos.md`](../hodos.md)) handles this on demand. When you notice a drift while doing other work, flag it — don't wait to be asked.

---

## When a section grows too large

If a spec section grows past ~300 lines or ~20% of its file, consider whether it should split into its own spec — or whether part of it belongs in `vision/` (purpose contamination).

---

## Bundled with this template

This `specs/` folder ships with:

- [`tooling-philosophy.md`](tooling-philosophy.md) — universal AI-maintained-codebase philosophy: library-vs-framework rule, evaluation checklist, decision rules. Pair it with a project-specific tooling spec (your stack inventory) — see the *"How to use this in your project"* section at the bottom of that file.
- Eight empty inventory skeletons (`SCHEMA.md`, `ROUTES.md`, `SITE_INVENTORY.md`, `COMPONENTS.md`, `GLOSSARY.md`, `OPERATIONS.md`, `ENVIRONMENT.md`, `DECISIONS.md`). Each has the header + field reference + a TODO marker. Fill them in as the project takes shape; treat their maintenance as part of the same edit that introduced the table / route / page / component / decision.

Other specs are project-specific and you author them as the project takes shape (typically one JSON contract per major subsystem).
