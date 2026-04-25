---
name: flowchart
description: "Use this when a process, state machine, data flow, or sequence of events needs a visual diagram. Produces a Mermaid diagram (graph / state / sequence / ER, chosen to fit the content). Embedded in the relevant spec file or saved standalone."
---

# flowchart

Produce a Mermaid diagram for a process flow, state change, data flow, or sequence of events. Pick the right diagram type for the content.

## When this skill fires

Auto-fires on:

- *"Make a flowchart for X"*
- *"Diagram X"*
- *"Draw the state machine for X"*
- *"Show the flow"*
- *"Visualize how X works"*

## What this skill does

- Picks the appropriate Mermaid diagram type:
  - `graph` for general process / decision flows
  - `stateDiagram-v2` for state machines
  - `sequenceDiagram` for ordered interactions between components
  - `erDiagram` for data relationships
  - `flowchart` (alias of graph) when the content fits a top-down or left-right layout best
- Embeds the diagram in the relevant spec file when one exists, or produces a standalone Markdown file
- Keeps the diagram concrete — actual node names from the system, not abstractions

## Full instructions

Complete behavior contract — Mermaid syntax conventions, diagram-type selection rules, embedding patterns — in [`../../commands/flowchart.md`](../../commands/flowchart.md).

## Related skills

- Usually invoked during `write-spec` or as part of `update-spec` to add a visual to an existing spec
- The flowchart is an artifact of Phase 1 (Design); does not generate code
