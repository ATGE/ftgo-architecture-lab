# PRD - FTGO Architecture Lab

## 1. Contexto y objetivos

FTGO es una plataforma de delivery que conecta consumidores, restaurantes y couriers. El negocio opera sobre una aplicacion monolitica Java empaquetada como WAR que ya muestra sintomas de infierno monolitico: builds lentos, escalado conflictivo, falta de aislamiento de fallos, lock-in tecnologico y equipos bloqueados por el tamano del codigo. Origen: `Brief §A.1`, `Richardson Cap. 1`.

El objetivo de producto de esta etapa no es reconstruir FTGO desde cero, sino documentar una arquitectura objetivo que permita migrar gradualmente hacia microservicios mediante Strangler Fig durante 18-24 meses. La documentacion debe servir como base para FSD, ADRs y diagramas C4, manteniendo trazabilidad explicita. Origen: `Brief §A.1`, `Brief §A.4`, `Richardson Cap. 2`.

## 2. Stakeholders

| Stakeholder | Necesidad principal | Origen |
|---|---|---|
| Consumidor | Ordenar comida con UX rapida, estado transparente y tracking en tiempo real. | `Brief §A.2`, `US-01` |
| Restaurante | Gestionar tickets, carga de cocina y dashboard de pedidos. | `Brief §A.2`, `US-02` |
| Courier | Recibir asignaciones cercanas, rutas optimizadas y pago confiable. | `Brief §A.2`, `US-03` |
| Empleado FTGO | Tener visibilidad operativa, reportes y soporte para incidentes. | `Brief §A.2` |
| Equipo de arquitectura | Guiar la migracion con calidad, trazabilidad y mantenibilidad. | `Brief §A.2` |
| Sistemas externos | Integrarse con SLAs predecibles para pagos, mapas y notificaciones. | `Brief §A.2` |

## 3. Capacidades de negocio

| ID | Capacidad | Responsabilidad | Origen |
|---|---|---|---|
| CAP-01 | Consumer Management | Gestiona registro, perfiles, direcciones y preferencias de consumidores. | `Brief §A.3`, `Richardson Cap. 2` |
| CAP-02 | Restaurant Management | Administra restaurantes, menus, horarios y disponibilidad. | `Brief §A.3`, `Richardson Cap. 2` |
| CAP-03 | Order Taking | Valida menus, calcula total, confirma pedidos y asigna numero unico. | `Brief §A.3`, `US-01`, `Richardson Cap. 2` |
| CAP-04 | Order Fulfillment / Kitchen | Entrega tickets al restaurante y gestiona estados de preparacion. | `Brief §A.3`, `US-02`, `Richardson Cap. 2` |
| CAP-05 | Delivery | Asigna couriers, calcula rutas y habilita tracking en tiempo real. | `Brief §A.3`, `US-03`, `Richardson Cap. 2` |
| CAP-06 | Billing & Accounting | Procesa cobros, comisiones y pagos a restaurantes/couriers. | `Brief §A.3`, `Brief §A.4` |
| CAP-07 | Notifications | Envia confirmaciones, alertas, recibos y actualizaciones por email/SMS/push. | `Brief §A.3`, `Brief §A.2` |

Estas capacidades son candidatos de descomposicion, no una obligacion de crear un microservicio por capacidad desde el dia uno. La decision se justifica en los ADRs. Origen: `Brief §A.3`, `Richardson Cap. 2`.

## 4. Requisitos no funcionales

| ID | NFR | Metrica | Origen | Justificacion |
|---|---|---|---|---|
| NFR-01 | Elasticidad en pico | Soportar 5x de trafico durante 12:00-14:00 y 19:00-22:00 hora local. | `Brief §A.4` | FTGO concentra demanda en almuerzo y cena; la arquitectura debe escalar los componentes de mayor carga. |
| NFR-02 | Latencia de UX | Respuesta percibida menor a 200 ms p95 para acciones del consumidor. | `Brief §A.4`, `US-01` | La toma de pedidos depende de una experiencia movil rapida para no perder conversion. |
| NFR-03 | Disponibilidad de pedidos | 99.9 % mensual minimo para el flujo de toma de pedidos. | `Brief §A.4`, `US-01` | Order Taking es el flujo de ingreso principal del negocio. |
| NFR-04 | Disponibilidad de tracking | 99.5 % mensual minimo para tracking en tiempo real, con degradacion aceptada. | `Brief §A.4`, `US-03` | Tracking mejora transparencia, pero puede degradarse sin bloquear la toma de pedidos. |
| NFR-05 | Tolerancia a fallos externos | Tomar pedidos aunque Stripe este caido mediante cola de retry; aceptar degradacion temporal de mapas. | `Brief §A.4` | Las integraciones externas no deben detener completamente el flujo comercial. |
| NFR-06 | Escalabilidad independiente | Componentes principales escalables por X-axis y Y-axis del Scale Cube. | `Brief §A.4`, `Richardson Cap. 1` | Evita el escalado conflictivo del monolito y permite dimensionar Order, Delivery o Kitchen por demanda. |
| NFR-07 | Consistencia de datos | Consistencia fuerte dentro del aggregate de pedido; consistencia eventual aceptada para reporting interservicios. | `Brief §A.4` | Un pedido requiere reglas atomicas locales, pero reporting y proyecciones pueden ser asincronas. |
| NFR-08 | Trazabilidad end-to-end | Cada accion de consumidor debe propagar correlation ID y aparecer en distributed tracing. | `Brief §A.4` | La migracion distribuida aumenta la necesidad de diagnostico transversal. |
| NFR-09 | Migracion incremental | Mantener convivencia con monolito durante 18-24 meses usando Strangler Fig. | `Brief §A.4`, `Brief §A.1` | Reduce riesgo de reemplazo big bang y permite extraer capacidades por prioridad. |
| NFR-10 | Cumplimiento de pagos y datos | Delegar PCI-DSS a Stripe; proteger datos de consumidor segun GDPR/normas locales. | `Brief §A.4` | El dominio maneja pagos y datos personales, por lo que debe minimizar exposicion directa. |

## 5. Alcance

### Entra en alcance

- Documentar arquitectura objetivo de FTGO para migracion gradual desde monolito.
- Cubrir capacidades Consumer, Restaurant, Order, Kitchen, Delivery, Billing y Notifications.
- Formalizar casos de uso principales derivados de `US-01`, `US-02`, `US-03` y NFRs.
- Definir decisiones iniciales de estilo arquitectonico, comunicacion y datos.
- Modelar contexto y contenedores C4 con sistemas externos: Stripe, Google Maps y SendGrid/Twilio.

### Fuera de alcance

- Construir o desplegar software ejecutable.
- Copiar el examen completo dentro del repo como anexo.
- Definir contratos API exhaustivos, esquemas de base de datos completos o backlog detallado.
- Reemplazar el monolito en un unico corte big bang.
- Agregar stakeholders, capacidades o NFRs no trazables al brief o Richardson.
