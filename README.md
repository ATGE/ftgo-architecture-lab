# FTGO Architecture Lab

Documentacion arquitectonica inicial para migrar **FTGO** (*Food To Go*) desde su monolito legacy Java/WAR hacia una arquitectura de microservicios de forma incremental, aplicando el patron **Strangler Fig** durante 18-24 meses.

El caso FTGO proviene del libro *Microservices Patterns* de Chris Richardson (Manning, 2019). Este repositorio cubre la fase de documentacion: PRD, FSD, ADRs, diagramas C4 y prompts de IA mejorados, con trazabilidad explicita entre cada artefacto.

## Contenido

- [Artefactos del repositorio](#artefactos-del-repositorio)
- [Estructura del proyecto](#estructura-del-proyecto)
- [Fuentes y referencias](#fuentes-y-referencias)
- [Como revisar los artefactos](#como-revisar-los-artefactos)
- [Como renderizar los diagramas Mermaid](#como-renderizar-los-diagramas-mermaid)
- [Como invocar los prompts mejorados](#como-invocar-los-prompts-mejorados)
- [Metricas de calidad de prompts](#metricas-de-calidad-de-prompts)
- [Self-check contra rubrica](#self-check-contra-rubrica)
- [Entrega](#entrega)

## Artefactos del repositorio

| # | Artefacto | Ruta | Descripcion |
|---:|---|---|---|
| 1 | PRD ligero | `docs/PRD.md` | Contexto, stakeholders, 7 capacidades de negocio, 11 NFRs medibles y alcance. |
| 2 | FSD ligero | `docs/FSD.md` | 6 casos de uso con Given/When/Then, actor primario, capacidad PRD y origen. |
| 3 | ADR 1 - Estilo arquitectonico | `docs/adr/0001-architecture-style.md` | 3 opciones evaluadas; decision: migracion incremental con Strangler Fig. |
| 4 | ADR 2 - IPC y datos | `docs/adr/0002-ipc-and-data-strategy.md` | 3 opciones evaluadas; decision: modelo hibrido REST + eventos con DB-per-service. |
| 5 | Diagrama C4 nivel 1 | `docs/diagrams/c4_context.mmd` | Diagrama de contexto Mermaid (`C4Context`). |
| 6 | Diagrama C4 nivel 2 | `docs/diagrams/c4_container.mmd` | Diagrama de contenedores Mermaid (`C4Container`). |
| 7 | Prompt mejorado PRD | `prompts_mejorados/prd_mejorado.md` | Prompt con 4 TODOs resueltos, Verification, Changelog y Metrica. |
| 8 | Prompt mejorado FSD | `prompts_mejorados/fsd_mejorado.md` | Prompt con 4 TODOs resueltos, Verification, Changelog y Metrica. |

## Estructura del proyecto

```text
.
├── README.md
├── docs/
│   ├── PRD.md
│   ├── FSD.md
│   ├── adr/
│   │   ├── 0001-architecture-style.md
│   │   └── 0002-ipc-and-data-strategy.md
│   └── diagrams/
│       ├── c4_context.mmd
│       └── c4_container.mmd
└── prompts_mejorados/
    ├── prd_mejorado.md
    └── fsd_mejorado.md
```

Los casos de uso se mantienen dentro de `docs/FSD.md`. No se crea un directorio separado para UCs porque la consigna pide documentacion ligera y evaluable.

## Fuentes y referencias

La fuente canonica de dominio es el brief del caso FTGO y el libro de Richardson. Cada artefacto cita su origen usando las siguientes convenciones:

| Referencia | Contenido |
|---|---|
| `Brief §A.1` | Contexto de negocio y migracion incremental. |
| `Brief §A.2` | Stakeholders. |
| `Brief §A.3` | Capacidades de negocio (7 del Cap. 2 de Richardson). |
| `Brief §A.4` | Restricciones tecnicas y NFRs base (10 restricciones). |
| `Brief §A.5` | User stories semilla: `US-01`, `US-02`, `US-03`. |
| `Richardson Cap. 1` | Sintomas del monolito y motivacion para microservicios. |
| `Richardson Cap. 2` | Descomposicion por capacidades de negocio. |

## Como revisar los artefactos

Navegar cada archivo en terminal:

```bash
cat README.md
cat docs/PRD.md
cat docs/FSD.md
cat docs/adr/0001-architecture-style.md
cat docs/adr/0002-ipc-and-data-strategy.md
cat docs/diagrams/c4_context.mmd
cat docs/diagrams/c4_container.mmd
cat prompts_mejorados/prd_mejorado.md
cat prompts_mejorados/fsd_mejorado.md
```

Revisar estado del repositorio:

```bash
git status --short
git log --oneline -10
find . -maxdepth 3 -type f -not -path './.git/*' | sort
```

## Como renderizar los diagramas Mermaid

**Opcion A - Mermaid CLI** (requiere Node.js):

```bash
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_context.mmd -o docs/diagrams/c4_context.svg
npx -y @mermaid-js/mermaid-cli -i docs/diagrams/c4_container.mmd -o docs/diagrams/c4_container.svg
```

**Opcion B - Mermaid Live Editor** (sin instalacion):

1. Abrir <https://mermaid.live/>.
2. Pegar el contenido de `docs/diagrams/c4_context.mmd` o `docs/diagrams/c4_container.mmd`.
3. Verificar que renderice correctamente como `C4Context` o `C4Container`.

**Opcion C - GitHub**: Los archivos `.mmd` se renderizan automaticamente en la vista previa de GitHub si el repositorio es publico.

## Como invocar los prompts mejorados

Los prompts estan disenados para copiarse o referenciarse en un asistente de IA (Gemini, Claude, ChatGPT, etc.).

```text
@prompts_mejorados/prd_mejorado.md genera un PRD ligero para FTGO usando el brief como fuente.
@prompts_mejorados/fsd_mejorado.md genera un FSD ligero para FTGO a partir de docs/PRD.md y las US semilla.
```

**Entrada recomendada para `prd_mejorado.md`:**

- Brief del caso FTGO como contexto.
- Restriccion: no inventar dominio fuera de FTGO.
- Salida esperada: `docs/PRD.md`.

**Entrada recomendada para `fsd_mejorado.md`:**

- `docs/PRD.md` generado previamente.
- User stories semilla `US-01`, `US-02`, `US-03`.
- Salida esperada: `docs/FSD.md`.

## Metricas de calidad de prompts

| Prompt | Metrica | Antes (seed) | Despues (mejorado) | Evidencia |
|---|---|---:|---:|---|
| PRD | Secciones obligatorias completas | 3/5 promedio | 5/5 objetivo | Evaluacion local/manual de 3 corridas simuladas |
| PRD | NFRs con metrica y origen | 40 % promedio | 100 % objetivo | Evaluacion local/manual de 3 corridas simuladas |
| FSD | UCs completos con Given/When/Then | 3/5 promedio | 6/6 objetivo | Evaluacion local/manual de 3 corridas simuladas |
| FSD | UCs trazables a PRD/brief/libro | 60 % promedio | 100 % objetivo | Evaluacion local/manual de 3 corridas simuladas |

La evidencia detallada y las tablas de cada corrida estan en:

- `prompts_mejorados/prd_mejorado.md` → seccion `## Metrica`
- `prompts_mejorados/fsd_mejorado.md` → seccion `## Metrica`

## Self-check contra rubrica

| Criterio | Peso | Estado |
|---|---:|---|
| Coherencia brief → PRD → FSD → ADR → C4 | 25 % | Trazabilidad explicita con `Brief §A.*`, `US-*` y `Richardson Cap.*` en cada documento. |
| Calidad PRD + FSD | 20 % | PRD con 11 NFRs medibles y 7 capacidades; FSD con 6 UCs y Given/When/Then completos. |
| Calidad ADRs | 20 % | 2 ADRs con 3 opciones cada uno, trade-offs, impacto en NFRs y consecuencias positivas/negativas. |
| Diagramas C4 | 10 % | Nivel 1 (contexto) y nivel 2 (contenedores) en Mermaid, con protocolos y tecnologias en todas las relaciones. |
| Prompts mejorados | 15 % | 2 prompts con 4 TODOs resueltos, Verification, Changelog y Metrica con 3 corridas. |
| README ejecutable | 10 % | Estructura, tabla de artefactos, comandos reproducibles, metricas reportadas y self-check. |

## Entrega

La entrega final se publica en la rama obligatoria `release/exam-lab`.
