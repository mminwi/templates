---
name: flowchart
description: "Compatibility/optional diagram helper. Main workflow uses state/transition CSVs in write-spec for implementable behavior. Use this only when the user explicitly wants a Mermaid diagram."
---

# flowchart

Produce a Mermaid diagram for a process flow, state change, data flow, or sequence of events when a diagram is useful. This is not a normal main workflow artifact.

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

- Optional helper when the user explicitly wants a diagram
- Implementable state behavior usually belongs in a state/transition CSV created by `write-spec`
