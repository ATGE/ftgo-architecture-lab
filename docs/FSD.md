# FSD - FTGO Architecture Lab

## 1. Introduccion

Este FSD define los casos de uso funcionales iniciales de FTGO para guiar la migracion desde monolito hacia una arquitectura objetivo basada en capacidades. Los UCs formalizan las user stories semilla y agregan escenarios derivados de las restricciones de pago, tracking y entrega descritas en el brief. Origen: `Brief §A.1`, `Brief §A.5`, `Richardson Cap. 2`.

## 2. Tabla de casos de uso

| ID | Titulo | Actor primario | Capacidad PRD | Origen |
|---|---|---|---|---|
| UC-01 | Tomar pedido | Consumidor | CAP-03 Order Taking | `US-01`, `Brief §A.5` |
| UC-02 | Aceptar o rechazar ticket de cocina | Restaurante | CAP-04 Order Fulfillment / Kitchen | `US-02`, `Brief §A.5` |
| UC-03 | Asignar entrega a courier | Courier | CAP-05 Delivery | `US-03`, `Brief §A.5` |
| UC-04 | Procesar pago del pedido | Consumidor | CAP-06 Billing & Accounting | `Brief §A.3`, `Brief §A.4`, `US-01` |
| UC-05 | Tracking en tiempo real del pedido | Consumidor | CAP-05 Delivery | `Brief §A.2`, `Brief §A.4`, `US-03` |
| UC-06 | Reasignar courier por rechazo o timeout | Courier | CAP-05 Delivery | `US-03`, `Brief §A.5` |

## 3. Detalle de casos de uso

### UC-01: Tomar pedido

| Campo | Valor |
|---|---|
| Actor primario | Consumidor |
| Capacidad PRD | CAP-03 Order Taking |
| Origen | `US-01`, `Brief §A.5`, `Brief §A.3` |

**Precondiciones**

- El consumidor tiene una sesion valida o un perfil identificable.
- El restaurante esta registrado y disponible.
- El menu consultado pertenece al restaurante seleccionado.

**Flujo principal**

1. El consumidor selecciona un restaurante.
2. El sistema muestra menu, precios y disponibilidad.
3. El consumidor agrega o quita items del carrito.
4. El consumidor confirma direccion de entrega y metodo de pago.
5. El sistema valida disponibilidad del restaurante y de los items.
6. El sistema calcula total y crea un pedido con numero unico.
7. El sistema confirma la recepcion del pedido al consumidor.

**Flujos alternativos**

- A1: Si el restaurante no esta disponible, el sistema bloquea la confirmacion y muestra estado actualizado.
- A2: Si un item ya no esta disponible, el sistema solicita ajustar el carrito.
- A3: Si el pago queda pendiente por falla externa, el pedido se mantiene en estado pendiente de cobro segun UC-04.

**Postcondiciones**

- Pedido creado con identificador unico.
- Estado inicial disponible para Kitchen, Billing, Delivery y Notifications.

**Given/When/Then**

- Given: el consumidor tiene un carrito con al menos un item disponible y una direccion de entrega valida.
- When: confirma el pedido.
- Then: FTGO crea el pedido, asigna numero unico y muestra confirmacion.

### UC-02: Aceptar o rechazar ticket de cocina

| Campo | Valor |
|---|---|
| Actor primario | Restaurante |
| Capacidad PRD | CAP-04 Order Fulfillment / Kitchen |
| Origen | `US-02`, `Brief §A.5`, `Brief §A.3` |

**Precondiciones**

- Existe un pedido confirmado para el restaurante.
- El dashboard del restaurante esta disponible.
- El restaurante puede evaluar carga de cocina.

**Flujo principal**

1. El sistema entrega el ticket al dashboard del restaurante.
2. El restaurante revisa items, cantidades y condiciones del pedido.
3. El restaurante acepta el ticket e informa tiempo estimado de preparacion.
4. El sistema actualiza el estado del pedido.
5. El sistema notifica al consumidor.

**Flujos alternativos**

- A1: Si el restaurante rechaza el ticket, debe informar motivo.
- A2: Si el ticket es rechazado, el pedido se cancela y se notifica al consumidor.
- A3: Si el dashboard no responde, el sistema conserva el estado y permite retry operativo.

**Postcondiciones**

- Pedido aceptado con ETA de cocina o cancelado con motivo.
- Consumidor informado del cambio de estado.

**Given/When/Then**

- Given: el restaurante recibe un ticket de pedido entrante.
- When: acepta el ticket con tiempo estimado de preparacion.
- Then: FTGO marca el pedido como aceptado por cocina y notifica al consumidor.

### UC-03: Asignar entrega a courier

| Campo | Valor |
|---|---|
| Actor primario | Courier |
| Capacidad PRD | CAP-05 Delivery |
| Origen | `US-03`, `Brief §A.5`, `Brief §A.3` |

**Precondiciones**

- El courier marco disponibilidad.
- Existe un pedido listo o proximamente listo para retiro.
- El sistema conoce ubicacion aproximada del courier y del restaurante.

**Flujo principal**

