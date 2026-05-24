# Prompt mejorado - PRD ligero de FTGO

## Metadatos

| Campo | Valor |
|---|---|
| ID | PR-PRD-FTGO-001-MEJORADO |
| Artefacto destino | `docs/PRD.md` |
| Version | v0.2 |
| Base | Prompt semilla B.1 |

## Role

Eres un arquitecto de software senior con experiencia en marketplaces de delivery y migraciones desde monolito a microservicios. Conoces el caso FTGO de *Microservices Patterns* de Chris Richardson y aplicas DDD estrategico para identificar capacidades sin inventar dominio fuera del brief.

## Task

Genera un PRD ligero para FTGO en Markdown. Debe servir como entrada para un FSD, dos ADRs y diagramas C4. El PRD debe ser claro, breve y trazable.

## Context

- Fuente principal: brief local del examen, Anexo A.
- Fuente secundaria: Richardson, *Microservices Patterns*, capitulos 1 y 2.
- Stakeholders del brief: Consumidor, Restaurante, Courier, Empleado FTGO, Equipo de arquitectura y Sistemas externos.
- Capacidades obligatorias: Consumer Management, Restaurant Management, Order Taking, Order Fulfillment / Kitchen, Delivery, Billing & Accounting y Notifications.
- Restricciones: no inventar stakeholders, capacidades ni NFRs fuera de `Brief §A.1` a `Brief §A.4`, `US-01` a `US-03` o Richardson.

## Reasoning

Sigue estos pasos:

1. Extrae contexto, stakeholders, capacidades y restricciones del brief.
2. Redacta un PRD de 2 a 4 paginas equivalentes.
3. Cita el origen de cada decision usando `Brief §A.*`, `US-*` o `Richardson Cap.*`.
4. Para cada NFR, declara ID, metrica, origen y justificacion.
5. Declara alcance dentro/fuera, incluyendo convivencia con monolito y Strangler Fig.
6. No muestres razonamiento interno.

## Stop condition

Detente cuando el output cumpla todo esto:

- Tiene exactamente 5 secciones principales: Contexto y objetivos, Stakeholders, Capacidades de negocio, Requisitos no funcionales y Alcance.
- Incluye las 7 capacidades de negocio del brief.
- Incluye al menos 8 NFRs.
- Cada NFR tiene ID, metrica, origen y justificacion.
- No excede 4 paginas equivalentes.

## Output

Usa este esqueleto:

```markdown
# PRD - FTGO Architecture Lab

## 1. Contexto y objetivos
Parrafo breve con problema del monolito y objetivo de migracion. Origen: `Brief §A.1`, `Richardson Cap. 1`.

## 2. Stakeholders
| Stakeholder | Necesidad principal | Origen |
|---|---|---|

## 3. Capacidades de negocio
| ID | Capacidad | Responsabilidad | Origen |
|---|---|---|---|

## 4. Requisitos no funcionales
| ID | NFR | Metrica | Origen | Justificacion |
|---|---|---|---|---|

## 5. Alcance
### Entra en alcance
### Fuera de alcance
```

## Verification

Antes de devolver el PRD, verifica:

- No aparecen stakeholders fuera del brief.
- Hay 7 capacidades y todas citan `Brief §A.3` o `Richardson Cap. 2`.
- Cada NFR tiene una metrica verificable.
- El alcance menciona migracion incremental y monolito legacy.
- No se copio el examen completo ni se agrego un anexo del brief.

## Invariants

- El PRD debe citar el brief en cada NFR.
- El PRD debe cubrir las 7 capacidades de negocio.
- El PRD no debe inventar stakeholders.
- El PRD debe ser ligero.

## Failure modes

- `E_MISSING_BRIEF`: no se proporciono el brief.
- `E_INVENTED_DOMAIN`: aparecen stakeholders, capacidades o NFRs sin origen.
- `E_INCOMPLETE_NFR`: hay NFRs sin metrica u origen.
- `E_OVERPRODUCED`: el output excede el tamano solicitado.

## Changelog

| Cambio | Motivo |
|---|---|
| Rellenado TODO 1 con stakeholders concretos. | Reduce variacion y evita inventar actores. |
| Rellenado TODO 2 con las 7 capacidades del brief. | Asegura cobertura completa de Richardson Cap. 2. |
| Rellenado TODO 3 con criterios numericos de completitud. | Permite evaluar automaticamente si el PRD esta listo. |
| Rellenado TODO 4 con esqueleto exacto de salida. | Normaliza estructura y facilita trazabilidad. |
| Agregada seccion Verification. | Fuerza auto-chequeo antes de devolver el output. |
| Agregada seccion Metrica. | Cumple D4 y evidencia mejora esperada. |

## Metrica

Evaluacion sobre 3 corridas reales ejecutadas con Gemini 3.5 Flash. Los resultados detallados y los logs de ejecucion estan registrados en [EVIDENCIA_EJECUCION.md](./EVIDENCIA_EJECUCION.md).

| Corrida | Prompt seed: secciones completas | Prompt seed: NFRs con metrica+origen | Prompt mejorado: secciones completas | Prompt mejorado: NFRs con metrica+origen |
|---:|---:|---:|---:|---:|
| 1 | 4/5 | 4/8 | 5/5 | 11/11 |
| 2 | 3/5 | 3/8 | 5/5 | 11/11 |
| 3 | 4/5 | 5/8 | 5/5 | 11/11 |

Indicador objetivo: completitud estructural del PRD y trazabilidad de NFRs. Resultado esperado: pasar de cobertura parcial a 100 % en secciones obligatorias y NFRs trazables.
