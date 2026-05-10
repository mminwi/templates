---
description: Break a large task into a tree of epic → feature → component so the user only commits to one slice at a time
---

# /decompose

Compatibility note: decomposition is now normally part of `/brainstorm`. Use this command directly only when the user explicitly asks for a standalone decomposition artifact.

## When to use

Invoke when the user describes a task that is:
- Multi-feature (e.g., "redocument the whole system")
- Unclear in scope (e.g., "make the dashboard better")
- Multi-module (touches DB + UI + integrations)
- Or simply too big to plan as a single unit

Do **not** use for a single-file change or a task with obvious bounded scope.

## What you must produce

A decomposition tree, three levels deep, written to `plans/{YYYY-MM-DD}-{task-name}-decomposition.md`.

```markdown
# Decomposition — {task name}

**Date:** {YYYY-MM-DD}
**Requested by:** {user}
**Scope:** {one paragraph describing the whole task in the user's words}

---

## Epic: {high-level goal}

### Feature 1: {name}
- **Component 1.1:** {name} — {one-line what it does}
- **Component 1.2:** ...

### Feature 2: {name}
- **Component 2.1:** ...
- **Component 2.2:** ...

### Feature 3: {name}
- ...

---

## Recommended execution order

1. **Start with:** Feature X / Component X.Y
   - **Reason:** {why this first — blocking, smallest, riskiest, etc.}
2. **Next:** ...
3. **Later (defer):** ...

---

## Out of scope (intentionally)

- {things the user might expect but should NOT be part of this work}
- {scope creep risks to flag}

---

## Open questions for the user

- [ ] {question the user must answer before proceeding}
- [ ] {ambiguity that could not be resolved without input}
```

## Rules

1. **Three levels, no more.** Epic → Feature → Component. If a component looks big, that's a flag — ask the user if it should become its own feature.
2. **Do not plan the whole tree in detail.** The point is to reveal scope, not to pre-plan every component.
3. **Recommend a starting point.** The user needs to know which slice to pick first. Default to smallest-and-riskiest (reveals surprises early).
4. **Call out out-of-scope.** Explicit exclusions prevent scope creep later.
5. **Ask clarifying questions up front.** If you're guessing about something load-bearing, stop and ask.

## Output handoff

After the decomposition is approved, the user picks one component. Then invoke `/write-plan` (for implementation work) or `/write-spec` (for documentation work) on that one component only.

## What you do NOT do in this skill

- Do not write flowcharts; state behavior now usually belongs in state/transition CSVs during `/write-spec`
- Do not write tests (that's `/backfill-tests` if capturing current behavior, or part of `/execute-plan` if implementing new code)
- Do not write code
- Do not produce more than the decomposition file
