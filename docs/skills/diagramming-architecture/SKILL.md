---
name: diagramming-architecture
description: Refactor and review C4 architecture diagrams written in Mermaid, with emphasis on visual readability, concise relationships, and render validation. Use for Mermaid C4Context/C4Container diagrams in this repository.
---

# Architecture Diagramming for Mermaid C4

This is a curated, repository-local copy derived from
`architecture-diagram-skill` for Codex-assisted work on FTGO diagrams.

The upstream skill also supports IcePanel and includes Python helper scripts.
Those parts are intentionally not copied here because this repository only
needs Mermaid C4 review and refactoring guidance.

## Scope

Use this skill when working on:

- Mermaid `C4Context` and `C4Container` diagrams.
- Visual readability improvements for architecture diagrams.
- C4 relationship labels and protocol/technology annotations.
- Render validation with Mermaid CLI.

Do not use this skill to push diagrams to IcePanel or run upstream helper
scripts; those files are not installed in this repo.

## Workflow

1. Determine the diagram level and audience.
2. Review the current diagram for C4 abstraction level, missing actors,
   external systems, containers, databases, queues, and relationship labels.
3. Reduce visual density where possible:
   - Keep C4 level 2 focused on deployable containers and key data stores.
   - Group repetitive technical elements when the architectural decision can
     remain clear without many individual nodes.
   - Shorten aliases, labels, and descriptions.
   - Prefer a small number of high-signal relationships over exhaustive arrows.
4. Preserve semantic intent from PRD, FSD, and ADRs.
5. Render the Mermaid diagram and inspect the output before final delivery.
6. Document any preserved full-detail version if a diagram is simplified.

## Mermaid C4 Checks

- Use the correct Mermaid directive: `C4Context`, `C4Container`,
  `C4Component`, `C4Dynamic`, or `C4Deployment`.
- Keep abstraction levels separate.
- Give every element a short description.
- Give every container an explicit technology.
- Make relationships unidirectional.
- Label relationships with concrete intent, not generic "Uses" or "Calls".
- Include protocol/technology for inter-container relationships.
- Keep labels concise enough to render legibly.

## Visual Readability Checks

| Issue | Preferred fix |
|---|---|
| Too many visible nodes | Group repetitive technical nodes or split into companion diagrams |
| Too many long arrows | Keep only key architectural relationships |
| Wide one-row layout | Tune or remove `UpdateLayoutConfig` |
| Long labels | Shorten to action-oriented phrases |
| Repeated databases | Use a grouped DB container when DB-per-service is the decision |
| Missing migration context | Keep legacy/external systems visible when relevant |

## Rendering

Use Mermaid CLI when Node.js is available:

```bash
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_context.mmd -o /tmp/c4_context.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container.mmd -o /tmp/c4_container.svg
```

If rendering fails because of local environment constraints, keep the command,
error, and pending validation note in the final report.

## Local References

- `knowledge/shared/c4-model.md`
- `knowledge/mermaid/c4-syntax.md`
- `references/c4-mapping.md`
