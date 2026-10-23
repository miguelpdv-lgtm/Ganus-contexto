# 09C · Contratos e Integraciones MVP v1.0

> **Fuente de solo lectura.** Convertido a Markdown desde `09_CONTRATOS_INTEGRACIONES_GANUS_OPERATIVO_MVP_v1.0.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**GANUS**

**09 · CONTRATOS + INTEGRACIONES**

MVP OPERATIVO · STAGING · APIs · EVENTOS · SINCRONIZACIÓN

Versión 1.0 · Documento técnico-funcional derivado de la arquitectura oficial y de los documentos funcionales 00–08 y 10.

Propósito: establecer cómo los dominios de GANUS intercambian información sin romper sus fronteras, duplicar maestros ni introducir lógica de negocio en la integración.

# 0. Propósito y posición del documento

Este documento define la frontera de intercambio de información de GANUS Operativo MVP. No crea nuevos módulos funcionales. Traduce las decisiones funcionales ya cerradas a contratos de aplicación, eventos, mensajes de STAGING, sincronización, trazabilidad, errores y criterios de integración.

La arquitectura oficial establece una solución modular monolítica con arquitectura hexagonal/clean, API-first y event-driven integration. STAGING es la frontera de integración e intercambio; los dominios no acceden directamente a repositorios o tablas de otros dominios.

Este documento debe ser utilizado junto con: 00 CEREBRO GANUS MVP, 01 ARQUITECTURA GANUS, 02 ORGANIZACIÓN, 03 INVENTARIO + CICLO DE VIDA, 04 FORMULARIOS, 05 PLANIFICACIÓN, 06 MOBILE + OFFLINE, 07 SEGUIMIENTO/CENTRO DE CONTROL/KNOWLEDGE/ADVISORY, 08 SERVICIOS TRANSVERSALES y 10 MODELO BD REFERENCIA MVP.

Regla de precedencia: si un detalle técnico parece contradecir una decisión funcional de los documentos anteriores, se conserva el comportamiento funcional y el detalle se resuelve como decisión técnica/ADR; no se cambia el producto desde el contrato.

## 0.1 Resultado esperado

- Cada intercambio entre dominios tiene un consumidor, productor, propósito y contrato identificable.

- Los datos transaccionales permanecen en el dominio que los posee.

- Los cambios relevantes pueden publicarse como eventos sin que el consumidor acceda a tablas internas.

- Los mensajes pueden repetirse sin producir duplicados funcionales.

- Una falla de integración no destruye la transacción origen ni obliga al usuario a repetirla sin control.

- Mobile puede operar offline y sincronizar mediante el mecanismo definido, manteniendo trazabilidad.

- Todo mensaje relevante permite reconstruir qué ocurrió, cuándo, en qué finca, sobre qué entidad y cuál fue su origen.

# 1. Principios no negociables

| **Principio**                   | **Regla de implementación**                                                                                                |
|---------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| Ownership                       | Cada dominio es dueño de sus entidades y reglas. Un consumidor recibe una representación autorizada, no la tabla origen.   |
| API-first                       | Los contratos de aplicación se definen antes de implementar consumidores.                                                  |
| Event-driven integration        | Los cambios relevantes se publican mediante eventos cuando exista necesidad de desacoplamiento, sincronización o reacción. |
| STAGING como frontera           | STAGING concentra intercambio, Inbox/Outbox, idempotencia, reintentos, checkpoints y dead-letter.                          |
| No acceso directo               | Ningún dominio consulta tablas/repositorios internos de otro dominio.                                                      |
| Idempotencia                    | Todo mensaje integrable debe poder procesarse más de una vez sin duplicar el resultado funcional.                          |
| Trazabilidad                    | Todo intercambio conserva correlationId, causationId, eventId/messageId, actor/origen y contexto.                          |
| Versionamiento                  | Los contratos y eventos son versionados; una modificación incompatible requiere nueva versión.                             |
| Seguridad por contexto          | La finca y el ámbito autorizado acompañan el intercambio cuando son relevantes.                                            |
| Offline-first                   | La sincronización es una capacidad de intercambio, no una excepción improvisada del móvil.                                 |
| No lógica de negocio en STAGING | STAGING transporta, valida contrato técnico, deduplica, reintenta y enruta; no decide reglas del negocio.                  |
| No duplicación de maestros      | Las proyecciones o cachés son copias derivadas y nunca sustituyen al dueño.                                                |

# 2. Modelo de integración GANUS

> DOMINIOS  
> Organización \| Inventario \| Formularios \| Planificación \| Mobile \| Seguimiento/Control  
> Knowledge \| Advisory \| Servicios Transversales  
> │  
> ▼  
> INTERFACES DE APLICACIÓN  
> │  
> ▼  
> STAGING  
> ┌──────────┬──────────┬──────────┐  
> INBOX OUTBOX CHECKPOINTS DLQ  
> │ │ │ │  
> └────────────┴──────────┴──────────┘  
> │  
> ▼  
> CONSUMIDORES / ADAPTADORES  
> │  
> ODOO / APIs externas / IoT  
> (cuando corresponda)

En el MVP, el modular monolith comparte PostgreSQL, pero el compartir infraestructura no elimina la frontera de dominio. La frontera es funcional y de código: interfaces de aplicación, DTOs/contratos y eventos; nunca acceso directo al repositorio de otro dominio.

## 2.1 Tipos de intercambio

| **Tipo**                    | **Uso**                                        | **Ejemplo**                                                               |
|-----------------------------|------------------------------------------------|---------------------------------------------------------------------------|
| Consulta síncrona           | Necesidad inmediata de información actual.     | Planificación consulta catálogo de espacios publicado por Organización.   |
| Comando síncrono            | Solicitar una operación al dominio dueño.      | Un proceso autorizado solicita crear/modificar una entidad en su dominio. |
| Evento asíncrono            | Informar que un hecho ocurrió.                 | AssetCreated, TaskCompleted, FarmUpdated.                                 |
| Mensaje de sincronización   | Mover cambios entre Mobile/STAGING y núcleo.   | CaptureUploaded, TaskSyncRequested.                                       |
| Proyección/lectura derivada | Optimizar consulta sin convertirse en maestro. | Resumen autorizado de activo para seguimiento.                            |

# 3. Contrato común de mensajes

Todos los eventos y mensajes que atraviesen la frontera STAGING deben compartir un sobre técnico común. El payload de negocio cambia por tipo de mensaje; el sobre no.

| **Campo**       | **Oblig.** | **Descripción**                                                                                  |
|-----------------|------------|--------------------------------------------------------------------------------------------------|
| messageId       | Sí         | Identificador único del mensaje físico. UUID.                                                    |
| eventId         | Sí         | Identificador del hecho/evento lógico. Puede coincidir con messageId cuando no exista reemisión. |
| eventType       | Sí         | Nombre estable del contrato, por ejemplo AssetCreated.v1.                                        |
| occurredAt      | Sí         | Fecha/hora del hecho en UTC.                                                                     |
| publishedAt     | Sí         | Fecha/hora de publicación.                                                                       |
| producer        | Sí         | Dominio/servicio que produjo el mensaje.                                                         |
| producerVersion | Sí         | Versión de aplicación que lo produjo.                                                            |
| schemaVersion   | Sí         | Versión del contrato del payload.                                                                |
| correlationId   | Sí         | Identifica la operación de extremo a extremo.                                                    |
| causationId     | No         | Mensaje/evento que causó el actual, cuando exista.                                               |
| tenantId        | Sí         | Identificador del cliente/organización aislada.                                                  |
| farmId          | Cond.      | Finca a la que pertenece el hecho cuando aplica.                                                 |
| actorType       | Sí         | USER, SYSTEM, MOBILE, INTEGRATION, AI, etc.                                                      |
| actorId         | Cond.      | Identidad del actor cuando exista.                                                               |
| source          | Sí         | Origen funcional/técnico del dato.                                                               |
| occurredOffline | Sí         | Indica si fue generado offline.                                                                  |
| idempotencyKey  | Sí         | Clave para evitar procesamiento duplicado.                                                       |
| payload         | Sí         | Contenido específico del contrato.                                                               |

## 3.1 Ejemplo conceptual de evento

> {  
> "messageId": "uuid",  
> "eventId": "uuid",  
> "eventType": "AssetCreated.v1",  
> "occurredAt": "2026-10-08T15:30:00Z",  
> "publishedAt": "2026-10-08T15:30:03Z",  
> "producer": "inventory",  
> "producerVersion": "1.0.0",  
> "schemaVersion": "1.0",  
> "correlationId": "uuid",  
> "causationId": null,  
> "tenantId": "org_123",  
> "farmId": "farm_456",  
> "actorType": "USER",  
> "actorId": "user_789",  
> "source": "GANUS_WEB",  
> "occurredOffline": false,  
> "idempotencyKey": "inventory:asset:create:uuid",  
> "payload": { "...": "contrato específico" }  
> }

# 4. Contratos API

Las APIs internas deben expresar casos de uso, no tablas. Los endpoints definitivos pueden organizarse por recurso/capacidad, pero no deben exponer directamente entidades internas ni permitir operaciones que contradigan el ownership funcional.

| **Regla**              | **Criterio**                                                                                  |
|------------------------|-----------------------------------------------------------------------------------------------|
| Identidad              | IDs estables; nunca usar nombre como identificador técnico.                                   |
| Contexto               | Toda operación debe validar tenant/organización y finca cuando corresponda.                   |
| Autorización           | Se valida en la capa de aplicación; no se confía en que el consumidor filtre.                 |
| Validación             | Errores de contrato se detectan antes de ejecutar el caso de uso.                             |
| Respuesta              | Debe distinguir éxito, rechazo de negocio, error de contrato y error técnico.                 |
| Paginación             | Obligatoria en consultas potencialmente masivas.                                              |
| Optimistic concurrency | Usar version/revision o equivalente cuando una entidad pueda ser modificada concurrentemente. |
| Trace                  | Toda respuesta debe poder relacionarse con correlationId/traceId.                             |
| No SQL compartido      | No entregar SQL, nombres de tablas o joins internos como contrato.                            |

## 4.1 Envelope HTTP recomendado

> Request headers mínimos:  
> Authorization  
> X-Correlation-Id  
> X-Idempotency-Key (obligatorio en comandos no seguros/reintentables)  
> X-Client-Version (cuando aplique)  
>   
> Respuesta:  
> {  
> "data": {...},  
> "meta": {  
> "correlationId": "uuid",  
> "timestamp": "UTC"  
> }  
> }

## 4.2 Clases de error

| **Código lógico**      | **Significado**                                 | **Acción**                                          |
|------------------------|-------------------------------------------------|-----------------------------------------------------|
| VALIDATION_ERROR       | Payload inválido.                               | No procesar; corregir datos.                        |
| UNAUTHORIZED           | No autenticado.                                 | Solicitar autenticación.                            |
| FORBIDDEN              | No autorizado para el ámbito.                   | No reintentar automáticamente.                      |
| NOT_FOUND              | Referencia inexistente.                         | Revisar dependencia/contexto.                       |
| CONFLICT               | Estado/versión incompatible o duplicado lógico. | Resolver conflicto; puede requerir relectura.       |
| IDEMPOTENCY_REPLAY     | Solicitud repetida ya procesada.                | Devolver resultado original cuando sea seguro.      |
| TEMPORARY_FAILURE      | Falla técnica recuperable.                      | Reintentar según política.                          |
| DEPENDENCY_UNAVAILABLE | Dependencia no disponible.                      | Enviar a retry/STAGING.                             |
| INTERNAL_ERROR         | Error no clasificado.                           | Registrar y enviar a observabilidad/DLQ según caso. |

# 5. Matriz de contratos entre dominios

| **Origen**              | **Consumidor**        | **Contrato / información**                                                               | **Modo**               | **Ownership**                                          |
|-------------------------|-----------------------|------------------------------------------------------------------------------------------|------------------------|--------------------------------------------------------|
| Organización            | Inventario            | FarmContext, SpaceReference, clasificación empresarial cuando sea contexto autorizado    | API + eventos          | Organización                                           |
| Organización            | Formularios           | ProcessReference, ActivityTypeReference, SpaceReference                                  | API + eventos          | Organización                                           |
| Organización            | Planificación         | FarmContext, SpaceReference, Process/ActivityTypeReference                               | API + eventos          | Organización                                           |
| Organización            | Knowledge             | OrganizationContext: industria, sector, negocio, finca, estructura y contexto autorizado | API/eventos            | Organización                                           |
| Inventario              | Formularios           | AssetReference/AssetLookup autorizado                                                    | API                    | Inventario                                             |
| Inventario              | Planificación         | AssetReference, estado, ubicación/contexto, ciclo y datos autorizados                    | API + eventos          | Inventario                                             |
| Inventario              | Mobile                | AssetSnapshot incluido en paquete de trabajo                                             | STAGING                | Inventario                                             |
| Inventario              | Knowledge             | AssetChanged, lifecycle/context data autorizado                                          | Eventos/API            | Inventario                                             |
| Formularios             | Planificación         | FormDefinition + FormVersion publicada                                                   | API + eventos          | Formularios                                            |
| Planificación           | Mobile                | WorkPackage / TaskPackage                                                                | STAGING                | Planificación                                          |
| Mobile                  | Planificación/Control | TaskExecutionResult, CaptureResult, EvidenceReference, Alert                             | STAGING                | Mobile produce resultado; Planificación gobierna tarea |
| Mobile                  | Inventario            | Actualización autorizada derivada de captura, solo cuando el caso de uso lo permita      | STAGING/API            | Inventario                                             |
| Planificación           | Control/Knowledge     | TaskCreated/Started/Completed, resultados y cumplimiento                                 | Eventos                | Planificación                                          |
| Control                 | Knowledge             | contexto operacional, indicadores y situaciones autorizadas                              | API/eventos            | Cada dominio conserva su dato                          |
| Knowledge               | Advisory              | Situation, Recommendation, ExplanationContext                                            | API/eventos            | Knowledge                                              |
| Advisory                | Planificación         | ActionProposal; nunca escritura directa de Task                                          | API/comando autorizado | Planificación                                          |
| Servicios Transversales | Todos                 | Identity, audit, files, notifications, search, configuration                             | Servicios comunes      | Transversal                                            |

# 6. Catálogo de eventos MVP

| **Dominio**  | **Evento**                 | **Momento**                      | **Consumidores principales**             |
|--------------|----------------------------|----------------------------------|------------------------------------------|
| Organization | FarmCreated.v1             | Finca creada/publicada           | Inventory, Planning, Knowledge           |
| Organization | FarmUpdated.v1             | Finca modificada                 | Inventory, Planning, Knowledge           |
| Organization | FarmDeactivated.v1         | Finca desactivada                | Todos los consumidores autorizados       |
| Organization | SpaceCreated.v1            | Espacio creado                   | Inventory, Forms, Planning, Track futuro |
| Organization | SpaceUpdated.v1            | Espacio modificado               | Inventory, Forms, Planning               |
| Organization | SpaceDeactivated.v1        | Espacio desactivado              | Inventory, Planning                      |
| Organization | ProcessUpdated.v1          | Proceso actualizado              | Forms, Planning, Knowledge               |
| Organization | ActivityTypeUpdated.v1     | Tipo de actividad actualizado    | Forms, Planning                          |
| Organization | UserFarmAssigned.v1        | Usuario asociado a finca         | Transversal/autorización                 |
| Inventory    | AssetCreated.v1            | Activo creado                    | Planning, Forms, Knowledge, Analytics    |
| Inventory    | AssetUpdated.v1            | Activo actualizado               | Planning, Knowledge, Analytics           |
| Inventory    | AssetStatusChanged.v1      | Estado cambiado                  | Planning, Knowledge, Analytics           |
| Inventory    | AssetLifecycleChanged.v1   | Etapa del ciclo cambia           | Knowledge, Analytics, Advisory           |
| Forms        | FormVersionPublished.v1    | Versión publicada                | Planning                                 |
| Planning     | TaskCreated.v1             | Tarea creada                     | Mobile, Control                          |
| Planning     | TaskPublished.v1           | Tarea disponible para campo      | Mobile                                   |
| Planning     | TaskStarted.v1             | Tarea iniciada                   | Control, Knowledge                       |
| Planning     | TaskCompleted.v1           | Tarea finalizada                 | Control, Knowledge                       |
| Planning     | TaskValidated.v1           | Resultado validado               | Control, Knowledge                       |
| Mobile       | TaskExecutionResult.v1     | Resultado capturado/sincronizado | Planning, Control                        |
| Mobile       | AlertCreated.v1            | Alerta enviada desde campo       | Control, Knowledge/Advisory              |
| Knowledge    | SituationDetected.v1       | Situación identificada           | Control, Advisory                        |
| Knowledge    | RecommendationPublished.v1 | Recomendación disponible         | Advisory, Control                        |
| Advisory     | ActionProposalCreated.v1   | Propuesta de acción              | Planning/usuario                         |
| Transversal  | FileEvidenceRegistered.v1  | Evidencia registrada             | Dominio propietario del contexto         |

# 7. Reglas de publicación y consumo de eventos

- El productor persiste primero la transacción funcional y su Outbox dentro de la misma unidad transaccional.

- Un proceso de publicación toma registros Outbox pendientes y los entrega a STAGING.

- El consumidor registra Inbox antes o durante el procesamiento según estrategia transaccional adoptada.

- Si el mismo messageId/idempotencyKey llega nuevamente, el consumidor no debe repetir el efecto funcional.

- Los eventos deben ser hechos ocurridos, no órdenes disfrazadas. Un comando se modela como comando.

- Un evento no debe obligar al consumidor a consultar tablas internas del productor.

- Los consumidores deben tolerar eventos fuera de orden cuando el contrato lo permita; si no es posible, deben declarar dependencia y checkpoint.

- Un evento incompatible no se corrige silenciosamente; se rechaza, registra y envía a manejo de excepción.

- Desactivación y cambios relevantes deben publicarse para que los consumidores puedan invalidar sus proyecciones.

- Los eventos históricos no se reinterpretan como reglas de negocio.

# 8. STAGING — frontera de intercambio

STAGING es un componente técnico de integración. No es un dominio funcional y no se convierte en dueño de información de negocio.

| **Componente**    | **Responsabilidad**                                                      | **No hace**                                             |
|-------------------|--------------------------------------------------------------------------|---------------------------------------------------------|
| Inbox             | Registrar mensajes recibidos, estado de procesamiento y deduplicación.   | No redefine el payload.                                 |
| Outbox            | Registrar eventos a publicar de forma transaccional con el origen.       | No ejecutar reglas de negocio.                          |
| Checkpoint        | Guardar último punto confirmado por consumidor/proceso.                  | No modificar datos origen.                              |
| Retry Queue       | Programar reintentos de errores recuperables.                            | No reintentar errores funcionales no recuperables.      |
| Dead Letter Queue | Conservar mensajes que no pudieron procesarse tras la política definida. | No descartar silenciosamente.                           |
| Mapper/Adapter    | Transformar contrato externo al modelo interno autorizado.               | No crear maestros ni reglas.                            |
| Observability     | Registrar métricas, trazas y errores de integración.                     | No almacenar datos transaccionales como fuente maestra. |

## 8.1 Estados de un mensaje

> RECEIVED → VALIDATED → PROCESSING → PROCESSED  
> └→ RETRYING → PROCESSED  
> └→ REJECTED → DLQ

REJECTED se utiliza para errores de contrato o negocio no recuperables. RETRYING se utiliza para fallas técnicas recuperables. DLQ conserva evidencia y permite reproceso controlado.

# 9. Sincronización Mobile ↔ STAGING ↔ Núcleo

Mobile no modifica la definición de tarea, formulario o población objetivo. Recibe un paquete de trabajo y produce resultados, capturas, evidencias y alertas. La sincronización mueve estos resultados hacia el núcleo.

> NÚCLEO  
> Planificación → crea/publica WorkPackage  
> ↓  
> STAGING  
> ↓  
> MOBILE  
> ↓  
> captura offline / resultados / evidencias / alertas  
> ↓  
> STAGING  
> ↓  
> validación + deduplicación + ordenamiento + aplicación  
> ↓  
> Planificación / Inventario / Control / Knowledge  
> ↓  
> eventos derivados

| **Regla** | **Comportamiento**                                                                                            |
|-----------|---------------------------------------------------------------------------------------------------------------|
| Paquete   | Debe identificar TaskID, versión de tarea, FormVersion, población recibida y contexto.                        |
| Captura   | Cada resultado debe identificar TaskID + AssetID cuando aplique + captureId.                                  |
| Offline   | El dispositivo genera identificadores locales/globales estables y conserva occurredAt/origen.                 |
| Reenvío   | Una captura enviada dos veces no duplica el resultado.                                                        |
| Conflicto | Si la tarea/activo cambió de forma incompatible, el núcleo marca conflicto; Mobile no decide silenciosamente. |
| Orden     | La sincronización debe tolerar paquetes fuera de orden mediante dependencia/checkpoint.                       |
| Evidencia | El archivo y su metadato se sincronizan con referencia trazable al resultado.                                 |
| Alerta    | La alerta se conserva aunque la tarea siga abierta; no se convierte automáticamente en tarea.                 |
| Resultado | El resultado por activo mantiene éxito/falla/cancelado/no ejecutado según la definición funcional.            |
| Cierre    | La tarea no se cierra por simple recepción; respeta el flujo de validación de Planificación.                  |

# 10. Contratos funcionales mínimos

## 10.1 FarmContext

> {  
> "organizationId": "org",  
> "farmId": "farm",  
> "name": "Finca",  
> "status": "ACTIVE",  
> "location": {"departmentId": "...", "municipalityId": "..."},  
> "classification": {  
> "industryId": "...",  
> "sectorId": "...",  
> "businessId": "..."  
> }  
> }

La clasificación Industria → Sector → Negocio es contexto de Organización. Knowledge puede consumirla para aplicar reglas; el contrato no transporta reglas ni datos de mercado.

## 10.2 AssetReference

> {  
> "assetId": "asset",  
> "farmId": "farm",  
> "assetTypeId": "type",  
> "displayName": "Activo",  
> "identifiers": \[{"type": "RFID", "value": "..."}\],  
> "status": "ACTIVE",  
> "locationRef": {"spaceId": "..."},  
> "lifecycle": {"modelId": "...", "stageId": "..."}  
> }

Es una referencia autorizada; los consumidores no reciben permiso implícito para modificar Inventario.

## 10.3 FormVersionReference

> {  
> "formId": "form",  
> "formVersionId": "version",  
> "status": "PUBLISHED",  
> "context": {  
> "processId": "...",  
> "activityTypeId": "..."  
> }  
> }

Planificación debe poder reconstruir exactamente qué versión del formulario recibió el campo.

## 10.4 WorkPackage

> {  
> "packageId": "pkg",  
> "taskId": "task",  
> "taskVersion": 3,  
> "farmId": "farm",  
> "form": {"formId": "...", "formVersionId": "..."},  
> "population": \[{"assetId": "..."}\],  
> "assignment": {"userId": "..."},  
> "context": {"spaceId": "...", "processId": "...", "activityTypeId": "..."},  
> "issuedAt": "UTC"  
> }

La población es calculada por Planificación. Mobile no la recalcula ni la amplía.

## 10.5 TaskExecutionResult

> {  
> "captureId": "capture",  
> "taskId": "task",  
> "assetId": "asset",  
> "formVersionId": "version",  
> "resultStatus": "SUCCESS",  
> "capturedAt": "UTC",  
> "capturedBy": "user/device",  
> "values": {...},  
> "evidence": \[{"evidenceId": "..."}\],  
> "source": "MOBILE_OFFLINE"  
> }

## 10.6 Alert

> {  
> "alertId": "alert",  
> "farmId": "farm",  
> "taskId": "task",  
> "assetId": "asset",  
> "type": "EXCEPTION",  
> "priority": "HIGH",  
> "channel": "VOICE",  
> "message": "...",  
> "evidence": \[{"evidenceId": "..."}\],  
> "occurredAt": "UTC"  
> }

Una alerta representa una situación detectada por el campo. Su interpretación, severidad derivada y eventual recomendación corresponden a los dominios de control/Knowledge/Advisory según el caso.

# 11. Integraciones externas del MVP

| **Sistema**                | **Papel en MVP**                               | **Integración**                                        | **Regla**                                                                                  |
|----------------------------|------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------------------------------------------|
| ODOO                       | ERP/negocio externo a GANUS Operativo          | Adaptador/API/eventos cuando el caso esté priorizado   | No se convierte en acceso directo desde dominios GANUS; la integración entra por contrato. |
| n8n                        | Orquestación externa                           | API/webhook/automatización                             | No contiene lógica transaccional central de GANUS.                                         |
| Almacenamiento de archivos | Evidencias/documentos                          | Servicio transversal + referencia                      | El dominio guarda metadatos; no duplica archivos innecesariamente.                         |
| Servicios de identidad     | Autenticación/autorización                     | Servicio transversal                                   | El dominio usa identidad y ámbito autorizados.                                             |
| Track                      | Futuro / fuera del núcleo MVP operativo actual | Contrato preparado, implementación según backlog Track | No duplicar identidad ni espacios; Track agrega geometría/telemetría.                      |

Las integraciones externas no deben entrar directamente a tablas PostgreSQL de GANUS. Cada integración debe tener adapter, contrato, mapeo, control de errores y trazabilidad.

# 12. Flujos de integración críticos

## 12.1 Alta de activo y disponibilidad para planificación

1.  Inventario crea el activo y obtiene ActivoID.

2.  Inventario registra el evento AssetCreated en Outbox.

3.  STAGING publica/entrega el evento.

4.  Planificación actualiza su referencia autorizada o consulta el activo mediante API.

5.  El activo queda disponible como población elegible según reglas de Planificación.

6.  Knowledge puede recibir el cambio para contexto e inteligencia.

## 12.2 Publicación de tarea al campo

7.  Planificación valida tarea, formulario/versionado y población.

8.  Planificación crea/publica Task y WorkPackage.

9.  Se publica TaskPublished.

10. STAGING entrega el paquete al dispositivo autorizado.

11. Mobile confirma recepción sin modificar la población.

12. El usuario ejecuta y captura.

## 12.3 Resultado de campo

13. Mobile registra TaskExecutionResult localmente.

14. Al existir conectividad, envía el mensaje a STAGING.

15. STAGING valida sobre, contrato e idempotencia.

16. El consumidor registra Inbox y aplica el resultado.

17. Planificación actualiza el avance de la tarea.

18. Control/Knowledge reciben eventos derivados.

19. Si existe evidencia, su referencia queda asociada al resultado.

## 12.4 Recomendación que termina en acción

20. Knowledge evalúa datos y contexto según reglas autorizadas.

21. Knowledge produce Situation y Recommendation.

22. Advisory explica la recomendación al usuario.

23. Si el usuario acepta una acción, se genera ActionProposal.

24. Planificación recibe una solicitud/command autorizado.

25. Planificación crea la tarea conforme a sus propias reglas y estados.

26. Advisory nunca escribe directamente una Task en la base de datos.

# 13. Idempotencia, consistencia y concurrencia

| **Caso**                               | **Regla**                                                                        |
|----------------------------------------|----------------------------------------------------------------------------------|
| Duplicado físico                       | messageId procesado previamente → no repetir efecto.                             |
| Duplicado funcional                    | idempotencyKey ya aplicada → devolver resultado existente cuando sea posible.    |
| Misma entidad, dos cambios             | Usar version/revision/updatedAt controlado por dominio; detectar conflicto.      |
| Evento tardío                          | No sobrescribir un estado más nuevo sin regla explícita.                         |
| Evento fuera de orden                  | Usar versión/secuencia del agregado o dependencia declarada.                     |
| Transacción origen + evento            | Usar Outbox para evitar que la transacción se confirme sin publicación trazable. |
| Evento publicado pero consumidor caído | Conservar en STAGING/retry hasta confirmación o DLQ.                             |
| Corrección                             | Publicar nuevo hecho/cambio; no borrar silenciosamente el evento histórico.      |

## 13.1 Clave de idempotencia

La clave debe ser determinística respecto a la operación. Ejemplo conceptual: domain:entity:operation:entityId:clientOperationId. Nunca depender exclusivamente de timestamp.

# 14. Seguridad y aislamiento

- Todo mensaje interno debe transportar o resolver el tenant/organización y la finca cuando aplique.

- El consumidor valida que el actor tenga ámbito autorizado; el mensaje no puede otorgar permisos.

- Un usuario de una finca no puede leer ni producir cambios para otra finca por manipulación del payload.

- Los contratos no deben transportar secretos, tokens internos ni credenciales.

- Los archivos se referencian mediante IDs/URLs temporales o mecanismo seguro; no se exponen rutas internas.

- Los logs no deben registrar datos sensibles innecesarios.

- Los endpoints administrativos y de integración deben estar protegidos por autenticación, autorización y rate limiting cuando sean expuestos.

- Los mensajes de integración deben poder auditarse sin permitir su alteración silenciosa.

# 15. Observabilidad de integraciones

| **Métrica**              | **Propósito**                                |
|--------------------------|----------------------------------------------|
| messages_received_total  | Volumen recibido.                            |
| messages_processed_total | Procesamiento exitoso.                       |
| messages_failed_total    | Fallos.                                      |
| messages_retried_total   | Reintentos.                                  |
| messages_dlq_total       | Mensajes enviados a DLQ.                     |
| processing_latency       | Tiempo recepción → procesamiento.            |
| sync_pending             | Mensajes/capturas pendientes de sincronizar. |
| sync_conflicts           | Conflictos de sincronización.                |
| outbox_pending           | Eventos pendientes de publicación.           |
| inbox_duplicates         | Mensajes duplicados detectados.              |

Toda trazabilidad operativa debe permitir buscar por correlationId, messageId, eventId, tenantId, farmId y entidad principal cuando aplique.

# 16. Gobierno y versionamiento de contratos

- Todo contrato tiene nombre, versión, propietario, productor, consumidores, propósito y estado.

- Los cambios compatibles pueden mantener la versión mayor y documentar el cambio.

- Los cambios incompatibles crean nueva versión; no se rompe silenciosamente a consumidores existentes.

- Un contrato puede estar DRAFT, ACTIVE, DEPRECATED o RETIRED.

- DEPRECATED debe tener fecha de retiro y consumidores migrados.

- Los ejemplos de payload son parte de la documentación y deben probarse.

- Cada contrato debe tener pruebas de schema y al menos una prueba de consumidor/productor.

- Las decisiones estructurales deben registrarse como ADR cuando afecten arquitectura.

# 17. Registro inicial de contratos

| **ID**    | **Contrato**             | **Tipo**      | **Owner**     | **Estado MVP** |
|-----------|--------------------------|---------------|---------------|----------------|
| CT-ORG-01 | FarmContext.v1           | API/DTO       | Organización  | ACTIVE         |
| CT-ORG-02 | SpaceReference.v1        | API/Event     | Organización  | ACTIVE         |
| CT-ORG-03 | OrganizationContext.v1   | API/Event     | Organización  | ACTIVE         |
| CT-INV-01 | AssetReference.v1        | API/Event     | Inventario    | ACTIVE         |
| CT-INV-02 | AssetLifecycleChanged.v1 | Event         | Inventario    | ACTIVE         |
| CT-FRM-01 | FormVersionReference.v1  | API/Event     | Formularios   | ACTIVE         |
| CT-PLN-01 | WorkPackage.v1           | STAGING       | Planificación | ACTIVE         |
| CT-PLN-02 | TaskStateChanged.v1      | Event         | Planificación | ACTIVE         |
| CT-MOB-01 | TaskExecutionResult.v1   | STAGING       | Mobile        | ACTIVE         |
| CT-MOB-02 | AlertCreated.v1          | STAGING/Event | Mobile        | ACTIVE         |
| CT-KN-01  | Situation.v1             | API/Event     | Knowledge     | ACTIVE         |
| CT-KN-02  | Recommendation.v1        | API/Event     | Knowledge     | ACTIVE         |
| CT-ADV-01 | ActionProposal.v1        | API/Command   | Advisory      | ACTIVE         |

# 18. Fronteras: quién puede hacer qué

| **Componente** | **Puede**                                                                                       | **No puede**                                                                                   |
|----------------|-------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| Organización   | Publicar contexto, fincas, espacios, procesos, actividades, clasificación empresarial.          | Crear activos, tareas, capturas, reglas o recomendaciones.                                     |
| Inventario     | Crear/modificar identidad, atributos, relaciones, ciclo, estado e historial del activo.         | Planificar trabajo o interpretar reglas de negocio.                                            |
| Formularios    | Publicar definición/versiones de captura.                                                       | Crear tareas, asignar usuarios o crear identidad de activos.                                   |
| Planificación  | Calcular población, crear/programar/asignar tareas, validar/cerrar.                             | Modificar identidad maestra de activos o ejecutar capturas en nombre del campo.                |
| Mobile         | Recibir paquete, ejecutar, capturar, adjuntar evidencia, alertar, sincronizar.                  | Segmentar población, cambiar formulario/tarea o modificar maestros fuera de casos autorizados. |
| Control        | Presentar avance, cumplimiento, estados e indicadores definidos.                                | Convertirse en maestro transaccional de tareas/activos.                                        |
| Knowledge      | Aplicar reglas, interpretar datos, producir situaciones/indicadores/recomendaciones gobernadas. | Escribir directamente en dominios transaccionales.                                             |
| Advisory       | Conversar, explicar, contextualizar y proponer acciones.                                        | Crear/modificar directamente una Task o Asset.                                                 |
| STAGING        | Transportar, deduplicar, reintentar, registrar y enrutar.                                       | Decidir negocio, ser maestro o alterar significado.                                            |
| n8n            | Orquestar integraciones externas.                                                               | Ser núcleo de transacciones GANUS.                                                             |

# 19. Criterios de aceptación

| **ID** | **Criterio**                                                                                                                                      |
|--------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| CI-001 | Ningún dominio consulta directamente tablas internas de otro dominio.                                                                             |
| CI-002 | Un evento puede procesarse dos veces sin duplicar su efecto.                                                                                      |
| CI-003 | Un evento conserva correlationId y trazabilidad hasta su consumidor.                                                                              |
| CI-004 | Una falla técnica de consumidor deja mensaje recuperable mediante retry.                                                                          |
| CI-005 | Un mensaje no procesable queda en DLQ con causa y contexto.                                                                                       |
| CI-006 | La creación de un activo publica identidad y contexto autorizado sin duplicar maestro.                                                            |
| CI-007 | La publicación de una tarea entrega al móvil la versión exacta de formulario y población calculada.                                               |
| CI-008 | Mobile puede generar resultados offline y sincronizarlos posteriormente.                                                                          |
| CI-009 | Un resultado reenviado no genera doble captura.                                                                                                   |
| CI-010 | Una alerta de campo llega al centro aun cuando la tarea permanezca abierta.                                                                       |
| CI-011 | Una recomendación aceptada por el usuario llega a Planificación como propuesta/command autorizado, nunca como escritura directa de Advisory.      |
| CI-012 | Los contratos versionados permiten evolucionar sin romper consumidores existentes.                                                                |
| CI-013 | Se puede reconstruir una transacción de integración usando messageId/correlationId.                                                               |
| CI-014 | La clasificación Industria → Sector → Negocio puede llegar a Knowledge como contexto, sin trasladar a Organización lógica de reglas de Knowledge. |
| CI-015 | ODOO/n8n y futuras integraciones externas entran por adapters/contratos, no por acceso directo a BD.                                              |

# 20. Matriz mínima de pruebas de integración

| **Área**           | **Pruebas mínimas**                                                          |
|--------------------|------------------------------------------------------------------------------|
| Contrato           | Schema válido, campos obligatorios, tipos, versión incompatible.             |
| Idempotencia       | Mismo messageId, misma idempotencyKey, reintento tras timeout.               |
| Seguridad          | Finca autorizada/no autorizada, tenant cruzado, actor inválido.              |
| Outbox             | Transacción exitosa + evento; rollback + no evento.                          |
| Inbox              | Procesamiento exitoso, duplicado, fallo recuperable, fallo permanente.       |
| Retry/DLQ          | Backoff, límite de reintentos, trazabilidad y reproceso controlado.          |
| Mobile             | Offline, reconexión, duplicado, conflicto, paquete incompleto, evidencia.    |
| Planificación      | Población no modificable desde Mobile, estados y cierre.                     |
| Inventario         | AssetID estable, actualización concurrente, lifecycle event.                 |
| Knowledge/Advisory | Situación/recomendación trazable; propuesta de acción sin escritura directa. |
| Externo            | Timeout, contrato inválido, API no disponible, respuesta inesperada.         |

# 21. Secuencia de implementación recomendada

27. Implementar envelope común, correlationId, error model y versionado.

28. Implementar STAGING técnico: Inbox, Outbox, retry, checkpoint y DLQ.

29. Implementar contratos de Organización y publicar contexto.

30. Implementar contratos de Inventario y referencias de activos/ciclo.

31. Implementar FormVersionReference.

32. Implementar WorkPackage y Task events.

33. Implementar Mobile sync y TaskExecutionResult/Alert.

34. Implementar consumo en Seguimiento/Centro de Control.

35. Implementar eventos de Knowledge y Recommendation.

36. Implementar ActionProposal hacia Planificación.

37. Agregar adapters externos priorizados, especialmente ODOO cuando exista caso de uso aprobado.

38. Completar pruebas de contrato, integración, seguridad, idempotencia y offline.

# 22. Definition of Done del documento 09

- Todos los contratos del registro tienen owner y consumidores identificados.

- Envelope común implementado y validado.

- Inbox/Outbox/idempotencia/retry/DLQ operativos.

- API no expone repositorios/tablas internas.

- Eventos principales del flujo Operativo publicados.

- WorkPackage y TaskExecutionResult permiten flujo completo web → campo → núcleo.

- Offline/sync mantiene trazabilidad y evita duplicados.

- Errores funcionales y técnicos están diferenciados.

- Pruebas de contrato e integración automatizadas.

- Observabilidad permite rastrear una operación extremo a extremo.

- Documentación de versionamiento y deprecación disponible.

- No se introdujo ninguna responsabilidad funcional nueva en un dominio.

# 23. Decisiones técnicas pendientes que NO deben resolverse inventando funcionalidad

| **Tema**             | **Decisión pendiente**                                                                                          |
|----------------------|-----------------------------------------------------------------------------------------------------------------|
| Broker interno       | Determinar implementación concreta del transporte event-driven dentro del modular monolith/infraestructura MVP. |
| Formato de schema    | JSON Schema/DTO validation y mecanismo de registry si se requiere.                                              |
| Persistencia STAGING | Definir tablas físicas finales y estrategia de partición/retención según volumen.                               |
| Object storage       | Seleccionar proveedor y estrategia de URLs temporales para evidencias.                                          |
| Auth técnico         | Definir mecanismo concreto de tokens/claims entre servicios internos/adapters.                                  |
| Retry policy         | Definir backoff, máximo de intentos y retención.                                                                |
| Conflict policy      | Definir resolución técnica para casos específicos de sincronización.                                            |
| External ODOO        | Definir endpoints/mapeos concretos cuando el backlog de integración sea priorizado.                             |

Estas decisiones son técnicas. No autorizan al desarrollador a cambiar las responsabilidades funcionales de los dominios.

# 24. Glosario

| **Término**          | **Definición**                                                                       |
|----------------------|--------------------------------------------------------------------------------------|
| Contrato             | Acuerdo formal de estructura y significado para intercambiar información.            |
| Evento               | Hecho que ya ocurrió y se publica para consumidores interesados.                     |
| Comando              | Solicitud para que un componente ejecute una acción autorizada.                      |
| STAGING              | Frontera técnica de intercambio y control de integración.                            |
| Inbox                | Registro de mensajes recibidos y su procesamiento.                                   |
| Outbox               | Registro de eventos pendientes de publicación, ligado a la transacción origen.       |
| Idempotencia         | Capacidad de procesar repetidamente una misma solicitud sin duplicar efecto.         |
| Checkpoint           | Punto de progreso confirmado por un consumidor/proceso.                              |
| DLQ                  | Cola de mensajes no procesables después de la política de reintentos.                |
| WorkPackage          | Paquete que Planificación entrega al campo con tarea, versión, contexto y población. |
| AssetReference       | Referencia autorizada al activo maestro de Inventario.                               |
| FormVersionReference | Referencia exacta a la versión publicada del formulario.                             |
| CorrelationId        | Identificador de trazabilidad de una operación extremo a extremo.                    |
| CausationId          | Identificador del mensaje que originó otro mensaje.                                  |
| Projection           | Copia derivada para lectura; no sustituye al maestro.                                |

# 25. Referencias documentales

- GANUS · 00 CEREBRO GANUS MVP — visión, principios, alcance y vocabulario.

- GANUS · 01 ARQUITECTURA GANUS — arquitectura modular monolítica, Clean/Hexagonal, API-first, event-driven y STAGING.

- GANUS · 02 FUNCIONAL ORGANIZACIÓN — contexto, fincas, espacios, procesos, clasificación Industria → Sector → Negocio, usuarios y salidas.

- GANUS · 03 FUNCIONAL INVENTARIO + CICLO DE VIDA — identidad única, ciclo, historial, publicación e interoperabilidad.

- GANUS · 04 FUNCIONAL FORMULARIOS DINÁMICOS — definición y versionamiento de captura.

- GANUS · 05 FUNCIONAL PLANIFICACIÓN — población, tareas, estados, despacho y control.

- GANUS · 06 FUNCIONAL MOBILE + OFFLINE — ejecución, voz, evidencia, alertas y sincronización.

- GANUS · 07 FUNCIONAL SEGUIMIENTO + CENTRO DE CONTROL + KNOWLEDGE + ADVISORY — situaciones, indicadores, recomendaciones y acciones.

- GANUS · 08 SERVICIOS TRANSVERSALES — identidad, navegación, búsqueda, notificaciones, auditoría, archivos, configuración y sincronización.

- GANUS · 10 MODELO BD REFERENCIA MVP — ownership lógico y ubicación de la información.

# 26. Regla de cierre

GANUS integra dominios; no los mezcla. Un contrato existe para que un dominio pueda entregar o solicitar información sin perder ownership, seguridad, trazabilidad ni contexto. STAGING existe para hacer confiable el intercambio; no para convertirse en un nuevo cerebro de negocio.

La implementación correcta es aquella en la que el desarrollador puede responder, para cada dato que cruza una frontera: quién lo creó, quién es su dueño, por qué cruza, qué contrato lo representa, cómo se identifica, cómo se repite sin duplicarse, cómo se audita y qué ocurre si falla.

REGLA FINAL: si una integración requiere crear una segunda fuente de verdad, acceso directo a tablas de otro dominio o lógica de negocio dentro de STAGING, la solución está mal diseñada y debe replantearse antes de codificar.
