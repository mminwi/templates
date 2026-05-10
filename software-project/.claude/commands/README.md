# .claude/commands/

Slash command files for the Hodos pipeline and maintenance skills. Each `.md` in this folder is a skill that Claude can invoke. The user types or voices a slash command (e.g., `/write-plan`) as an explicit override; the auto-trigger model fires the same skills based on natural-language intent.

## Looking for the menu?

**The full skill menu — trigger phrases, what each produces, how to say it — lives in [`hodos.md`](../../hodos.md) at the project root.**

That's the one to open when you want to remember how to say something. Don't look here.

## Main workflow skills

The main Hodos build workflow is now:

```text
brainstorm → write-spec → write-plan → execute-plan
```

- **`brainstorm.md`** — classify, recommend, decompose, define composer guidance.
- **`write-spec.md`** — create/update CSV canvases, page mock-ups, interaction matrices, state tables, generated views.
- **`write-plan.md`** — create checkbox plan, notebook entry, test plan updates, skeleton phase if needed, critic loop.
- **`execute-plan.md`** — execute plan, run tests, update notebook, require separate-agent verification.

## Maintenance skills

- `debug.md`
- `audit-subsystem.md` (includes spec drift and test coverage assessment)
- `refactor.md`

Note: `debug.md` corresponds to the `systematic-debug` skill in the cheat sheet — the slash command name is `/debug` (kept short for typing/voicing).

## Removed from earlier Hodos versions

- `gherkin.md` — Gherkin vocabulary (Feature/Scenario/Given/When/Then/And/But) is preserved as a primer in [`hodos.md`](../../hodos.md), but is not enforced as a workflow skill. The CLAUDE.md fragment rejects BDD frameworks as runtime dependencies.
- `spec-page.md`, `spec-event.md` — folded into the unified `write-spec.md` (the spec format adapts to the thing being specced).
- `decompose.md` — folded into `brainstorm.md`.
- `flowchart.md` — replaced by state/transition CSVs except when a one-off diagram is useful.
- `critic-review.md` — folded into the automatic critic loop in `write-plan.md`.
- `skeleton-first.md` — folded into `write-plan.md` as a skeleton/framework phase.
- `verify.md`, `review-work.md` — folded into `execute-plan.md` as final verification and separate-agent review.
- `update-spec.md` — folded into `write-spec.md` as targeted edit mode.
- `spec-drift-check.md` — folded into `audit-subsystem.md` as the spec drift agent.
- `backfill-tests.md` — folded into `audit-subsystem.md` as test coverage assessment and test plan recommendations.
- `upgrade-dependency.md` — one-off task that didn't earn a slot in the lean skill set; treat dependency upgrades as Medium-tier work that follows the normal Working Discipline.

## Where the artifacts these skills produce end up

| Artifact | Location |
|---|---|
| Plan files (Hodos) | `plans/{YYYY-MM-DD}-{task}.md` |
| Notebook entries | `notebook/{YYYY-MM-DD}-{topic}.md` |
| Test plan | `tests/TEST_PLAN.md` |
| Bug investigations | `plans/{YYYY-MM-DD}-bug-{name}.md` |
| Spec update proposals | `plans/{YYYY-MM-DD}-update-{spec-name}.md` |
| Audit reports (includes spec drift + test coverage) | `plans/{YYYY-MM-DD}-audit-{subsystem}.md` |
| Refactor plans | `plans/{YYYY-MM-DD}-refactor-{name}.md` |
| Spec canvases | `specs/database/*.csv`, `specs/pages/{page}/interaction-matrix.csv`, `specs/pages/{page}/mock-up.html` |
| Critic reports | `plans/{plan-name}-critic.md` when produced by the automatic plan loop |
| Review reports | `plans/{plan-name}-review.md` when separate-agent verification is written to disk |

All of these get committed to git. They are the decision log.

## Adding or changing a skill

1. Edit the relevant `{skill-name}.md` file in this folder.
2. Update the skill menu in [`../../hodos.md`](../../hodos.md) with new trigger phrases or descriptions.
3. If the skill's routing should be auto-discoverable from natural language, update the routing hints in [`../../CLAUDE-add-to-project.md`](../../CLAUDE-add-to-project.md).
4. Keep the skill count tight. ~16 is the working ceiling. More than 20 starts producing skill-vs-skill confusion.
