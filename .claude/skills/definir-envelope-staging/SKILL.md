---
name: definir-envelope-staging
description: Aplica el sobre común de mensajes de GANUS (documento 09), el envelope HTTP, las clases de error, las claves de idempotencia, el patrón Inbox/Outbox, los reintentos, la DLQ y el gobierno de versiones de contratos en STAGING. Usar al publicar o consumir un evento, al crear un contrato nuevo, al implementar STAGING o al enviar o recibir datos del móvil.
---

# Definir envelope y contratos de STAGING

## Propósito
STAGING es la frontera técnica de intercambio. **Transporta, valida el contrato, deduplica, reintenta, registra y
enruta.** No decide reglas de negocio ni es dueño de datos (09C §8, §18).

## Fuentes
- 09C §1 (principios), §3 (sobre común), §4 (API, envelope HTTP, errores), §7 (publicación y consumo), §8 (STAGING),
  §13 (idempotencia y concurrencia), §14 (seguridad), §15 (observabilidad), §16 (gobierno de contratos),
  §17 (registro de contratos), §20, §23 (decisiones técnicas pendientes).
- 10 §15 (tablas de STAGING), §29 (seguridad del intercambio).
- MC §5.

## Cuándo usarla
- Al definir un evento o contrato nuevo.
- Al publicar un evento desde un dominio.
- Al consumir un mensaje.
- Al implementar Inbox, Outbox, reintentos o DLQ.

## Sobre común (09C §3) — obligatorio en todo mensaje que cruce STAGING

| Campo | Oblig. | Descripción |
| --- | --- | --- |
| `messageId` | Sí | UUID del mensaje físico |
| `eventId` | Sí | Identificador del hecho lógico (puede ser igual a `messageId`) |
| `eventType` | Sí | Nombre estable con versión, por ejemplo `TaskPublished.v1` |
| `occurredAt` | Sí | Fecha y hora del hecho, UTC |
| `publishedAt` | Sí | Fecha y hora de publicación |
| `producer` | Sí | Dominio que produjo el mensaje (`forms`, `planning`, `mobile`…) |
| `producerVersion` | Sí | Versión de la aplicación productora |
| `schemaVersion` | Sí | Versión del contrato del payload |
| `correlationId` | Sí | Operación de punta a punta |
| `causationId` | No | Mensaje que causó este |
| `tenantId` | Sí | Organización aislada |
| `farmId` | Cond. | Finca, cuando aplica |
| `actorType` | Sí | `USER`, `SYSTEM`, `MOBILE`, `INTEGRATION`, `AI`… |
| `actorId` | Cond. | Identidad del actor |
| `source` | Sí | Origen funcional o técnico (`GANUS_WEB`, `MOBILE_OFFLINE`…) |
| `occurredOffline` | Sí | Si el hecho ocurrió sin conexión |
| `idempotencyKey` | Sí | Clave para no procesar dos veces |
| `payload` | Sí | Contenido del contrato |

El payload cambia según el contrato; el sobre no.

## Clave de idempotencia (09C §13.1)
Determinística respecto a la operación, nunca solo por timestamp:
`<dominio>:<entidad>:<operación>:<entityId>:<clientOperationId>`

Ejemplos para tu área:
| Operación | Clave |
| --- | --- |
| Publicar versión de formulario | `forms:form_version:publish:<formVersionId>` |
| Publicar tarea | `planning:task:publish:<taskId>:<taskVersion>` |
| Resultado de campo | `mobile:capture:submit:<captureId>` |
| Alerta | `mobile:alert:create:<alertId>` |

## Envelope HTTP (09C §4.1)
Cabeceras: `Authorization`, `X-Correlation-Id`, `X-Idempotency-Key` (obligatoria en comandos reintentables),
`X-Client-Version` cuando aplique. Respuesta: `{ "data": {...}, "meta": { "correlationId", "timestamp" } }`.

## Clases de error (09C §4.2)
| Código | Acción |
| --- | --- |
| VALIDATION_ERROR | No procesar; corregir datos |
| UNAUTHORIZED | Solicitar autenticación |
| FORBIDDEN | No reintentar |
| NOT_FOUND | Revisar dependencia o contexto |
| CONFLICT | Resolver; puede requerir releer |
| IDEMPOTENCY_REPLAY | Devolver el resultado original |
| TEMPORARY_FAILURE | Reintentar según política |
| DEPENDENCY_UNAVAILABLE | Enviar a reintento |
| INTERNAL_ERROR | Registrar y enviar a observabilidad o DLQ |

