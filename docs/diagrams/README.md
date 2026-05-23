# C4 Diagrams

This directory contains the Mermaid C4 diagrams for the FTGO architecture lab.

## Files

| File | Purpose |
|---|---|
| `c4_context.mmd` | C4 level 1 context diagram for FTGO, actors, and external systems. |
| `c4_container.mmd` | C4 level 2 readable/evaluator-friendly container diagram. |
| `c4_container_full.mmd` | Full-detail container diagram preserved from the previous version. |

The readable container diagram keeps the semantics from ADR 0001 and ADR 0002:
incremental Strangler Fig migration, hybrid REST/HTTPS plus domain events, and
DB-per-service ownership represented as a grouped `Service-owned DBs` container
to reduce visual density.

## Render

Requires Node.js and Mermaid CLI through `npx`.

```bash
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_context.mmd -o /tmp/c4_context.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container.mmd -o /tmp/c4_container.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_full.mmd -o /tmp/c4_container_full.svg
```

For repository artifacts, prefer reviewing the `.mmd` sources and generating
SVG/PNG only when needed for presentation or validation.
