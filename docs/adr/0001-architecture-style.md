# ADR 0001: Estilo arquitectonico para migrar FTGO

**Status**: Accepted

## Contexto

FTGO opera como un monolito Java/WAR con sintomas de infierno monolitico: builds lentos, escalado conflictivo, bajo aislamiento de fallos, lock-in tecnologico y equipos bloqueados por el tamano del codigo. La direccion decidio migrar a microservicios para sostener crecimiento, pero el monolito debe seguir vivo durante 18-24 meses. Origen: `Brief §A.1`, `Brief §A.4`, `Richardson Cap. 1`.

La decision debe respetar picos de 5x, latencia percibida menor a 200 ms p95, 99.9 % de disponibilidad en toma de pedidos, escalabilidad independiente, trazabilidad end-to-end y migracion incremental. Origen: `Brief §A.4`.

## Opciones consideradas

### Opcion 1: Mantener monolito

**Descripcion**: conservar la aplicacion Java/WAR como unidad principal y limitar cambios a modularizacion interna.

**Pros**

- Menor complejidad operativa inmediata.
- No requiere introducir broker, observabilidad distribuida ni despliegues por servicio.
- Reduce cambios organizacionales de corto plazo.

**Contras**

- Mantiene escalado conflictivo entre Order, Delivery, Kitchen y Billing.
- No resuelve aislamiento de fallos.
- Dificulta acelerar equipos por capacidad de negocio.
- No atiende la motivacion principal descrita por Richardson para salir del monolito.

**Impacto en NFRs**

- NFR-01 y NFR-06 quedan debiles porque el escalado sigue acoplado.
- NFR-03 puede sostenerse con infraestructura, pero con mayor costo.
- NFR-09 queda incumplido porque no hay migracion efectiva.

### Opcion 2: Big bang hacia microservicios

**Descripcion**: reemplazar el monolito completo por microservicios en un unico corte de release.

**Pros**

- Alinea rapidamente la arquitectura con capacidades de negocio.
- Permite ownership de datos y despliegue independiente desde el inicio.
- Elimina dependencias legacy si el corte es exitoso.

**Contras**

- Alto riesgo de interrupcion del negocio.
- Requiere reimplementar demasiados flujos criticos a la vez.
- Dificulta validar contratos, datos y operacion distribuida gradualmente.
- Contradice la restriccion explicita de migracion incremental.

**Impacto en NFRs**

- NFR-06 podria mejorar despues del corte, pero el riesgo sobre NFR-03 es alto.
- NFR-08 se vuelve obligatorio desde el primer dia y aumenta complejidad.
- NFR-09 se incumple porque reemplaza el monolito de golpe.

### Opcion 3: Migracion incremental con Strangler Fig

**Descripcion**: extraer capacidades gradualmente desde el monolito hacia servicios independientes, manteniendo convivencia controlada con el legacy.

**Pros**

- Cumple la restriccion de convivencia 18-24 meses.
- Permite priorizar capacidades de mayor valor o dolor operativo.
- Reduce riesgo al validar servicios, observabilidad y datos por etapas.
- Facilita descomponer por capacidades de negocio segun Richardson Cap. 2.

**Contras**

- Durante la transicion existen dos mundos: monolito y servicios.
- Requiere integracion temporal, routing, sincronizacion de datos y monitoreo cuidadoso.
- Puede crear deuda de adaptadores si no se eliminan al final de la migracion.

**Impacto en NFRs**

- NFR-01 y NFR-06 mejoran progresivamente al extraer componentes de alta carga.
- NFR-03 se protege al migrar flujos criticos por fases.
- NFR-08 se vuelve indispensable para diagnosticar llamadas entre monolito y servicios.
- NFR-09 se cumple directamente.

## Decision

FTGO adoptara una migracion incremental desde el monolito hacia microservicios usando Strangler Fig.

La arquitectura objetivo se organizara alrededor de capacidades de negocio, empezando por los flujos que mas necesitan escalabilidad, aislamiento o integracion externa: Order Taking, Kitchen, Delivery, Billing y Notifications. El monolito legacy seguira operando durante la transicion y sera rodeado por APIs, adaptadores y routing controlado hasta retirar responsabilidades extraidas. Origen: `Brief §A.1`, `Brief §A.3`, `Brief §A.4`, `Richardson Cap. 1`, `Richardson Cap. 2`.

## Consecuencias positivas

- Reduce riesgo frente a un reemplazo big bang.
- Permite escalar servicios de alta demanda de forma independiente.
- Hace visibles boundaries por capacidades de negocio.
- Facilita aprendizaje operativo gradual sobre microservicios, observabilidad y datos distribuidos.

## Consecuencias negativas

- Aumenta la complejidad temporal de integracion con el monolito.
- Exige disciplina para no dejar adaptadores legacy permanentes.
- Requiere correlation IDs, tracing y monitoreo desde etapas tempranas.
- Puede duplicar reglas o datos durante ventanas de migracion si no se gobierna bien.

## Follow-ups

- Definir el orden de extraccion de capacidades, iniciando por Order Taking o Notifications segun riesgo y valor.
- Crear contratos anticorrupcion entre servicios nuevos y monolito.
- Definir estrategia de datos e IPC en ADR 0002.
- Validar con POC de routing entre API Gateway, monolito y un primer servicio extraido.
