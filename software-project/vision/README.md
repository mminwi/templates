# vision/

This folder contains the **vision documents** for this project. Vision describes *why* the software exists — its purpose, audience, market positioning, philosophical rationale, marketing language. The software runs without these documents; they exist so people understand what they're looking at and why decisions were made.

> **The test for whether something belongs here:** *"If this content disappeared, would the code break?"*
> No → it's vision. Goes here.
> Yes → it's a spec. Goes in [`../specs/`](../specs/README.md).

---

## What goes here

- **Mission and founding documents** — why this project exists, what problem it solves, the principles behind it
- **Audience definitions** — who uses this, who buys it, who it's marketed to
- **Market positioning** — where this fits among alternatives, what it's better at, what it deliberately doesn't do
- **Marketing language** — voice, tone, customer-facing copy, product story
- **Philosophical rationale** — *"we do X because we believe Y"* — the reasoning behind opinionated design choices
- **Use-case framing** — *"this could also serve X market," "imagine Y persona using this for Z"* — speculative use cases that shape product direction without binding the code
- **Strategic decisions and their reasoning** — pivots, retired directions, deliberate scope cuts, "why we said no to X"

## What does NOT go here

- Database schema, route maps, runtime behavior — `../specs/`
- API contracts, auth rules, configuration the runtime depends on — `../specs/`
- Reasoning the user worked through while making a decision — *usually nowhere.* Ephemeral context for a single decision is not vision; it's noise. Capture vision content as deliberate writing, not stream-of-consciousness extraction.

---

## Why specs and vision are separated

Specs and vision get conflated for a reason: when you build software, *why* and *how* are linked in your head. You decide to build feature X because of customer Y in market Z. The decision lives at the intersection.

The reason to separate them in the documents is that **the AI maintains the program by reading the specs.** If specs are full of *why* and *who* and *what market*, every code change forces the AI to read marketing copy first. That's expensive in context window and noisy for reasoning. The cleaner the specs, the better the AI works.

Vision is for humans (and for the occasional AI conversation about strategy). Specs are for the program.

---

## How to subdivide vision/

The template ships with `vision/` as a single folder. Subdivide as the project grows:

```
vision/
  founding-brief.md         ← mission, principles, why this exists
  audience.md               ← who uses it, who pays, who it serves
  positioning.md            ← market, alternatives, what we don't do
  marketing/
    voice.md                ← tone and voice rules for customer copy
    landing-page.md         ← the hero, the pitch, the CTA framing
  pedagogy/                 ← (project-specific) how we approach the work
  catalog/                  ← (project-specific) domain content/configuration
```

Or keep it flat if the project is small. The template doesn't impose a structure — only the rule that vision-flavored content goes here, not in `specs/`.

---

## When the user gives you vision content

The user often explains purpose while explaining a feature. *"We're building this for affluent families who already hire tutors,"* during a conversation about the signup flow.

The right move:
1. Capture the **feature** (signup flow behavior) in `specs/`.
2. Capture the **audience framing** (*"affluent families who hire tutors"*) in `vision/audience.md` if it's been stated as a deliberate strategic choice.
3. If the audience framing was just reasoning to explain *why* a feature is shaped a certain way, **don't write it down at all.** Stream-of-consciousness reasoning is not vision; it's context for one decision.

The firewall rule (see CLAUDE.md and `../hodos.md`): *"Would the user, three weeks from now, be surprised to find this written down?"* If yes, don't capture it without explicit confirmation.

---

## Vision documents are reference material, not live contracts

Specs are contracts the code must obey. Vision is reference material that explains *why* the code is shaped the way it is. Vision documents can become stale without breaking the program, so they don't carry the same drift-check discipline as specs.

That said, when vision changes meaningfully (a strategic pivot, a market shift, retirement of a direction), update the vision documents in the same pass as the related code changes. The vision is for humans walking in cold; outdated vision documents mislead them.
