# Installing Agent Skills

This repository stores agent skills under `docs/skills/` so they are
trackable project artifacts for Codex-assisted work. They are not installed
under `.claude/skills/` because this repo is using Codex and the skills are
kept as local guidance, not as Claude Code runtime commands.

## Reinstall or Update

Run from the repository root.

```bash
tmpdir="$(mktemp -d)"
git clone https://github.com/robertanton81/architecture-diagram-skill.git "$tmpdir/architecture-diagram-skill"
git clone https://github.com/cheriftj/c4-model-skill.git "$tmpdir/c4-model-skill"

git -C "$tmpdir/architecture-diagram-skill" checkout a556bed26b8ac92d5afe956a971c953fa96f2810
git -C "$tmpdir/c4-model-skill" checkout 5b24dda00b38a402da59237a8b8685aaa588a07e

mkdir -p docs/skills/diagramming-architecture/knowledge/mermaid
mkdir -p docs/skills/diagramming-architecture/knowledge/shared
mkdir -p docs/skills/diagramming-architecture/references
mkdir -p docs/skills/c4-model

cp "$tmpdir/architecture-diagram-skill/skill/knowledge/mermaid/c4-syntax.md" docs/skills/diagramming-architecture/knowledge/mermaid/
cp "$tmpdir/architecture-diagram-skill/skill/knowledge/shared/c4-model.md" docs/skills/diagramming-architecture/knowledge/shared/
cp "$tmpdir/architecture-diagram-skill/skill/references/c4-mapping.md" docs/skills/diagramming-architecture/references/
cp -R "$tmpdir/c4-model-skill/skills/c4-model/." docs/skills/c4-model/

git -C "$tmpdir/architecture-diagram-skill" rev-parse HEAD
git -C "$tmpdir/c4-model-skill" rev-parse HEAD
```

After updating, refresh `docs/agent-skills/SKILLS_SOURCES.md` with the new
commit hashes, copied files, license notes, and security review result.

## Verify in Codex

Codex can use these files as repo-local instructions and references by reading:

- `docs/skills/diagramming-architecture/SKILL.md`
- `docs/skills/c4-model/SKILL.md`

The upstream `c4-model-skill` documents Claude-style commands such as
`/c4m:review` and `/c4m:update`. In this Codex repo, use the same intent in
plain language instead, for example:

```text
Use docs/skills/c4-model/SKILL.md to review docs/diagrams/c4_container.mmd.
Use docs/skills/diagramming-architecture/SKILL.md to improve Mermaid readability.
```

## Recommended Workflow

1. Review C4 semantics with `docs/skills/c4-model/SKILL.md`.
2. Refactor visual readability with
   `docs/skills/diagramming-architecture/SKILL.md`.
3. Validate Mermaid rendering.
4. Review `git diff` before committing.

## Render Checks

```bash
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_context.mmd -o /tmp/c4_context.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container.mmd -o /tmp/c4_container.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_order_checkout.mmd -o /tmp/c4_container_order_checkout.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_kitchen_ticket.mmd -o /tmp/c4_container_kitchen_ticket.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_delivery_flow.mmd -o /tmp/c4_container_delivery_flow.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_event_flow.mmd -o /tmp/c4_container_event_flow.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container_data_ownership.mmd -o /tmp/c4_container_data_ownership.svg
```
