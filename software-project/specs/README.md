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

## File format

- **JSON** is the default for schema-heavy specs (database tables, API routes, plan matrices). Structure is enforced; contradictions across files are easier to spot.
- **Markdown** is fine for prose-heavy specs (runtime behavior described in narrative form, complex flows that benefit from narrative context).
- **When you want to review and comment**, ask the AI to render a JSON spec as Markdown. Edit or comment on the MD; the AI re-syncs to JSON.

Validate JSON specs after every edit:

```bash
node -e "JSON.parse(require('fs').readFileSync('specs/<file>.json','utf8')); console.log('ok')"
```

---

## Spec-first edits

When a design conversation produces a new direction:

1. **Decide the tier** (Small / Medium / Big — see CLAUDE.md Working Discipline).
2. **For Medium and Big changes:** update the relevant spec *first*. Code follows. The spec is the contract; if the spec is wrong, the code that follows it is wrong.
3. **For Small changes** (typos, prose tweaks, single-file bugfixes): skip spec-first. Just code.
4. **Validate JSON if you touched a JSON spec.**
5. **Then implement.**

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
