# ADR 0002: Estrategia de comunicacion y datos

**Status**: Accepted

## Contexto

FTGO necesita coordinar acciones inmediatas del usuario, procesos distribuidos de cocina/entrega/pago/notificaciones e integracion con sistemas externos como Stripe, Google Maps y SendGrid/Twilio. La arquitectura debe convivir con el monolito durante la migracion Strangler Fig. Origen: `Brief §A.1`, `Brief §A.2`, `Brief §A.4`, ADR 0001.

La decision debe preservar latencia percibida menor a 200 ms p95 en acciones del consumidor, tolerancia ante fallos externos, consistencia fuerte dentro del aggregate de pedido, consistencia eventual para reporting y trazabilidad end-to-end. Origen: `Brief §A.4`.

## Opciones consideradas

### Opcion 1: Solo REST/HTTPS sincrono

**Descripcion**: todos los servicios se comunican por APIs REST sincronas y cada request coordina las dependencias necesarias en linea.

**Pros**

- Modelo simple para acciones request/response.
- Facil de depurar en etapas iniciales.
- Encaja con API Gateway y dashboards web/moviles.

**Contras**

- Aumenta acoplamiento temporal entre servicios.
- Una falla de Stripe, mapas o notificaciones puede degradar cadenas completas.
- Puede producir latencia acumulada en flujos como checkout y asignacion.
- No modela bien eventos de estado de pedido, cocina, delivery y notificaciones.

**Impacto en NFRs**

- NFR-02 puede degradarse por cadenas sincronas.
- NFR-05 queda debil porque las fallas externas impactan el flujo.
- NFR-07 se simplifica localmente pero complica procesos distribuidos.

### Opcion 2: Solo eventos asincronos

**Descripcion**: todos los cambios y comandos entre componentes se publican como eventos, minimizando llamadas sincronas.

**Pros**

- Alto desacoplamiento temporal.
- Mejora resiliencia ante picos y fallos temporales.
- Encaja con notificaciones, tracking, reporting y retry de pagos.

**Contras**

- Mala experiencia para acciones que requieren respuesta inmediata.
- Mayor complejidad de correlacion, idempotencia y manejo de consistencia eventual.
- Dificulta validaciones interactivas del consumidor, como confirmar pedido o consultar menu.
- Puede ser excesivo para dashboards y operaciones CRUD simples.

**Impacto en NFRs**

- NFR-05 mejora para fallos externos mediante colas y retry.
- NFR-02 puede sufrir si el usuario espera confirmaciones inmediatas.
- NFR-08 se vuelve critico para seguir procesos asincronos.

### Opcion 3: Modelo hibrido REST/HTTPS + eventos

**Descripcion**: usar REST/HTTPS para acciones inmediatas y consultas interactivas, y eventos asincronos para procesos distribuidos, integraciones tolerantes a fallos y propagacion de cambios de estado.

**Pros**

- Ajusta el mecanismo al tipo de interaccion.
- Mantiene UX directa para checkout, dashboard y consultas.
- Desacopla procesos de cocina, delivery, billing, notifications y tracking.
- Permite colas de retry para fallos externos sin bloquear toda la operacion.
- Compatible con ownership de datos por servicio y consistencia eventual entre boundaries.

**Contras**

- Requiere gobernar dos estilos de comunicacion.
- Exige idempotencia, versionado de eventos y observabilidad distribuida.
- Puede haber duplicidad temporal de datos durante la migracion Strangler Fig.

**Impacto en NFRs**

- NFR-02 se protege usando REST en acciones inmediatas.
- NFR-05 mejora con eventos, colas y retries.
- NFR-07 se alinea con consistencia fuerte dentro del aggregate y eventual entre servicios.
- NFR-08 exige correlation ID en HTTP y eventos.
- NFR-09 se favorece al integrar monolito y servicios gradualmente.

## Decision

FTGO usara un modelo hibrido:

- REST/HTTPS sincrono para acciones inmediatas de usuarios y dashboards: tomar pedido, consultar menu, aceptar/rechazar ticket, consultar tracking y operar back office.
- Eventos asincronos para procesos distribuidos: `OrderCreated`, `PaymentAuthorized`, `KitchenTicketAccepted`, `CourierAssigned`, `CourierAssignmentTimedOut`, `OrderStatusChanged` y eventos de notificacion.
- Ownership de datos por servicio donde aplique, evitando base de datos compartida entre servicios nuevos. Cada servicio conserva consistencia fuerte dentro de su aggregate; las proyecciones interservicio y reporting aceptan consistencia eventual.
- Integracion temporal con el monolito legacy mediante APIs/adaptadores durante Strangler Fig.

Origen: `Brief §A.3`, `Brief §A.4`, `Richardson Cap. 2`, ADR 0001.

## Consecuencias positivas

- Balancea UX inmediata con resiliencia distribuida.
- Reduce dependencia directa de sistemas externos en flujos criticos.
- Habilita escalabilidad independiente por servicio y por cola.
- Hace explicita la frontera de datos de cada capacidad extraida.

## Consecuencias negativas

- Aumenta complejidad de diseno, pruebas y operacion.
- Requiere convenciones de eventos, correlation IDs e idempotencia.
- Obliga a gestionar consistencia eventual y compensaciones en casos de pago/cancelacion.
- Durante la migracion puede haber integraciones temporales con el monolito dificiles de retirar.

## Follow-ups

- Definir convenciones de nombres, versionado y compatibilidad de eventos.
- Disenar outbox/inbox o mecanismo equivalente para publicacion confiable.
- Documentar estrategia de idempotencia para Billing, Delivery y Notifications.
- Validar con POC de `OrderCreated` desde Order Service hacia Kitchen, Billing y Notifications.
