# C4 Diagrams

This directory contains the Mermaid C4 diagrams for the FTGO architecture lab.

## Files

| File | Purpose |
|---|---|
| `c4_context.mmd` | C4 level 1 context diagram for FTGO, actors, and external systems. |
| `c4_container.mmd` | C4 level 2 overview required by the rubric, optimized for readability. |
| `c4_container_order_flow.mmd` | Focused C4 Container view for order taking, kitchen tickets, and payment. |
| `c4_container_delivery_flow.mmd` | Focused C4 Container view for delivery assignment, tracking, maps, and notifications. |
| `c4_container_events_data.mmd` | Focused C4 Container view for ADR 0002 IPC and data strategy. |
| `c4_container_full.mmd` | Full-detail container diagram preserved from the previous version. |

The overview keeps the semantics from ADR 0001 and ADR 0002 while avoiding a
single dense canvas. The focused diagrams split order/payment, delivery, and
events/data concerns so the architecture remains readable. `Service-owned DBs`
represents DB-per-service ownership without showing one database per service in
the readable views. `c4_container_full.mmd` keeps the full-detail version for
traceability.

## Render

Requires Node.js and Mermaid CLI through `npx`.

```bash
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_context.mmd -o /tmp/c4_context.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container.mmd -o /tmp/c4_container.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_order_flow.mmd -o /tmp/c4_container_order_flow.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_delivery_flow.mmd -o /tmp/c4_container_delivery_flow.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_events_data.mmd -o /tmp/c4_container_events_data.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_full.mmd -o /tmp/c4_container_full.svg
```

For repository artifacts, prefer reviewing the `.mmd` sources and generating
SVG/PNG only when needed for presentation or validation.
