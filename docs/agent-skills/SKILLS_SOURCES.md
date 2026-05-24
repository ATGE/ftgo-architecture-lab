# Agent Skills Sources

Installed on: 2026-05-23

These skills are third-party guidance artifacts copied into this repository for
Codex-assisted C4/Mermaid diagram work. Review their contents before relying on
them. No third-party scripts were executed during installation; only `git clone`
and file copy operations were used.

## diagramming-architecture

| Field | Value |
|---|---|
| Local path | `docs/skills/diagramming-architecture/` |
| Source | <https://github.com/robertanton81/architecture-diagram-skill> |
| Commit | `a556bed26b8ac92d5afe956a971c953fa96f2810` |
| License | MIT declared in upstream `package.json`; no `LICENSE` file was present in the cloned source |
| Purpose in this repo | Mermaid C4 visual refactor and readability guidance |

Copied files:

- `SKILL.md` as a curated Mermaid-only local version.
- `skill/knowledge/mermaid/c4-syntax.md` -> `knowledge/mermaid/c4-syntax.md`
- `skill/knowledge/shared/c4-model.md` -> `knowledge/shared/c4-model.md`
- `skill/references/c4-mapping.md` -> `references/c4-mapping.md`

Omitted upstream files:

- `skill/scripts/analyze_codebase.py`
- `skill/scripts/push_to_icepanel.py`
- IcePanel references and knowledge files.
- Node/package files and SDK source files.

Minimum security review:

- `SKILL.md` read before installation.
- Upstream executable scripts found: `analyze_codebase.py`,
  `push_to_icepanel.py`.
- Those executable scripts were not copied and were not executed.
- Relevant dependency for local use: Node.js plus Mermaid CLI through `npx`
  only when rendering diagrams.

## c4-model

| Field | Value |
|---|---|
| Local path | `docs/skills/c4-model/` |
| Source | <https://github.com/cheriftj/c4-model-skill> |
| Commit | `5b24dda00b38a402da59237a8b8685aaa588a07e` |
| License | MIT, from upstream `LICENSE` |
| Purpose in this repo | Semantic review of C4 model levels, elements, and relationships |

Copied files:

- Full contents of upstream `skills/c4-model/*`.

Minimum security review:

- `SKILL.md` read before installation.
- No executable scripts were present under `skills/c4-model/`.
- Relevant dependency for local use: none for reading/review; Node.js plus
  Mermaid CLI only when rendering diagrams.
- No third-party scripts were executed.
