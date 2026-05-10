# Hodos — the Methodology

**Hodos** (Greek: ὁδός — *"way," "path"*; the root of *method*, *exodus*, *synod*) is the gate-driven methodology this template enforces for AI-assisted software change — adding, modifying, or extending the system.

A short cheat sheet. **Open this while talking with the AI.** Refer back when you forget the right word for what you want.

---

## How to invoke each skill — what to say

Speak naturally. You don't type slash commands — you describe intent and Claude routes to the matching skill via the triggers listed below. Multiple phrasings per skill are recognized so your natural language works. Slash command names exist as explicit overrides when the auto-trigger guesses wrong.

*This is the single source of truth for the menu. If you add or change a skill, update this section.*

### Main workflow

| Say this | Skill | Slash | Produces |
|---|---|---|---|
| *"Let's brainstorm," "what could we do about X," "how should we handle Y," "break this up"* | `brainstorm` | `/brainstorm` | Intake classification, one strong recommendation, small-slice decomposition, composer guidance, notebook summary |
| *"Update the spec," "spec out X," "make the CSVs," "make the mock-up," "new page," "scaffold specs for X," "reverse engineer this into specs"* | `write-spec` | `/write-spec` | CSV canvases, page mock-ups, interaction matrices, state/transition tables, generated views. **Bootstrap mode** creates the full initial canvas set for new pages/entities/modules. |
| *"Write the plan," "let's plan this," "plan the implementation"* | `write-plan` | `/write-plan` | Checkbox plan, notebook link, `tests/TEST_PLAN.md` updates, skeleton phase if needed, automatic critic loop |
| *"Execute the plan," "build it," "run the plan," "implement this"* | `execute-plan` | `/execute-plan` | Implementation, tests, notebook closeout, separate-agent verification before completion |

Normal sequence:

```text
brainstorm → write-spec → write-plan → execute-plan
```

Folded steps:

- `decompose` is part of `brainstorm`.
- `flowchart` is replaced by state/transition CSVs except as an optional diagram helper.
- `critic-review` is part of `write-plan`.
- `skeleton-first` is a phase inside `write-plan` when work is substantial.
- `verify` and `review-work` are final gates inside `execute-plan`.

### Maintenance — Investigate & Assess

| Say this                                                                  | Skill              | Slash               | Produces                                                                                           |
| ------------------------------------------------------------------------- | ------------------ | ------------------- | -------------------------------------------------------------------------------------------------- |
| *"Let's debug this," "why is X failing," "find the bug"*                  | `systematic-debug` | `/debug`            | Four-phase investigation (reproduce → pattern → hypothesize → fix). Stops after 3 failed attempts. |
| *"Audit X," "how healthy is X," "check for spec drift," "is the spec accurate," "does X have enough tests"* | `audit-subsystem` | `/audit-subsystem` | Full health check: code hygiene, spec drift, test coverage + test plan recommendations, ops/security. Green/Yellow/Red grade. |
| *"Refactor X," "clean up X without changing behavior," "restructure X"*   | `refactor`         | `/refactor`         | Characterization tests → refactor → same tests still pass                                          |

---

## Common confusions — disambiguating similar phrases

Some phrases are ambiguous. The AI will ask to clarify in these cases; here's the breakdown for your own reference.

### "Update the spec" / spec work

| If the intent is...                     | Skill                       |
| --------------------------------------- | --------------------------- |
| Create or revise CSVs/mock-ups/spec canvases | `write-spec`          |
| Small targeted edit to an existing spec | `write-spec` (targeted edit mode) |
| Report where specs are stale (no edits) | `audit-subsystem` (includes spec drift) |

### "Fix this" / bug vs refactor

| If the problem is...                               | Skill                                |
| -------------------------------------------------- | ------------------------------------ |
| Something is broken / wrong output / crash         | `systematic-debug`                   |
| Code works but is ugly / duplicated / hard to read | `refactor`                           |
| Code works but has no tests                        | `audit-subsystem` (assesses coverage + recommends test plan updates) |
| Code works but has significant debt                | `audit-subsystem` first, then triage |

### "Let's plan this" / planning granularity

| If the work is...                    | Skill                                           |
| ------------------------------------ | ----------------------------------------------- |
| Too big to plan as one unit          | `brainstorm` decomposes it first                |
| Bounded, scope is clear              | `write-plan` directly                           |
| Not sure what we're trying to do yet | `brainstorm` first                              |

---

## The tier rule — when to skip Hodos

Hodos costs time. Match overhead to the change.

