# Hodos — the Methodology

**Hodos** (Greek: ὁδός — *"way," "path"*; the root of *method*, *exodus*, *synod*) is the four-phase gate-driven methodology this template enforces for AI-assisted software change — adding, modifying, or extending the system.

A short cheat sheet. **Open this while talking with the AI.** Refer back when you forget the right word for what you want.

---

## How to invoke each skill — what to say

Speak naturally. You don't type slash commands — you describe intent and Claude routes to the matching skill via the triggers listed below. Multiple phrasings per skill are recognized so your natural language works. Slash command names exist as explicit overrides when the auto-trigger guesses wrong.

*This is the single source of truth for the menu. If you add or change a skill, update this section.*

### Phase 1 — Design (no code yet)

| Say this                                                                              | Skill        | Slash         | Produces                                                                                     |
| ------------------------------------------------------------------------------------- | ------------ | ------------- | -------------------------------------------------------------------------------------------- |
| *"Let's brainstorm," "what could we do about X," "I'm thinking about Y but not sure"* | `brainstorm` | `/brainstorm` | Surfaces tradeoffs and asks what you're really trying to do — *before* any planning artifact |
| *"Let's decompose this," "break X into pieces," "slice this up"*                      | `decompose`  | `/decompose`  | Tree: epic → feature → component                                                             |
| *"Spec out X," "document how X works," "write the spec for Y"*                        | `write-spec` | `/write-spec` | Feature/event spec — JSON by default, MD on request                                          |
| *"Make a flowchart for X," "diagram X," "draw the state machine for X"*               | `flowchart`  | `/flowchart`  | Mermaid diagram (graph / state / sequence / ER, chosen to fit)                               |
| *"Write the plan," "let's plan this," "plan this out"*                                | `write-plan` | `/write-plan` | Checkbox plan file in `plans/` (Big-tier changes only)                                       |

### Phase 2 — Audit

| Say this                                                                 | Skill           | Slash            | Produces                                                         |
| ------------------------------------------------------------------------ | --------------- | ---------------- | ---------------------------------------------------------------- |
| *"Run the critic," "audit this plan," "find flaws," "review the design"* | `critic-review` | `/critic-review` | Severity-sorted flaw report. Best run in a separate AI instance. |

### Phase 3 — Build

| Say this                                                                                                   | Skill            | Slash             | Produces                                                                                          |
| ---------------------------------------------------------------------------------------------------------- | ---------------- | ----------------- | ------------------------------------------------------------------------------------------------- |
| *"Frame it up," "write the framework," "framework first," "skeleton first," "show me the structure first"* | `skeleton-first` | `/skeleton-first` | File paths + function signatures + empty bodies. Waits for your approval before logic is written. |
| *"Execute the plan," "build it," "run the plan," "implement this"*                                         | `execute-plan`   | `/execute-plan`   | Implementation, checkbox by checkbox, no improvising                                              |

### Phase 4 — Verify

| Say this                                                          | Skill                            | Slash          | Produces                                                                         |
| ----------------------------------------------------------------- | -------------------------------- | -------------- | -------------------------------------------------------------------------------- |
| *(Fires automatically before claiming work is done)*              | `verification-before-completion` | `/verify`      | Confirms behavior with concrete checks — does it actually work, not just compile |
| *"Review the work," "does this match the plan," "audit the diff"* | `review-work`                    | `/review-work` | Pass / Pass-with-notes / Needs-fix report. Best run in a separate AI instance.   |

### Maintenance — Investigate & Assess

| Say this                                                                  | Skill              | Slash               | Produces                                                                                           |
| ------------------------------------------------------------------------- | ------------------ | ------------------- | -------------------------------------------------------------------------------------------------- |
| *"Let's debug this," "why is X failing," "find the bug"*                  | `systematic-debug` | `/debug`            | Four-phase investigation (reproduce → pattern → hypothesize → fix). Stops after 3 failed attempts. |
| *"Update the spec," "fix the spec for X," "I changed Y, update the spec"* | `update-spec`      | `/update-spec`      | Targeted edit proposals. Structure preserved.                                                      |
| *"Check for spec drift," "is the spec still accurate," "scan the specs"*  | `spec-drift-check` | `/spec-drift-check` | Drift report — spec-wrong / code-wrong / both-wrong / accurate                                     |
| *"Audit the X module," "how healthy is X," "check the health of X"*       | `audit-subsystem`  | `/audit-subsystem`  | Green/Yellow/Red report across categories                                                          |
| *"Refactor X," "clean up X without changing behavior," "restructure X"*   | `refactor`         | `/refactor`         | Characterization tests → refactor → same tests still pass                                          |
| *"Backfill tests for X," "add tests to X," "cover X with tests"*          | `backfill-tests`   | `/backfill-tests`   | Tests capturing actual current behavior                                                            |

---

## Common confusions — disambiguating similar phrases

Some phrases are ambiguous. The AI will ask to clarify in these cases; here's the breakdown for your own reference.

### "Fix the spec" / spec work

| If the intent is...                     | Skill                       |
| --------------------------------------- | --------------------------- |
| Write a new spec from scratch           | `write-spec` (create)       |
| Targeted edit of an existing spec       | `update-spec` (edit)        |
| Report where specs are stale (no edits) | `spec-drift-check` (assess) |

### "Fix this" / bug vs refactor

| If the problem is...                               | Skill                                |
| -------------------------------------------------- | ------------------------------------ |
| Something is broken / wrong output / crash         | `systematic-debug`                   |
| Code works but is ugly / duplicated / hard to read | `refactor`                           |
| Code works but has no tests                        | `backfill-tests`                     |
| Code works but has significant debt                | `audit-subsystem` first, then triage |

### "Let's plan this" / planning granularity