1. El sistema identifica couriers disponibles cerca del restaurante.
2. El sistema ofrece una asignacion al courier candidato.
3. El courier acepta dentro del timeout definido.
4. El sistema confirma la asignacion.
5. El sistema muestra ruta optimizada hacia restaurante y consumidor.

**Flujos alternativos**

- A1: Si el courier rechaza, se ejecuta UC-06.
- A2: Si el courier no responde dentro de 30 segundos, se ejecuta UC-06.
- A3: Si Google Maps degrada, el sistema permite continuar con informacion basica de direccion.

**Postcondiciones**

- Pedido asignado a un courier.
- Courier tiene ruta o instrucciones minimas para retiro y entrega.

**Given/When/Then**

- Given: hay un pedido listo para retiro y un courier disponible cerca.
- When: el courier acepta la asignacion dentro del timeout.
- Then: FTGO asigna el pedido al courier y muestra la ruta.

### UC-04: Procesar pago del pedido

| Campo | Valor |
|---|---|
| Actor primario | Consumidor |
| Capacidad PRD | CAP-06 Billing & Accounting |
| Origen | `Brief §A.3`, `Brief §A.4`, `US-01` |

**Precondiciones**

- Existe un pedido creado por UC-01.
- El consumidor selecciono un metodo de pago.
- FTGO delega datos sensibles de tarjeta a Stripe.

**Flujo principal**

1. El sistema calcula monto final del pedido.
2. El sistema solicita autorizacion de pago a Stripe.
3. Stripe confirma autorizacion.
4. El sistema marca el pago como autorizado.
5. El sistema habilita continuidad del pedido hacia cocina.

**Flujos alternativos**

- A1: Si Stripe esta caido, el sistema registra intento pendiente y envia el cobro a cola de retry.
- A2: Si Stripe rechaza el pago, el sistema informa al consumidor y solicita otro metodo.
- A3: Si el pedido fue cancelado por restaurante, el sistema evita capturar el pago o inicia compensacion.

**Postcondiciones**

- Pago autorizado o registrado como pendiente de retry.
- El pedido conserva trazabilidad del estado de cobro sin almacenar datos PCI sensibles.

**Given/When/Then**

- Given: el consumidor confirma un pedido con metodo de pago valido.
- When: FTGO solicita autorizacion a Stripe.
- Then: FTGO registra el resultado del pago y continua o deja el pedido pendiente de retry si Stripe no esta disponible.

### UC-05: Tracking en tiempo real del pedido

| Campo | Valor |
|---|---|
| Actor primario | Consumidor |
| Capacidad PRD | CAP-05 Delivery |
| Origen | `Brief §A.2`, `Brief §A.4`, `US-03` |

**Precondiciones**

- El pedido fue asignado a un courier.
- El consumidor tiene acceso al estado del pedido.
- El sistema recibe actualizaciones de ubicacion o estado de entrega.

**Flujo principal**

1. El consumidor abre el detalle del pedido.
2. El sistema muestra estado actual de preparacion o entrega.
3. El sistema actualiza ubicacion del courier cuando esta disponible.
4. El sistema muestra ETA derivada de la ruta.
5. El sistema notifica cambios relevantes del pedido.

**Flujos alternativos**

- A1: Si Google Maps se degrada, el sistema muestra estado textual y ETA aproximada.
- A2: Si no hay posicion reciente del courier, el sistema muestra la ultima actualizacion conocida.
- A3: Si tracking se degrada, no debe bloquear la toma de pedidos.

**Postcondiciones**

- Consumidor tiene visibilidad del estado del pedido.
- Eventos de tracking quedan trazables para soporte operativo.

**Given/When/Then**

- Given: el consumidor tiene un pedido asignado a courier.
- When: consulta el tracking del pedido.
- Then: FTGO muestra estado, ubicacion o ultima actualizacion disponible y ETA cuando sea posible.

### UC-06: Reasignar courier por rechazo o timeout

| Campo | Valor |
|---|---|
| Actor primario | Courier |
| Capacidad PRD | CAP-05 Delivery |
| Origen | `US-03`, `Brief §A.5`, `Brief §A.4` |

**Precondiciones**

- Existe un pedido que requiere courier.
- Un courier candidato rechazo la asignacion o no respondio dentro del timeout.
- Hay otros couriers disponibles o el pedido puede quedar en busqueda.

**Flujo principal**

1. El sistema detecta rechazo o timeout de asignacion.
2. El sistema libera al courier anterior de la oferta.
3. El sistema busca el siguiente courier disponible cercano.
4. El sistema envia una nueva oferta de asignacion.
5. El sistema registra el intento para trazabilidad operativa.

**Flujos alternativos**

- A1: Si no hay courier disponible, el pedido queda en estado pendiente de asignacion.
- A2: Si varios couriers rechazan, el sistema continua intentando segun politica operativa.
- A3: Si la asignacion se retrasa, el sistema notifica degradacion o nuevo ETA al consumidor.

**Postcondiciones**

- El pedido queda asignado a otro courier o pendiente de asignacion.
- Los intentos quedan registrados con correlation ID.

**Given/When/Then**

- Given: un courier no acepta una oferta de entrega dentro de 30 segundos.
- When: expira el timeout de asignacion.
- Then: FTGO busca otro courier cercano y registra el intento fallido.