| Tier | Use when | Required process |
|---|---|---|
| Tier 0 — Tiny | Safe edit; no meaningful behavior change | Direct edit + quick verification |
| Tier 1 — Small | Local behavior/code change; low risk | Lean implementation + verification; update spec/test if behavior changes |
| Tier 2 — Medium | Several files, one feature/page, meaningful behavior | Brainstorm if needed → spec canvases → plan/notebook/test plan → execute with reviewer |
| Tier 3 — Big | Multi-agent, multi-module, schema, architecture, high risk | Brainstorm with decomposition → spec canvases → composer → plan + critic loop + skeleton + test plan → execute + separate reviewer |

The instinct test: *"If something went wrong here, how bad would it be?"* Bad answer → run full Hodos. Low stakes → skip.

---

## The two-path rule — specs vs vision

`specs/` and `vision/` are different folders with different rules.

**The test:** *If this content disappeared, would the code break?*
- **Yes** → it's a spec. Goes in `specs/`.
- **No** → it's vision. Goes in `vision/`.

| Content type                                               | Where                                                                                      |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Database schema, route map, runtime behavior               | `specs/`                                                                                   |
| API request/response shapes, auth rules                    | `specs/`                                                                                   |
| Marketing copy, audience definitions, market positioning   | `vision/`                                                                                  |
| Philosophical rationale, "why this exists"                 | `vision/`                                                                                  |
| Use-case framing — *"this could also serve X market"*      | `vision/`                                                                                  |
| Configuration data the software consumes (catalog, prices) | Either — usually `vision/` if values are domain-specific, `specs/` if values are universal |

**The leak you have to prevent:** the user explains *why* a feature exists while specifying *how* it should work. Both feel relevant. *Don't* write the *why* into the spec. Write the *what* (feature) in the spec; the *why* (purpose) goes in vision, or doesn't get written down at all if it's just reasoning.

---

## Don't promote reasoning to rules

The user talks in long voice-dictated streams with strong context. They reason out loud while making decisions. Reasoning is *not* policy.

**The test:** *"Would the user, three weeks from now, be surprised to find this written down as a rule?"* If yes, don't write it down without an explicit *"make this a rule."*

**Example exchange:**

> **User:** *"I'm leaning toward option C for the template repo, though I'm not sure GitHub is the right place for everything we'll do…"*

> **Wrong response:** *"Going with option C. Saving a memory: 'User prefers not to use GitHub for some content types.'"*

> **Right response:** *"Going with option C — separate Git repo. You mentioned hesitation about GitHub for some things; treating that as in-the-moment reasoning, not a rule. Tell me if you want it captured as a permanent constraint."*

The cost of asking *"is this a rule?"* once is tiny. The cost of an unintended rule three weeks later is real.

---

## Core philosophy — compliance over understanding

AI models treat paragraphs of text as *recommendations* that can be compromised. Soft specs drift. The only reliable way to keep an AI on rails is **hard gates** — explicit artifacts the AI must produce and the user must approve before the next step is allowed.

The main Hodos workflow is those hard gates expressed as four SOPs: brainstorm, write-spec, write-plan, and execute-plan. Each SOP produces or updates durable artifacts. If something goes wrong later, you can look at the artifacts and see exactly where the reasoning drifted.

---

## The four main SOPs of Hodos

### 1 — Brainstorm

**Goal:** classify the request, converge on direction, and decompose large work into small slices before artifacts are written.

**Why it matters:** the "Lost in the Middle" problem — an AI that starts coding before the scope is pinned will drift by turn 20. Fresh context + pinned scope = focused execution.

**Key outputs:**

- Intake classification and tier
- One strong recommendation, not padded options
- Small-slice decomposition when needed
- Composer guidance: file/page ownership, interfaces, parallel vs series
- Notebook summary for Tier 2/3 work

**What you approve:** the direction and slice boundaries. If the decomposition is wrong, fix it before specs or plans are written.

---

### 2 — Write Spec

**Goal:** capture intended product behavior in structured spec canvases before planning implementation.

**Why it matters:** prose drifts. CSVs, mock-ups, interaction matrices, and state tables give the user a reviewable surface and give the AI a tighter contract.

**Bootstrap mode:** when a new page, database entity, or module has no spec canvases yet, write-spec creates the full initial artifact set first — mock-up, interaction matrix, database CSV, state transitions if applicable — so there is something concrete to review and edit before proceeding.

**Key artifacts:**

- Database CSVs — usually one CSV per table
- Page mock-ups — low-fidelity HTML visual canvas
- Page interaction matrices
- State/transition CSVs when state behavior matters
- Generated JSON/Markdown views only when useful

