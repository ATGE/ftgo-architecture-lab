# C4 Diagrams

This directory contains the Mermaid C4 diagrams for the FTGO architecture lab.
Use `prompts_mejorados/c4_mejorado.md` to generate or review these diagrams
with a readability-first C4 Mermaid workflow. Semantic validation is guided by
the local `c4-model-skill`; if `/c4m:review` is unavailable, document the
manual review against `docs/skills/c4-model/SKILL.md`.

## Files

| File | Purpose |
|---|---|
| `c4_context.mmd` | C4 level 1 context diagram for FTGO, actors, and external systems. |
| `c4_container.mmd` | C4 level 2 overview required by the rubric, optimized for readability. |
| `c4_container_order_checkout.mmd` | Focused C4 Container view for order creation and payment authorization. |
| `c4_container_kitchen_ticket.mmd` | Focused C4 Container view for restaurant ticket acceptance/rejection. |
| `c4_container_delivery_flow.mmd` | Focused C4 Container view for delivery assignment, tracking, maps, and notifications. |
| `c4_container_event_flow.mmd` | Focused C4 Container view for asynchronous IPC from ADR 0002. |
| `c4_container_data_ownership.mmd` | Focused C4 Container view for DB-per-service ownership from ADR 0002. |

The overview keeps the semantics from ADR 0001 and ADR 0002 while avoiding a
single dense canvas. The focused diagrams replace the older combined
order-flow and events-data views because they separate concerns and reduce
visual noise. `Service-owned DBs` represents DB-per-service ownership without
showing one database per service in the readable views.

## Recommended Reading

1. `c4_context.mmd`
2. `c4_container.mmd`
3. `c4_container_order_checkout.mmd`
4. `c4_container_kitchen_ticket.mmd`
5. `c4_container_delivery_flow.mmd`
6. `c4_container_event_flow.mmd`
7. `c4_container_data_ownership.mmd`

## Render

Requires Node.js and Mermaid CLI through `npx`.

```bash
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_context.mmd -o /tmp/c4_context.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container.mmd -o /tmp/c4_container.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_order_checkout.mmd -o /tmp/c4_container_order_checkout.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_kitchen_ticket.mmd -o /tmp/c4_container_kitchen_ticket.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_delivery_flow.mmd -o /tmp/c4_container_delivery_flow.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_event_flow.mmd -o /tmp/c4_container_event_flow.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_data_ownership.mmd -o /tmp/c4_container_data_ownership.svg
```

For repository artifacts, prefer reviewing the `.mmd` sources and generating
SVG/PNG only when needed for presentation or validation.
