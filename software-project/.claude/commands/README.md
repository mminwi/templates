# .claude/commands/

Slash command files for the Hodos pipeline and maintenance skills. Each `.md` in this folder is a skill that Claude can invoke. The user types or voices a slash command (e.g., `/decompose`) as an explicit override; the auto-trigger model fires the same skills based on natural-language intent.

## Looking for the menu?

**The full skill menu — trigger phrases, what each produces, how to say it — lives in [`hodos.md`](../../hodos.md) at the project root.**

That's the one to open when you want to remember how to say something. Don't look here.

## The 16 skills

- **Hodos Phase 1 (Design):** `brainstorm.md`, `decompose.md`, `write-spec.md`, `flowchart.md`, `write-plan.md`
- **Hodos Phase 2 (Audit):** `critic-review.md`
- **Hodos Phase 3 (Build):** `skeleton-first.md`, `execute-plan.md`
- **Hodos Phase 4 (Verify):** `verify.md`, `review-work.md`
- **Maintenance (investigate & assess):** `debug.md`, `update-spec.md`, `spec-drift-check.md`, `audit-subsystem.md`, `refactor.md`, `backfill-tests.md`

Note: `debug.md` corresponds to the `systematic-debug` skill in the cheat sheet — the slash command name is `/debug` (kept short for typing/voicing).

## Removed from earlier Hodos versions

- `gherkin.md` — Gherkin vocabulary (Feature/Scenario/Given/When/Then/And/But) is preserved as a primer in [`hodos.md`](../../hodos.md), but is not enforced as a workflow skill. The CLAUDE.md fragment rejects BDD frameworks as runtime dependencies.
- `spec-page.md`, `spec-event.md` — folded into the unified `write-spec.md` (the spec format adapts to the thing being specced).
- `upgrade-dependency.md` — one-off task that didn't earn a slot in the lean skill set; treat dependency upgrades as Medium-tier work that follows the normal Working Discipline.

## Where the artifacts these skills produce end up

| Artifact | Location |
|---|---|
| Plan files (Hodos) | `.claude/plans/{YYYY-MM-DD}-{task}.md` |
| Brainstorm summaries (for Big-tier work) | `.claude/plans/{YYYY-MM-DD}-{topic}-brainstorm.md` |
| Bug investigations | `.claude/plans/{YYYY-MM-DD}-bug-{name}.md` |
| Spec update proposals | `.claude/plans/{YYYY-MM-DD}-update-{spec-name}.md` |
| Drift reports | `.claude/plans/{YYYY-MM-DD}-drift-{spec-name}.md` |
| Audit reports | `.claude/plans/{YYYY-MM-DD}-audit-{subsystem}.md` |
| Refactor plans | `.claude/plans/{YYYY-MM-DD}-refactor-{name}.md` |
| Backfill plans | `.claude/plans/{YYYY-MM-DD}-backfill-tests-{target}.md` |
| New specs | `specs/{name}.json` (or `.md` on request) |
| Flowcharts | Embedded in the relevant spec file |
| Critic reports | `.claude/plans/{plan-name}-critic.md` alongside the plan |
| Review reports | `.claude/plans/{plan-name}-review.md` alongside the plan |
| Verification reports | Inline in the conversation; cited in the change manifest |

All of these get committed to git. They are the decision log.

## Adding or changing a skill

1. Edit the relevant `{skill-name}.md` file in this folder.
2. Update the skill menu in [`../../hodos.md`](../../hodos.md) with new trigger phrases or descriptions.
3. If the skill's routing should be auto-discoverable from natural language, update the routing hints in [`../../CLAUDE-add-to-project.md`](../../CLAUDE-add-to-project.md).
4. Keep the skill count tight. ~16 is the working ceiling. More than 20 starts producing skill-vs-skill confusion.