## Publicar (09C §7)
1. El dominio guarda su transacción **y** el registro de Outbox en la misma unidad transaccional.
2. Un proceso toma los Outbox pendientes y los entrega a STAGING.
3. Si la transacción hace rollback, no hay evento.
4. Los eventos son hechos ocurridos, no órdenes disfrazadas.

## Consumir (09C §7, §13)
1. Registrar el mensaje en Inbox.
2. Validar sobre y contrato (schema y versión).
3. Si `messageId` o `idempotencyKey` ya se aplicó → no repetir el efecto; devolver el resultado existente.
4. Validar tenant, finca y actor: el mensaje no otorga permisos (09C §14).
5. Llamar al caso de uso del dominio dueño. STAGING no aplica la regla.
6. Marcar el resultado del procesamiento y avanzar el checkpoint.
7. Tolerar mensajes fuera de orden usando versión o secuencia del agregado; un evento viejo no pisa un estado más nuevo.

## Estados de un mensaje (09C §8.1)
```
RECEIVED → VALIDATED → PROCESSING → PROCESSED
                         └→ RETRYING → PROCESSED
                         └→ REJECTED → DLQ
```
- RETRYING: fallas técnicas recuperables.
- REJECTED: errores de contrato o de negocio no recuperables.
- DLQ: conserva evidencia y permite reproceso controlado. Nunca se descarta en silencio.
- Diferencia con el MC §5.2 (PENDIENTE… CONFIRMADO): **DP-21**. Propuesta: usar los del 09C más el ACK al productor.

## Tablas (10 §15)
`staging_inbox_message`, `staging_outbox_message`, `staging_message_attempt`, `staging_dead_letter`,
`staging_checkpoint`, `staging_sync_package`, `staging_sync_item`.
Índices: `status + created_at`, `idempotency_key`, `correlation_id` (10 §27).

## Gobierno de contratos (09C §16–§17)
- Todo contrato tiene nombre, versión, dueño, productor, consumidores, propósito y estado (DRAFT, ACTIVE, DEPRECATED, RETIRED).
- Un cambio incompatible crea una versión nueva (`.v2`); no se rompe a los consumidores.
- Cada contrato tiene un ejemplo de payload probado y pruebas de productor y consumidor.

Contratos de tu área: CT-FRM-01 FormVersionReference.v1, CT-PLN-01 WorkPackage.v1, CT-PLN-02 TaskStateChanged.v1,
CT-MOB-01 TaskExecutionResult.v1, CT-MOB-02 AlertCreated.v1.

## Seguridad y observabilidad (09C §14–§15)
- Sin secretos, tokens ni credenciales en los mensajes. Archivos por ID o URL temporal.
- Métricas: `messages_received_total`, `messages_processed_total`, `messages_failed_total`, `messages_retried_total`,
  `messages_dlq_total`, `processing_latency`, `sync_pending`, `sync_conflicts`, `outbox_pending`, `inbox_duplicates`.
- Todo se puede buscar por `correlationId`, `messageId`, `eventId`, `tenantId`, `farmId` y entidad.

## Prohibido
- Lógica de negocio en STAGING (09C §1).
- Usar STAGING como fuente maestra.
- Corregir en silencio un evento incompatible (09C §7).
- Ocultar errores de sincronización o integración (CONST §3).

## Decisiones técnicas pendientes (09C §23, resolver con ADR)
Broker interno, formato de schema (JSON Schema o validación de DTO), tablas físicas finales y retención, política de
reintentos (backoff, máximo de intentos), política de conflictos, almacenamiento de objetos. Y DP-21.

## Verificación (09C §19–§20)
- CI-002: un mensaje procesado dos veces produce un solo efecto.
- CI-003: el `correlationId` llega hasta el consumidor.
- CI-004 y CI-005: falla recuperable → reintento; no procesable → DLQ con causa.
- CI-013: una operación se reconstruye por `messageId` o `correlationId`.
- Transacción con rollback → sin evento.
