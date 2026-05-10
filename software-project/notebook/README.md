# notebook/

The engineering notebook is the project trace log.

It records what happened, why it happened, what was expected, what was learned, and what future AI/humans should know. It is not legal/IP protection in this template, and it is not a duplicate of plans, specs, tests, or git history.

## When to create or update an entry

Any time work uses a plan file, the notebook is engaged too.

```text
If the work is significant enough for `write-plan`, it is significant enough for a notebook entry.
```

Also update the notebook for:

- Significant brainstorm/spec convergence sessions
- Experiments
- Major debugging sessions with lessons learned
- Important design decisions
- Failed approaches worth remembering
- Non-obvious equipment/environment/customer/data constraints

Skip for tiny edits, typos, and obvious one-line fixes.

## Naming

Use a flat directory:

```text
notebook/{YYYY-MM-DD}-{short-topic}.md
```

Example:

```text
notebook/2026-05-10-contact-page-rewrite.md
```

## Entry template

```markdown
# Notebook — {Topic}

**Date:** {YYYY-MM-DD}
**Related plan:** {link}
**Related specs:** {links}
**Related tests:** {links}

## Objective

What are we doing?

## Expected Outcome

What do we hope will happen?

## Context Not Captured Elsewhere

Record important facts that may not exist in code/specs/plans: equipment, environment, customer context, data source, device model, external constraint, failure condition, or operational detail.

## Process Summary

Short overview of the approach.

## Stage Notes

### Brainstorm

### Spec Update

### Planning

### Execution

### Verification

## Result

What happened?

## Lessons / Follow-up

What should future AI/humans remember?
```

## Rules

- Link to full plans/specs/tests instead of pasting them.
- Summarize each stage without duplicating the full artifact.
- Capture non-obvious context that would not be discoverable from source files.
- Record surprises, failures, and unresolved gaps.
- Keep entries useful for future AI sessions with limited context.