**What you approve:** the structured spec artifacts. If the mock-up or CSV is wrong, fix it before planning implementation.

---

### 3 — Write Plan

**Goal:** translate aligned spec canvases into an executable checkbox plan.

**What the plan includes:**

- File paths and done criteria
- Execution model: owner, writable files, interfaces, parallel/series
- Notebook link and summary
- `tests/TEST_PLAN.md` updates
- Skeleton/framework phase when work is substantial
- Automatic critic loop for non-trivial plans

**Critic role:** the critic asks whether the plan/design will actually achieve the intended outcome. Critic review is part of planning, not something the user must manually trigger.

### 4 — Execute Plan

**Goal:** implement the approved plan exactly and verify completion before claiming done.

**Execution rules:**

- Check off plan tasks in real time
- Stop if the plan is wrong instead of improvising
- Run tests/manual checks from the test plan
- Update notebook with result, surprises, and follow-up
- Use a separate short-context reviewer before marking complete

**Reviewer role:** the reviewer asks, "This is what the agent was told to do. Did it actually do it?" It is intentionally narrower than the critic.

**Done means:** a separate reviewer can verify that the approved plan was executed, not that the builder thinks it is close enough.

---

## Anti-patterns Hodos is designed to prevent

These are the failure modes that motivated each gate:

1. **Drifting off-plan mid-task.** The AI starts executing, realizes it could do something cleverer, pivots, and the plan no longer describes what was built. *Fix:* plan is a contract. Change the plan file first, then execute.
2. **Brainstorming substituting for planning.** You spend 30 minutes discussing options with the AI. No artifact is produced. Next session, the context is gone and you discuss it again. *Fix:* Tier 2/3 brainstorms update the notebook and hand off to specs/plans.
3. **Review by the same AI that wrote it.** *"Yes, my code looks correct."* Always does. *Fix:* fresh context for review, ideally a different AI instance.
4. **Reasoning treated as rules.** User reasons out loud about whether to use option A or B; AI saves the in-the-moment thought as a permanent rule. *Fix:* the firewall above. Confirm before promoting.
5. **Vision contaminating specs.** User explains *why* a feature exists; AI writes the *why* into the *spec* alongside the feature. Three months later the spec is half marketing copy. *Fix:* the two-path rule. Test: would the code break if this disappeared?
6. **Implementation before behavior is defined.** You write code, then write tests for what it happens to do. Tests rubber-stamp the implementation. *Fix:* spec canvases and test plan before implementation.
7. **40-minute silent stretches.** AI runs off and works without checking in. User can't tell if it's stuck or making progress. *Fix:* phase announcements when work runs long ("Inventorying," "Editing," "Verifying").
8. **"Done" without verification.** AI says it's complete, hands it off, user discovers it doesn't work. *Fix:* separate-agent verification is mandatory inside `execute-plan`.

---

## Vocabulary primer

Words you'll hear from the AI that are worth knowing precisely so your input lands the way you intend.

### Gherkin (test-scenario vocabulary)

Even though Hodos doesn't enforce a BDD framework, the Gherkin vocabulary is useful for explaining *what should happen when*. Six words:

- **Feature** — the thing being tested or specified (one per file usually)
- **Scenario** — a specific case or situation
- **Given** — the starting state (preconditions)
- **When** — the action that happens
- **Then** — the expected outcome
- **And / But** — continuations when you need more than one Given, When, or Then

Example: *"Given the user is signed in, when they click 'Delete account,' then the account is marked deleted but their order history remains."*

Speaking in this shape makes specs much easier for the AI to translate into structured behavior.

### Other terms

- **Skeleton / framework** — file paths + function signatures + empty bodies, with no logic. Same thing; the user prefers "framework."
- **Characterization tests** — tests written to capture the *current* behavior of code (not the *desired* behavior). Used before refactoring so you can prove behavior didn't change.
- **Idempotent** — an operation that can be run multiple times without side effects beyond the first run. Webhook handlers, migration scripts, etc., should be idempotent.
- **Audit gate** — a separate-context review step before declaring something done. The reviewer doesn't have the builder's context and can spot what was missed.
- **Hard gate vs soft text** — a *hard gate* requires producing a specific artifact before the next step is allowed. *Soft text* is a recommendation in prose that the AI may or may not follow.
- **Drift** — gradual divergence between the spec (what we said the system does) and the code (what it actually does). `audit-subsystem` finds it as part of the full health check.
- **Spec-first** — design and document the change in the spec *before* writing code. Required for Medium and Big tier changes.
