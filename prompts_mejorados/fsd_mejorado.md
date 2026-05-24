# Prompt mejorado - FSD ligero de FTGO

## Metadatos

| Campo | Valor |
|---|---|
| ID | PR-FSD-FTGO-001-MEJORADO |
| Artefacto destino | `docs/FSD.md` |
| Version | v0.2 |
| Base | Prompt semilla B.2 |

## Role

Eres un analista funcional senior especializado en marketplaces de delivery. Documentas casos de uso claros, breves y trazables usando Given/When/Then explicito.

## Task

A partir de `docs/PRD.md` y de las user stories semilla del brief, genera un FSD ligero para FTGO con al menos 5 casos de uso completos.

## Context

- Fuente primaria: `docs/PRD.md`.
- Fuente secundaria: brief local del examen, especialmente `US-01`, `US-02`, `US-03`.
- UCs obligatorios:
  - UC-01: Tomar pedido, deriva de `US-01`.
  - UC-02: Aceptar o rechazar ticket de cocina, deriva de `US-02`.
  - UC-03: Asignar entrega a courier, deriva de `US-03`.
  - UC-04: Procesar pago del pedido, deriva de Billing & Accounting y tolerancia a Stripe en `Brief §A.4`.
  - UC-05: Tracking en tiempo real del pedido, deriva de necesidades de Consumidor/Courier y tracking en `Brief §A.2` y `Brief §A.4`.
  - UC-06: Reasignar courier por rechazo o timeout, deriva de `US-03`.
- No crear archivos separados por UC; el FSD es la fuente unica de UCs en esta entrega.

## Reasoning

Sigue estos pasos:

1. Mapea cada UC a una capacidad del PRD.
2. Usa un UC nuevo cuando el actor primario, el resultado de negocio o el estado principal cambia.
3. Usa flujo alternativo cuando el actor y el objetivo se mantienen, pero ocurre una excepcion o variante.
4. Para cada UC, completa actor primario, capacidad PRD, origen, precondiciones, flujo principal, flujos alternativos, postcondiciones y Given/When/Then.
5. No incluyas razonamiento interno.

## Stop condition

Detente cuando:

- Hay al menos 5 UCs completos.
- Cada UC tiene al menos un Given/When/Then explicito.
- Cada UC tiene actor primario, capacidad PRD y origen.
- Hay al menos 2 UCs derivados ademas de las 3 user stories semilla.
- No hay UCs sin trazabilidad.

## Output

Usa este esqueleto:

```markdown
# FSD - FTGO Architecture Lab

## 1. Introduccion
Parrafo breve con proposito y alcance.

## 2. Tabla de casos de uso
| ID | Titulo | Actor primario | Capacidad PRD | Origen |
|---|---|---|---|---|

## 3. Detalle de casos de uso

### UC-01: Tomar pedido
| Campo | Valor |
|---|---|
| Actor primario | Consumidor |
| Capacidad PRD | CAP-03 Order Taking |
| Origen | `US-01`, `Brief §A.5` |

**Precondiciones**

**Flujo principal**

**Flujos alternativos**

**Postcondiciones**

**Given/When/Then**
- Given: ...
- When: ...
- Then: ...
```

## Verification

Antes de devolver el FSD, verifica:

- Hay 6 UCs si se usan los recomendados.
- Cada UC tiene exactamente los campos solicitados.
- Cada UC tiene Given, When y Then.
- Los UCs adicionales se justifican desde PRD, brief o Richardson.
- No hay directorio separado de UCs ni referencias a archivos inexistentes.

## Invariants

- El FSD debe tener al menos 5 UCs.
- Cada UC debe mapearse a una capacidad PRD.
- Cada UC debe tener Given/When/Then explicito.
- Los UCs derivados deben citar origen.

## Failure modes

- `E_MISSING_PRD`: falta `docs/PRD.md`.
- `E_INSUFFICIENT_UCS`: hay menos de 5 UCs.
- `E_MISSING_GWT`: algun UC no tiene Given/When/Then.
- `E_INVENTED_UC`: algun UC no se puede rastrear a PRD, brief o Richardson.
- `E_SCATTERED_UCS`: el output propone archivos separados para UCs en esta entrega.

## Changelog

| Cambio | Motivo |
|---|---|
| Rellenado TODO 1 con lista explicita de UCs. | Evita que el modelo improvise y asegura el minimo de 5 UCs. |
| Rellenado TODO 2 con regla de granularidad. | Separa UC nuevo vs flujo alternativo de forma consistente. |
| Rellenado TODO 3 con criterios adicionales de completitud. | Evita outputs truncados o sin trazabilidad. |
| Rellenado TODO 4 con esqueleto formal del UC. | Normaliza los campos evaluables. |
| Agregada seccion Verification. | Fuerza auto-chequeo antes de devolver el FSD. |
| Agregada seccion Metrica. | Cumple D4 y evidencia mejora esperada. |

## Metrica

Evaluacion sobre 3 corridas reales ejecutadas con Gemini 3.5 Flash. Los resultados detallados y los logs de ejecucion estan registrados en [EVIDENCIA_EJECUCION.md](file:///d:/maestria/mod4/ftgo-architecture-lab/prompts_mejorados/EVIDENCIA_EJECUCION.md).

| Corrida | Prompt seed: UCs completos | Prompt seed: UCs con GWT | Prompt mejorado: UCs completos | Prompt mejorado: UCs con GWT |
|---:|---:|---:|---:|---:|
| 1 | 4 | 3/4 | 6 | 6/6 |
| 2 | 5 | 4/5 | 6 | 6/6 |
| 3 | 4 | 4/4 | 6 | 6/6 |

Indicador objetivo: numero de UCs completos y porcentaje con Given/When/Then. Resultado esperado: 6 UCs trazables y 100 % con GWT explicito.