| If the work is...                    | Skill                                           |
| ------------------------------------ | ----------------------------------------------- |
| Too big to plan as one unit          | `decompose` first, then `write-plan` on a slice |
| Bounded, scope is clear              | `write-plan` directly                           |
| Not sure what we're trying to do yet | `brainstorm` first                              |

---

## The tier rule — when to skip Hodos

Hodos costs time. Match overhead to the change.

| Change type                                     | Required Hodos phases        |
| ----------------------------------------------- | ---------------------------- |
| Typo fix, comment edit, one-line doc change     | None — just do it            |
| Single-file bug fix, <10 min                    | Phase 4 verify only          |
| Single-feature change, one module               | Phases 1 (lean) + 3 + 4      |
| Multi-module change, new feature, schema change | Full Hodos (all four phases) |

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

The four phases of Hodos are those hard gates. Each phase produces an artifact (plan file, flowchart, Gherkin scenarios, skeleton, review output) that you can inspect and approve. The artifacts are saved. If something goes wrong later, you can look at the artifacts and see exactly where the reasoning drifted.

---

## The four phases of Hodos

### Phase 1 — Decompose & Design

**Goal:** before any code, produce the design artifacts that describe *what* the change is, *where* it lives, and *what "done" looks like.*

**Why it matters:** the "Lost in the Middle" problem — an AI that starts coding before the scope is pinned will drift by turn 20. Fresh context + pinned scope = focused execution.

**Key artifacts:**

- **Decomposition tree** — epic → feature → component. You commit to one slice at a time.
- **Feature spec** — for any new feature. JSON by default (rigorous, validatable). MD on request when you want to review and comment.
- **Flowchart** — Mermaid diagram for process flows and state changes.
- **Plan file** — numbered checkbox list with file paths and "done" criteria. Big-tier changes only.

**What you approve:** the artifacts. If the flowchart is wrong, fix it now. Changing a flowchart costs 30 seconds. Changing code costs hours.

---

### Phase 2 — Audit (the Critic Agent gate)

**Goal:** find flaws in the design *before* code is written.

**Why it matters:** the builder and the reviewer have different attention patterns. The builder wants to be helpful and make progress. The reviewer looks for what's wrong. Asking one AI to do both tasks creates conflict of interest.

**Pattern:**

1. Freeze the design artifacts from Phase 1.
2. Invoke `critic-review` (ideally in a **fresh, separate AI instance** — new chat or second AI tool).
3. The critic is primed as "ruthless Senior QA Architect. Find contradictions, missing edge cases, integration flaws. Do not write code."
4. Flaws come back. Builder fixes the design. Loop if needed.
5. Only when the critic finds nothing substantive do you proceed to Phase 3.

**Two-AI variant (highest assurance):** Builder AI drafts the design. Critic AI reviews cold without seeing the builder's reasoning. User arbitrates disagreements. This is the "AIs playing against each other" pattern — highest-leverage technique in Hodos.

---

### Phase 3 — Build

**Goal:** write code that matches the approved design exactly.

**Two sub-steps:**

- **Skeleton first** — module structure, class/function signatures, docstrings. Empty bodies. Verify the *shape* before any logic exists. (The user prefers the word **framework** to "skeleton" — same thing.)
- **Fill in** — implement against the skeleton and the test scenarios. Check off plan items. If the plan is wrong, **stop and revise it** — don't improvise off-plan.

---

### Phase 4 — Verify

**Goal:** catch drift between plan and implementation before the work is declared done.

**Why it matters:** AI gets tired. By the end of a long task, details slip. A fresh reviewer persona catches what the builder missed.

**What gets checked:**

- Does the diff match the plan?
- Does the code actually work — not just compile, but produce the expected behavior?
- Were there silent deviations from the skeleton?
- Were any side effects missed?

**Output:** pass / pass-with-notes / needs-fix. Builder fixes issues. Loop until clean.

---

## Anti-patterns Hodos is designed to prevent

These are the failure modes that motivated each gate:

1. **Drifting off-plan mid-task.** The AI starts executing, realizes it could do something cleverer, pivots, and the plan no longer describes what was built. *Fix:* plan is a contract. Change the plan file first, then execute.
2. **Brainstorming substituting for planning.** You spend 30 minutes discussing options with the AI. No artifact is produced. Next session, the context is gone and you discuss it again. *Fix:* every design discussion produces an artifact in `plans/` or `specs/`.
3. **Review by the same AI that wrote it.** *"Yes, my code looks correct."* Always does. *Fix:* fresh context for review, ideally a different AI instance.
4. **Reasoning treated as rules.** User reasons out loud about whether to use option A or B; AI saves the in-the-moment thought as a permanent rule. *Fix:* the firewall above. Confirm before promoting.
5. **Vision contaminating specs.** User explains *why* a feature exists; AI writes the *why* into the *spec* alongside the feature. Three months later the spec is half marketing copy. *Fix:* the two-path rule. Test: would the code break if this disappeared?
6. **Implementation before behavior is defined.** You write code, then write tests for what it happens to do. Tests rubber-stamp the implementation. *Fix:* behavior contract first (Gherkin or equivalent), code meets the contract.
7. **40-minute silent stretches.** AI runs off and works without checking in. User can't tell if it's stuck or making progress. *Fix:* phase announcements when work runs long ("Inventorying," "Editing," "Verifying").
8. **"Done" without verification.** AI says it's complete, hands it off, user discovers it doesn't work. *Fix:* `verification-before-completion` is mandatory before claiming done.

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
- **Drift** — gradual divergence between the spec (what we said the system does) and the code (what it actually does). `spec-drift-check` finds it.
- **Spec-first** — design and document the change in the spec *before* writing code. Required for Medium and Big tier changes.
