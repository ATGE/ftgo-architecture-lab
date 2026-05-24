# AGENTS.md

## Project Purpose

This repository is the FTGO Architecture Lab. It documents an incremental
migration from a legacy Java/WAR monolith to microservices using Strangler Fig
over 18-24 months.

The deliverable is architectural documentation, not runnable application code:
PRD, FSD, ADRs, Mermaid C4 diagrams, improved prompts, and local agent skills.

## Source of Truth

Read the relevant source documents before editing:

- `README.md` for the repo map, review flow, and rubric self-check.
- `docs/PRD.md` for product context, stakeholders, capabilities, and NFRs.
- `docs/FSD.md` for use cases and Given/When/Then scenarios.
- `docs/adr/0001-architecture-style.md` for Strangler Fig migration.
- `docs/adr/0002-ipc-and-data-strategy.md` for REST/HTTPS, events, and DB-per-service.
- `docs/diagrams/*.mmd` for C4 diagrams.

Do not invent FTGO domain concepts that are not traceable to the brief, PRD,
FSD, ADRs, or current diagrams.

## Repository Map

- `docs/PRD.md`: lightweight product requirements.
- `docs/FSD.md`: lightweight functional specification and use cases.
- `docs/adr/`: accepted architecture decisions.
- `docs/diagrams/`: Mermaid C4 source diagrams and diagram README.
- `docs/agent-skills/`: provenance and reinstall notes for local skills.
- `docs/skills/`: repo-local agent skill guidance for C4/Mermaid work.
- `prompts_mejorados/`: improved prompts and prompt-quality evidence.

## Working Rules

- Inspect the current files before changing them.
- Keep edits scoped to the requested artifact.
- Preserve traceability across brief, PRD, FSD, ADRs, C4 diagrams, and README.
- Use ASCII unless the target file already uses non-ASCII or the content needs it.
- Do not touch unrelated untracked files or user changes.
- Do not commit automatically.
- Prefer concise documentation over duplicating long explanations already present elsewhere.

## Diagram Rules

- `docs/diagrams/c4_context.mmd` is the C4 level 1 context diagram.
- `docs/diagrams/c4_container.mmd` is the readable/evaluator-friendly C4 level 2 diagram.
- Keep `C4Context` and `C4Container` directives valid.
- Keep `System_Boundary(ftgo, "FTGO Platform")` in the container diagram.
- Every `Rel(...)` in the container diagram must include a technology/protocol argument.
- Preserve Stripe, Google Maps, SendGrid/Twilio, and Legacy Monolith unless the user explicitly changes scope.
- Keep the Strangler Fig migration context visible.

## Agent Skills

Use local skills as guidance, not as executable tooling:

- `docs/skills/c4-model/SKILL.md` for C4 semantic review.
- `docs/skills/diagramming-architecture/SKILL.md` for Mermaid visual readability.

Do not assume Claude-specific commands are available. In Codex, read the skill
files and apply their guidance directly.

## Validation Commands

Useful checks from the repository root:

```bash
git status --short
cat docs/PRD.md
cat docs/FSD.md
cat docs/adr/0001-architecture-style.md
cat docs/adr/0002-ipc-and-data-strategy.md
cat docs/diagrams/c4_context.mmd
cat docs/diagrams/c4_container.mmd
```

Mermaid render checks, when Node.js and Mermaid CLI can run:

```bash
timeout 120s npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_context.mmd -o /tmp/c4_context.svg
timeout 120s npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container.mmd -o /tmp/c4_container.svg
```

If Mermaid CLI times out or fails because of the environment, report the exact
command and result instead of hiding the failed validation.

## Security and Dependencies

- Do not execute third-party scripts copied from external skill repositories.
- Do not add dependency managers, install scripts, or generated artifacts unless the user asks or the repo needs them.
- Review external sources before trusting them.
- Keep local skill provenance in `docs/agent-skills/SKILLS_SOURCES.md`.

## Final Response Expectations

When finishing a task, report:

- Files created or modified.
- Validation commands run and their results.
- Mermaid render result if diagrams changed.
- Any known pending validation or environment limitation.
