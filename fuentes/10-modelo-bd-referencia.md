# 10 · Modelo BD de Referencia MVP v1.0

> **Fuente de solo lectura.** Convertido a Markdown desde `10_MODELO_BD_REFERENCIA_MVP_GANUS_v1.0.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**MODELO DE BASE DE DATOS — REFERENCIA MVP**

Modelo lógico y físico de referencia · Ownership · Relaciones · Integridad · Seguridad · Rendimiento

Versión 1.0 · 08 octubre 2026

**Propósito.** Traducir las definiciones funcionales cerradas de GANUS OPERATIVO a un modelo de información de referencia para PostgreSQL, manteniendo propiedad de datos por dominio, trazabilidad, versionamiento, operación offline y separación entre datos transaccionales, intercambio y explotación analítica.

# 0. Naturaleza y límites del documento

Este documento define el modelo lógico de referencia y las reglas de persistencia que el desarrollo debe respetar. No sustituye la Arquitectura GANUS oficial ni constituye todavía el DDL final de producción.

| **Nivel**    | **Documento / artefacto**  | **Responsabilidad**                                                      |
|--------------|----------------------------|--------------------------------------------------------------------------|
| Visión       | CEREBRO GANUS              | Propósito, principios y alcance.                                         |
| Arquitectura | Arquitectura GANUS oficial | Patrones técnicos, stack, despliegue y restricciones arquitectónicas.    |
| Funcional    | Documentos 02–09           | Qué debe hacer GANUS y cómo se comporta.                                 |
| BD           | Este documento             | Qué información debe persistirse, quién es su dueño y cómo se relaciona. |
| DDL          | Artefacto posterior        | CREATE TABLE, índices, constraints, migrations y detalles PostgreSQL.    |
| Desarrollo   | Constitución de Desarrollo | Cómo debe implementarse el código.                                       |

**NOTA:** Cuando una definición funcional cambie, el modelo BD debe actualizarse mediante una decisión trazable. El modelo no debe crear funcionalidades que no existan en la especificación funcional.

# 1. Principios de modelado

| **Principio**              | **Regla**                                                                                                                |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------|
| Una fuente de verdad       | Cada entidad maestra tiene un único dominio propietario.                                                                 |
| Ownership                  | Un módulo puede consultar datos autorizados de otro dominio, pero no apropiarse de su persistencia.                      |
| Identidad estable          | Los identificadores internos de entidades principales son estables y no dependen de nombres editables.                   |
| Trazabilidad               | Los cambios relevantes y resultados operativos deben poder reconstruirse.                                                |
| Versionamiento             | Configuraciones publicadas se congelan; los cambios generan versiones nuevas.                                            |
| No eliminación destructiva | Activos y registros críticos conservan historia; se cambia estado o se aplica baja lógica.                               |
| Contexto                   | Los datos operativos están asociados al alcance organizacional/finca correspondiente.                                    |
| Integridad                 | Las invariantes críticas se protegen con constraints y validación de aplicación.                                         |
| Offline                    | Mobile puede conservar información de trabajo y resultados localmente y sincronizar posteriormente.                      |
| Intercambio separado       | Inbox/Outbox y colas de intercambio no son sustitutos de las entidades de negocio.                                       |
| Eficiencia                 | El modelo debe permitir consultas indexadas, paginación y sincronización incremental.                                    |
| Evolución                  | No se introducen bases adicionales o microservicios por anticipación; la arquitectura oficial permanece como referencia. |

# 2. Arquitectura lógica de información

> ORGANIZACIÓN / SEGURIDAD  
> │  
> ├── Finca / Espacios / Usuarios / Roles  
> │  
> ├───────────────┐  
> ↓ ↓  
> INVENTARIO FORMULARIOS  
> │ │  
> │ └── Definición / versión / campos / validaciones  
> │  
> └──────────────┐  
> ↓  
> PLANIFICACIÓN  
> │  
> ↓  
> STAGING  
> │  
> ↓  
> MOBILE  
> │  
> ↓  
> RESULTADOS  
> │  
> ┌──────────┼──────────┐  
> ↓ ↓ ↓  
> CONTROL KNOWLEDGE AUDITORÍA  
> │  
> ↓  
> ADVISORY

La base común puede utilizar separación lógica por dominio. La propiedad funcional de cada entidad permanece clara aunque físicamente exista en la misma instancia PostgreSQL.

# 3. Convenciones generales

| **Elemento**     | **Convención recomendada**                                                                                   |
|------------------|--------------------------------------------------------------------------------------------------------------|
| PK               | UUID/identificador interno estable para entidades principales.                                               |
| Código humano    | Campo separado, generado y/o parametrizado; nunca usarlo como FK principal si puede cambiar.                 |
| Fechas           | timestamp con zona/normalización definida por arquitectura; conservar instante real.                         |
| Estado           | Código controlado por dominio; no texto libre.                                                               |
| JSON             | Solo para estructuras realmente variables o payloads de integración; no esconder entidades maestras en JSON. |
| FK               | Siempre hacia la PK/ID estable del propietario.                                                              |
| Audit timestamps | created_at, updated_at y, donde corresponda, actor/origen.                                                   |
| Soft delete      | deleted_at/deactivated_at solo cuando la entidad requiera baja lógica; no universalizar sin criterio.        |
| Version          | version_id o número de versión controlado por la entidad versionable.                                        |
| Idempotencia     | clave de idempotencia separada de PK de negocio.                                                             |
| Correlation      | correlation_id para seguir un flujo entre módulos.                                                           |
| Tenant/context   | organization/group/farm scope según entidad y regla funcional.                                               |

**NOTA:** La elección final entre UUID y otro mecanismo debe respetar la Arquitectura oficial y la estrategia de migrations. Este documento no obliga una librería concreta.

# 4. Propiedad de datos por dominio

| **Dominio**      | **Entidades propietarias principales**                                                                                                  | **No debe ser dueño de**                   |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------|
| Organization     | Group, Farm, User, Role, Permission, UserFarm/Assignment, Space, Process, ActivityType                                                  | Asset, Task, Form result, KPI.             |
| Inventory        | Asset, AssetIdentifier, AssetAttribute/values, AssetRelation, AssetDocumentLink, AssetHistory, LifecycleAssignment, LifecycleTransition | Task scheduling, Rule, KPI.                |
| Forms            | Form, FormVersion, FormField, FormSection, FormKey, FormValidation, FormCatalog/Option, Publication history                             | Task, target population, execution result. |
| Planning         | Task, TaskDataset/Target, TaskAssignment, TaskStateHistory, TaskAssetResult, schedule/planning data                                     | Asset identity, Form definition.           |
| Mobile/Execution | Local device structures; server-side execution/result records as defined by operational ownership                                       | Master Asset identity, Form definition.    |
| Staging          | InboxMessage, OutboxMessage, SyncPackage, SyncItem, Checkpoint, DeadLetter                                                              | Business master records.                   |
| Control          | Operational read/aggregation models where needed; not source of truth for transactions                                                  | Asset/task/form master.                    |
| Knowledge        | Rule, IndicatorDefinition, Situation, KnowledgeSource, Evaluation/Result, Recommendation                                                | Transactional task execution.              |
| Advisory         | Recommendation interpretation/action proposal where separate from Knowledge ownership                                                   | Master asset/task.                         |
| Transversal      | Session, Notification, AuditEvent, File, ImportJob, ExportJob, Preferences, SystemIntegrationStatus                                     | Domain business entities.                  |

# 5. Organización y seguridad — modelo de información

| **Entidad**          | **Propósito**                                  | **Relaciones principales**                                          |
|----------------------|------------------------------------------------|---------------------------------------------------------------------|
| organization_group   | Grupo empresarial opcional.                    | 1:N farm; puede no existir para una finca independiente.            |
| farm                 | Finca/contexto operativo principal.            | N:1 group opcional; 1:N users/assignments, spaces, assets, tasks.   |
| user                 | Identidad de usuario.                          | N:M farm mediante assignment; N:M role según modelo de asignación.  |
| role                 | Rol funcional predefinido.                     | N:M permissions; asignado a usuarios en contexto.                   |
| permission           | Capacidad autorizable.                         | N:M role.                                                           |
| user_farm_assignment | Asignación del usuario a una finca.            | user + farm + estado + contexto.                                    |
| user_role_assignment | Asignación de rol en alcance definido.         | user + role + farm/organization scope.                              |
| space_type           | Tipo de espacio físico.                        | 1:N space; configurable.                                            |
| space                | Espacio territorial/operativo dentro de finca. | N:1 farm; N:1 parent space opcional.                                |
| process              | Proceso operativo.                             | 1:N activity_type.                                                  |
| activity_type        | Tipo de actividad.                             | N:1 process; consumido por Planning/Forms según reglas funcionales. |

La estructura física es genérica: una finca puede definir jerarquías como Finca → Sector → Potrero, Finca → Lote → Potrero, Finca → Potrero o Finca → Corral. No se debe codificar una única jerarquía.

# 6. Inventario — núcleo del modelo

**AssetType → AssetTemplate → AssetTemplateVersion → Asset → Identifiers → Location → Relations → Attributes → Documents → History**

| **Entidad**            | **Campos funcionales mínimos**                                                                    | **Ownership**                                       |
|------------------------|---------------------------------------------------------------------------------------------------|-----------------------------------------------------|
| asset_type             | id, domain/category/type/subtype, status, metadata                                                | Inventory/configuration master según diseño oficial |
| asset_template         | id, asset_type_id, name, status                                                                   | Inventory consume configuración publicada           |
| asset_template_version | id, template_id, version_no, status, published_at                                                 | Inventory/Forms boundary según configuración        |
| asset                  | asset_id, farm_id, template_version_id, generated_code, status, lifecycle_id opcional, created_at | Inventory                                           |
| asset_identifier       | id, asset_id, identifier_type, value, normalized_value, status, validity                          | Inventory                                           |
| asset_attribute_value  | id, asset_id, attribute_key/field_id, typed value, source, effective dates                        | Inventory                                           |
| asset_relation         | id, source_asset_id, target_asset_id, relation_type, valid_from/to                                | Inventory                                           |
| asset_location         | id, asset_id, space_id, valid_from/to, source                                                     | Inventory                                           |
| asset_history          | id, asset_id, event_type, before/after reference, actor, timestamp, source                        | Inventory                                           |
| asset_file_link        | asset_id, file_id, category                                                                       | Inventory + transversal Files                       |

**NOTA:** AssetID es la identidad transversal. RFID, ID GANUS, chapeta, código oficial, QR u otros identificadores resuelven al mismo AssetID; no crean activos paralelos.

# 7. Taxonomía de activos

> Dominio → Categoría → Tipo → Subtipo

- La taxonomía es maestra y parametrizable.

- Debe soportar activos biológicos, territoriales, infraestructura, equipos, humanos cuando aplique, suministros e identidades/dispositivos digitales según alcance.

- Inventory consume tipos/plantillas publicados; no debe duplicar el catálogo maestro.

- Los códigos de activo se generan automáticamente y no son editables por el usuario.

- El modelo no debe usar columnas específicas para 'vaca', 'búfalo', etc. como diseño estructural.

# 8. Ciclo de vida del activo

Todo activo puede quedar asociado a un ciclo de vida desde su creación. El ciclo define etapas esperadas, tareas referenciales, condiciones e indicadores. La etapa actual se determina por reglas/fechas/datos configurados y sus transiciones deben conservar historia.

| **Entidad**                | **Propósito**                                                                                 |
|----------------------------|-----------------------------------------------------------------------------------------------|
| lifecycle_definition       | Define el ciclo aplicable a un tipo/plantilla o alcance.                                      |
| lifecycle_version          | Versión publicada de la definición.                                                           |
| lifecycle_stage            | Etapa dentro de una versión.                                                                  |
| lifecycle_stage_task       | Tarea referencial de una etapa; guía Planning, no crea automáticamente una tarea salvo regla. |
| lifecycle_condition        | Condición que Knowledge puede evaluar.                                                        |
| lifecycle_indicator_ref    | Indicador relevante de etapa.                                                                 |
| asset_lifecycle_assignment | Ciclo asignado al activo desde creación o cambio controlado.                                  |
| asset_lifecycle_transition | Historial de transición: etapa anterior/nueva, fecha, fuente/evento, actor.                   |

**Activo creado → Ciclo asignado → Etapa actual → Condiciones → Indicadores → Desviación → Knowledge → Alert/Recommendation → Planning**

**NOTA:** La relación con Knowledge y Advisory es funcional: el modelo de BD conserva la configuración y trazabilidad; el motor de reglas decide la interpretación.

# 9. Formularios — definición versionada

El modelo de Forms representa la definición, no la ejecución. La parametrización debe permitir que la misma definición sea utilizada en Preview, Planning, Web y Mobile según los límites funcionales acordados.

| **Entidad**           | **Propósito**                                                           |
|-----------------------|-------------------------------------------------------------------------|
| form                  | Identidad estable de la definición.                                     |
| form_version          | Snapshot/version publicada o en borrador.                               |
| form_section          | Agrupación/presentación lógica.                                         |
| form_field            | Campo de captura: nombre, tipo, orden, required, preload, help, status. |
| form_field_validation | Reglas de validación del campo.                                         |
| form_key              | Clave de búsqueda/contexto habilitada por la definición.                |
| form_catalog          | Catálogo local/referenciado utilizado por campos.                       |
| form_catalog_option   | Opciones de catálogo.                                                   |
| form_publication      | Registro de publicación/versionado.                                     |

En la versión funcional refinada, las claves de búsqueda son dimensiones habilitadas para localizar/segmentar activos y las claves de contexto son dimensiones funcionales de trazabilidad. No deben convertirse en campos duplicados.

# 10. Tipos de datos de captura

| **Familia** | **Ejemplos**                                |
|-------------|---------------------------------------------|
| Texto       | Texto corto, texto largo/nota.              |
| Numérico    | Entero, decimal.                            |
| Temporal    | Fecha, fecha-hora.                          |
| Selección   | Única, múltiple, sí/no.                     |
| Evidencia   | Foto, documento, video, nota de voz, firma. |

La forma de captura —voz, selección, cámara, RFID, báscula, API u otra— pertenece al mecanismo de ejecución y no debe confundirse con el tipo de dato persistido. En particular, voz no es un tipo de campo.

# 11. Capturas y resultados de formularios

| **Entidad**               | **Propósito**                                                                          |
|---------------------------|----------------------------------------------------------------------------------------|
| form_execution_result     | Cabecera del resultado de una captura: tarea, form_version, user, context, timestamps. |
| form_execution_value      | Valor estructurado por field_id.                                                       |
| form_execution_evidence   | Relación entre resultado y file/evidence.                                              |
| form_execution_validation | Resultado de validaciones relevantes.                                                  |
| asset_form_result_link    | Relación resultado ↔ AssetID cuando la captura aplica a un activo.                     |

**TaskID → FormVersionID → AssetID/context → Captured values → Evidence → Validation → Result**

**NOTA:** Un resultado no debe sobrescribir automáticamente cualquier atributo del Asset. Solo campos explícitamente autorizados/mapeados pueden actualizar atributos maestros; el resto permanece como resultado histórico/evidencia.

# 12. Planificación — unidad de trabajo

La unidad operacional es la tarea/orden operativa. Planificación determina qué trabajo hacer, sobre quién, cuándo y quién lo ejecuta.

| **Entidad**        | **Propósito**                                                                              |
|--------------------|--------------------------------------------------------------------------------------------|
| task               | Identidad de la tarea/orden: form_version, activity_type, farm, schedule, priority, state. |
| task_target        | Población objetivo calculada y congelada para la ejecución.                                |
| task_assignment    | Responsable/supervisor/operador/cuadrilla asignada.                                        |
| task_schedule      | Fecha/hora, duración estimada, recurrencia aplicada si corresponde.                        |
| task_state_history | Historial completo de cambios de estado.                                                   |
| task_asset_result  | Resultado individual por activo.                                                           |
| task_condition     | Condiciones obligatorias/operativas de la tarea.                                           |
| task_exception     | Excepción o causa registrada durante ejecución.                                            |

La población se define antes del campo. Mobile no cambia la población objetivo; únicamente identifica/selecciona dentro del paquete recibido.

# 13. Estado de tarea y resultado por activo

| **Objeto**      | **Estados**                                                                                                                                            |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| Task            | Borrador → Validada → Programada → Disponible para campo → En ejecución → Pausada → Finalizada → Por validar → Cerrada; Cancelada como salida alterna. |
| TaskAssetResult | Pendiente → En ejecución → Ejecutado exitosamente / Ejecutado con inconsistencia / No ejecutado / Cancelado.                                           |

**NOTA:** La versión final funcional del MVP debe prevalecer sobre versiones anteriores que utilicen estados alternativos.

# 14. Mobile y persistencia local

El servidor debe modelar el paquete y resultados necesarios para soportar Mobile offline-first. La base local Android es una persistencia operativa del dispositivo; no es una segunda fuente maestra de activos o formularios.

| **Entidad servidor**     | **Uso en Mobile**                                                   |
|--------------------------|---------------------------------------------------------------------|
| sync_package             | Paquete lógico entregado al dispositivo.                            |
| sync_package_item        | Cada elemento/versión incluida.                                     |
| device                   | Identidad del dispositivo autorizado.                               |
| device_user_session      | Relación dispositivo/sesión/usuario cuando corresponda.             |
| mobile_task_state        | Estado recibido/ejecutado en dispositivo.                           |
| mobile_result_submission | Envío de resultados.                                                |
| mobile_alert_submission  | Envío de alertas desde campo.                                       |
| sync_checkpoint          | Punto hasta el que el dispositivo/servidor confirmó sincronización. |

La base local debe almacenar tareas, formularios/versiones, dataset, contexto, resultados, estados, evidencias, alertas y cola de sincronización necesarios para el trabajo autorizado.

# 15. STAGING — frontera de intercambio

| **Entidad**             | **Propósito**                                                                   |
|-------------------------|---------------------------------------------------------------------------------|
| staging_inbox_message   | Mensaje recibido; estado, origen, schema/version, idempotency key, correlation. |
| staging_outbox_message  | Mensaje pendiente de entregar a un consumidor autorizado.                       |
| staging_message_attempt | Intentos, timestamps, errores y respuesta.                                      |
| staging_dead_letter     | Mensaje que no pudo procesarse y requiere atención.                             |
| staging_checkpoint      | Punto de avance para sincronización incremental.                                |
| staging_sync_package    | Paquete de intercambio.                                                         |
| staging_sync_item       | Detalle de contenido del paquete.                                               |

**Origen → Inbox/Outbox → Autenticación → Validación → Idempotencia → Procesamiento → ACK → Checkpoint**

- El mensaje no es el dato de negocio; transporta una instrucción/evento/resultado.

- Debe existir una clave de idempotencia para evitar doble procesamiento.

- Los mensajes deben poder rastrearse por correlation_id.

- Los errores deben conservarse sin bloquear indefinidamente el resto del intercambio.

- El sistema debe permitir recuperación/reproceso controlado de mensajes fallidos.

# 16. Idempotencia, concurrencia y versionamiento

| **Control**            | **Regla**                                                                                        |
|------------------------|--------------------------------------------------------------------------------------------------|
| Idempotencia           | Una misma operación externa recibida varias veces produce como máximo un efecto lógico.          |
| Versión de formulario  | Una tarea conserva la versión exacta utilizada; nunca se sustituye silenciosamente.              |
| Dataset                | La población publicada queda identificada y trazable.                                            |
| Optimistic concurrency | Cambios concurrentes sobre entidades editables deben detectar versión/updated_at cuando aplique. |
| Estados                | Las transiciones inválidas deben rechazarse.                                                     |
| Historial              | Los cambios relevantes conservan actor, fecha, origen y contexto.                                |
| Integraciones          | Cada mensaje tiene schema/version y origen identificable.                                        |

# 17. Knowledge

Knowledge es el motor de comprensión empresarial. Su modelo visible es Contexto → Regla → Indicador → Situación → Recomendación.

| **Entidad**              | **Propósito**                                                                                                  |
|--------------------------|----------------------------------------------------------------------------------------------------------------|
| knowledge_rule           | Regla versionada, gobernada y con fuente/estado.                                                               |
| knowledge_rule_version   | Snapshot/version efectiva.                                                                                     |
| indicator_definition     | Definición del indicador y su semántica.                                                                       |
| indicator_result         | Valor calculado para periodo/contexto.                                                                         |
| knowledge_situation      | Situación detectada: normal, desviación, incumplimiento, riesgo, oportunidad, tendencia o datos insuficientes. |
| knowledge_source         | Origen del conocimiento: GANUS, finca, operación, documento, sector, etc.                                      |
| knowledge_evaluation     | Ejecución/evaluación de una regla o condición.                                                                 |
| knowledge_recommendation | Recomendación explicable derivada de una situación.                                                            |

La base de datos conserva definición, resultados y trazabilidad; no debe convertir el LLM en autoridad para modificar directamente entidades transaccionales.

# 18. Advisory

| **Entidad**                  | **Propósito**                                           |
|------------------------------|---------------------------------------------------------|
| advisory_interaction         | Contexto de una consulta/solicitud.                     |
| advisory_response            | Respuesta generada y sus referencias.                   |
| advisory_recommendation_link | Relación entre recomendación y situación/datos.         |
| advisory_action_proposal     | Acción propuesta para pasar a Planning, cuando aplique. |

Advisory no es dueño de Task. Una acción aceptada se convierte en trabajo mediante Planning.

# 19. Indicadores y control

| **Entidad**                      | **Propósito**                                                                                    |
|----------------------------------|--------------------------------------------------------------------------------------------------|
| indicator_definition             | Nombre, fórmula/semántica, unidad, periodicidad, alcance y fuente.                               |
| indicator_result                 | Valor calculado, periodo, contexto, estado de calidad.                                           |
| indicator_dimension_value        | Dimensiones que permiten drill-down.                                                             |
| operational_aggregate (opcional) | Proyección de lectura/consulta cuando sea necesaria por rendimiento; nunca fuente transaccional. |

Las familias funcionales de indicadores incluyen propios, recomendados, comportamiento y referencia. El cálculo crítico debe ser determinista.

# 20. Situaciones, alertas y notificaciones

| **Entidad**           | **Propósito**                               |
|-----------------------|---------------------------------------------|
| situation             | Hecho interpretado por Knowledge.           |
| alert                 | Situación priorizada que requiere atención. |
| notification          | Entrega de información a un destinatario.   |
| notification_delivery | Estado de entrega/lectura.                  |
| alert_task_link       | Relación entre alerta y tarea originada.    |

Una alerta no es una tarea. Una alerta puede originar una tarea y conservar el vínculo de trazabilidad.

# 21. Archivos y evidencias

| **Entidad**       | **Propósito**                                                                        |
|-------------------|--------------------------------------------------------------------------------------|
| file              | Metadata del archivo: ID, nombre, tipo, tamaño, checksum, storage reference, estado. |
| file_link         | Relación genérica controlada con entidad de dominio.                                 |
| file_access_event | Trazabilidad de acceso cuando aplique.                                               |

- PostgreSQL conserva metadata; el contenido pesado se almacena en el mecanismo de almacenamiento definido por Arquitectura.

- Fotos, videos, documentos, notas de voz y firmas pueden ser evidencias de resultados, activos, tareas o alertas.

- El acceso a un archivo siempre se valida por autorización y contexto.

- El checksum/hash puede utilizarse para detectar corrupción o duplicados según estrategia técnica.

# 22. Auditoría

| **Entidad** | **Campos mínimos**                                                                                                                        |
|-------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| audit_event | audit_id, timestamp, user_id, farm/context, action, entity_type, entity_id, source, correlation_id, before/after reference, result/error. |

La auditoría transversal registra eventos relevantes de seguridad y operación. Los historiales de dominio permanecen bajo ownership del dominio.

# 23. Servicios transversales

| **Servicio**       | **Persistencia de referencia**      |
|--------------------|-------------------------------------|
| Session/Context    | user_session, user_context_session  |
| Preferences        | user_preference                     |
| Notifications      | notification, notification_delivery |
| Audit              | audit_event                         |
| Files              | file, file_link                     |
| Import             | import_job, import_row_error        |
| Export             | export_job                          |
| Integration status | system_integration_status           |
| Sync               | sync/checkpoint structures          |

No crear una tabla 'shared_catalog' universal para absorber catálogos de negocio. Cada catálogo debe pertenecer al dominio que lo gobierna.

# 24. Importación y migración masiva

**Archivo → Mapping → Normalización → Validación → Preview → Confirmación → Carga → Resultado / errores**

| **Entidad**      | **Propósito**                                         |
|------------------|-------------------------------------------------------|
| import_job       | Cabecera del proceso de importación.                  |
| import_mapping   | Mapeo de columnas/atributos a estructura destino.     |
| import_row_error | Error por fila/campo.                                 |
| import_result    | Resumen de procesados, válidos, inválidos y cargados. |

Para Inventory, ningún registro que incumpla la estructura publicada debe cargarse como activo válido. Los errores deben ser descargables/consultables.

# 25. Relaciones críticas entre dominios

| **Origen**   | **Relación**      | **Destino**           | **Regla**                                                       |
|--------------|-------------------|-----------------------|-----------------------------------------------------------------|
| Organization | farm_id/context   | Inventory             | Todo activo operativo pertenece a una finca.                    |
| Organization | farm_id           | Planning              | Toda tarea opera en contexto de finca.                          |
| Inventory    | asset_id          | Planning              | La tarea referencia activos; Planning no es dueño de identidad. |
| Forms        | form_version_id   | Planning              | La tarea utiliza una versión publicada.                         |
| Planning     | task_id           | Mobile/Result         | Toda ejecución deriva de una tarea.                             |
| Mobile       | result_id/task_id | Control               | Resultados alimentan seguimiento.                               |
| Inventory    | asset_id          | Knowledge             | Knowledge interpreta datos del activo.                          |
| Knowledge    | recommendation_id | Planning              | Recomendación aceptada puede originar trabajo.                  |
| Control      | situation/alert   | Planning              | Situación/alerta puede originar tarea.                          |
| Transversal  | file_id           | Any authorized domain | Archivo se vincula mediante metadata y autorización.            |

# 26. Reglas de integridad

- Una PK identifica una sola entidad.

- Un identificador activo de un tipo que requiera unicidad no puede resolver a dos activos.

- Una tarea publicada debe tener formulario/versionado y población definida según reglas.

- Una versión publicada de formulario no puede mutarse.

- Un resultado de activo debe apuntar a una tarea válida y a un AssetID cuando la tarea sea sobre activos.

- Una transición de estado debe ser válida para el estado anterior.

- Un usuario debe operar dentro de un contexto autorizado.

- Una relación de espacio debe pertenecer a la misma finca o seguir una regla explícita de referencia.

- Una relación padre/hijo de activo no debe crear ciclos cuando la relación sea jerárquica.

- Un mensaje idempotente no puede producir efectos duplicados.

- Los registros históricos críticos no deben eliminarse físicamente.

- Los documentos/evidencias deben conservar relación con su origen.

# 27. Índices y rendimiento

La selección final de índices se valida con cargas reales, pero el modelo debe contemplar desde el inicio las rutas de consulta principales.

| **Entidad**      | **Índices/rutas recomendadas**                                       |
|------------------|----------------------------------------------------------------------|
| asset            | farm_id + status; template/type; generated_code; created_at.         |
| asset_identifier | normalized_value + identifier_type; asset_id.                        |
| asset_location   | farm/space + valid_to; asset_id.                                     |
| asset_relation   | source_asset_id; target_asset_id; relation_type.                     |
| task             | farm_id + state + scheduled_at; assignee + state; created_at.        |
| task_target      | task_id + asset_id; task_id + status.                                |
| form_version     | form_id + version_no/status.                                         |
| form_field       | form_version_id + order.                                             |
| result           | task_id; asset_id; captured_at; user_id.                             |
| staging          | status + created_at; idempotency_key; correlation_id.                |
| audit            | farm/context + timestamp; entity_type + entity_id; user + timestamp. |
| notification     | recipient + read/status + created_at.                                |

- Evitar índices excesivos que encarezcan escrituras.

- Usar paginación en listados.

- Evaluar índices parciales para estados activos cuando el volumen lo justifique.

- Medir consultas reales antes de introducir caches o estructuras duplicadas.

# 28. Seguridad de BD

| **Control**     | **Aplicación**                                                           |
|-----------------|--------------------------------------------------------------------------|
| Acceso          | Solo servicios autorizados; no acceso directo desde Web/Mobile.          |
| Privilegios     | Usuarios técnicos con permisos mínimos.                                  |
| Secretos        | Fuera del repositorio/código.                                            |
| Cifrado         | En tránsito y protección de backups según arquitectura.                  |
| Backups         | Frecuencia, retención y prueba de restauración definidos operativamente. |
| Integridad      | FK, unique, check constraints y transacciones donde corresponda.         |
| Auditoría       | Cambios administrativos/críticos trazables.                              |
| Aislamiento     | Consultas siempre limitadas por contexto autorizado.                     |
| Datos sensibles | No exponer en logs o errores.                                            |

# 29. Seguridad de intercambio persistido

| **Control**     | **Persistencia**                                        |
|-----------------|---------------------------------------------------------|
| Idempotency key | staging_inbox/outbox y operación relacionada.           |
| Correlation ID  | Mensaje + resultados derivados.                         |
| Schema version  | Cada mensaje/paquete.                                   |
| Origin          | Sistema/dispositivo que produce el mensaje.             |
| Attempts        | Historial de reintentos.                                |
| Dead letter     | Mensajes no procesables.                                |
| Checkpoint      | Último punto confirmado.                                |
| ACK             | Confirmación de recepción/procesamiento según contrato. |

# 30. Histórico, retención y baja lógica

- Asset y entidades maestras críticas no se eliminan físicamente durante operación normal.

- Los documentos/evidencias deben conservarse según política de negocio y almacenamiento.

- Los mensajes de intercambio deben tener política de retención diferenciada del dato de negocio.

- Los logs técnicos no sustituyen la auditoría funcional.

- Los históricos deben conservar suficiente información para reconstruir el estado en el tiempo.

- Las políticas concretas de retención legal, costos y archivado se definirán fuera de este MVP si no están cerradas funcionalmente.

# 31. Modelo de relación de alto nivel

> GROUP (opcional)  
> 1  
> │  
> N  
> FARM ───────────── USER  
> │ │  
> │ N  
> │ │  
> ├── SPACE ──\< SPACE  
> │  
> ├── ASSET ──\< IDENTIFIER  
> │ │  
> │ ├──\< ATTRIBUTE  
> │ ├──\< RELATION \>── ASSET  
> │ ├──\< LOCATION \>── SPACE  
> │ ├──\< HISTORY  
> │ └──\< FILE  
> │  
> └── TASK ──\< TARGET \>── ASSET  
> │  
> ├── FORM_VERSION  
> ├── ASSIGNMENT \>── USER  
> ├── STATE_HISTORY  
> └── ASSET_RESULT  
> │  
> ├──\< VALUE  
> └──\< EVIDENCE  
>   
> FORM ──\< FORM_VERSION ──\< SECTION  
> └──\< FIELD ──\< VALIDATION  
> └──\< CATALOG_OPTION  
>   
> STAGING ── INBOX / OUTBOX / PACKAGE / CHECKPOINT / DEAD_LETTER  
>   
> KNOWLEDGE ── RULE / INDICATOR / SITUATION / RECOMMENDATION  
> │  
> └────────→ PLANNING / TASK  
>   
> TRANSVERSAL ── SESSION / NOTIFICATION / AUDIT / FILE / IMPORT / EXPORT

# 32. Entidades principales — diccionario resumido

| **Entidad**                | **Descripción**                   | **Owner**                    |
|----------------------------|-----------------------------------|------------------------------|
| group                      | Agrupación empresarial opcional.  | Organization                 |
| farm                       | Finca/contexto principal.         | Organization                 |
| user                       | Usuario autenticado de GANUS.     | Organization/Identity        |
| role                       | Rol funcional.                    | Organization                 |
| permission                 | Permiso de acción.                | Organization                 |
| space                      | Espacio físico jerárquico.        | Organization                 |
| process                    | Proceso.                          | Organization                 |
| activity_type              | Tipo de actividad.                | Organization                 |
| asset                      | Activo digital único.             | Inventory                    |
| asset_identifier           | Identificador físico/lógico.      | Inventory                    |
| asset_relation             | Relación entre activos.           | Inventory                    |
| asset_history              | Historial del activo.             | Inventory                    |
| lifecycle_definition       | Definición de ciclo.              | Inventory/Knowledge boundary |
| lifecycle_stage            | Etapa.                            | Inventory/Knowledge boundary |
| asset_lifecycle_assignment | Ciclo aplicado al activo.         | Inventory                    |
| form                       | Definición estable de formulario. | Forms                        |
| form_version               | Versión inmutable publicada.      | Forms                        |
| form_field                 | Campo de captura.                 | Forms                        |
| task                       | Unidad de trabajo.                | Planning                     |
| task_target                | Población objetivo.               | Planning                     |
| task_asset_result          | Resultado por activo.             | Planning/Execution           |
| sync_package               | Paquete de intercambio.           | Staging                      |
| inbox_message              | Mensaje recibido.                 | Staging                      |
| outbox_message             | Mensaje por entregar.             | Staging                      |
| knowledge_rule             | Regla gobernada.                  | Knowledge                    |
| indicator_definition       | Definición de indicador.          | Knowledge                    |
| indicator_result           | Resultado de indicador.           | Knowledge                    |
| situation                  | Situación detectada.              | Knowledge                    |
| recommendation             | Recomendación.                    | Knowledge/Advisory           |
| notification               | Notificación.                     | Transversal                  |
| audit_event                | Auditoría transversal.            | Transversal                  |
| file                       | Metadata de archivo.              | Transversal                  |
| import_job                 | Proceso de importación.           | Transversal                  |

# 33. Lo que NO debe hacerse en la BD

- No crear una tabla gigante universal para todos los módulos.

- No duplicar Asset como Animal, Bovino, Buffalo, etc. como fuentes separadas.

- No permitir que Planning tenga una copia editable de la identidad del activo.

- No guardar todo el formulario como JSON sin estructura consultable.

- No guardar fotos/videos pesados directamente en tablas transaccionales si la arquitectura dispone almacenamiento de objetos.

- No usar texto libre para estados que requieran transición controlada.

- No usar nombres visibles como claves foráneas principales.

- No usar la BD de staging como fuente maestra.

- No permitir que un LLM escriba SQL arbitrario contra producción.

- No crear tablas para funcionalidades futuras no incluidas en el MVP solo porque podrían ser útiles.

- No introducir una BD adicional para resolver un problema que PostgreSQL puede resolver adecuadamente.

- No duplicar catálogos de negocio en una capa 'shared' sin owner.

# 34. Casos de consistencia críticos

| **Caso**                    | **Debe garantizarse**                                                              |
|-----------------------------|------------------------------------------------------------------------------------|
| Crear animal/activo         | AssetID único, código generado, identificadores válidos, finca y template/version. |
| Cambiar identificador       | Nuevo valor único; historial; resolución correcta al mismo AssetID.                |
| Publicar formulario         | Version inmutable y consumible.                                                    |
| Crear tarea                 | Formulario publicado + dataset + contexto + responsable según reglas.              |
| Publicar tarea              | Dataset y versión quedan trazables.                                                |
| Ejecutar offline            | Resultado persistido localmente y preparado para sync.                             |
| Sincronizar dos veces       | No duplica resultado/alerta.                                                       |
| Cancelar un activo de tarea | Solo ese resultado cambia; otros continúan.                                        |
| Cerrar tarea                | Todos los resultados tienen estado compatible con cierre.                          |
| Generar alerta              | Origen, contexto y prioridad conservados.                                          |
| Advisory → Planning         | La recomendación queda vinculada a la tarea creada.                                |
| Cambio de etapa             | Transición histórica con fecha, fuente y regla/evento.                             |

# 35. Reglas para el DDL posterior

1.  Crear migrations versionadas; no depender de auto-sync en producción.

2.  Crear primero estructuras maestras y después dependencias.

3.  Agregar PK, FK, UNIQUE y CHECK donde la regla sea estructural.

4.  Definir índices según consultas reales.

5.  Usar transacciones para operaciones multi-entidad que deban ser atómicas.

6.  Evitar triggers complejos para lógica de negocio; la lógica de dominio permanece en la aplicación salvo controles de integridad apropiados.

7.  Documentar cada migration.

8.  Probar migración hacia adelante y estrategia de recuperación.

9.  Separar datos de configuración, transacción, intercambio y auditoría cuando el ownership lo requiera.

10. Validar cargas y planes de consulta antes de cerrar índices.

# 36. Criterios de aceptación del modelo

- Cada entidad principal tiene un owner único.

- AssetID es la identidad transversal y todos sus identificadores resuelven al mismo activo.

- Una finca puede existir con o sin grupo empresarial.

- Los datos están correctamente acotados por contexto/finca.

- Una plantilla publicada tiene versión inmutable.

- Una tarea referencia la versión exacta del formulario utilizada.

- La población objetivo es persistida de manera trazable.

- Existe resultado individual por activo cuando la tarea trabaja sobre activos.

- Mobile puede sincronizar de forma incremental.

- Los mensajes repetidos no generan duplicados.

- Los errores de intercambio pueden reintentarse/reprocesarse.

- Knowledge puede rastrear regla → indicador → situación → recomendación.

- Advisory puede entregar una acción a Planning sin apropiarse de Task.

- Auditoría puede reconstruir operaciones críticas.

- Los archivos están vinculados y protegidos.

- El modelo permite paginación, índices y consultas por contexto.

- No existe dependencia de acceso directo entre módulos.

- El modelo no introduce funcionalidades fuera del MVP.

# 37. Trazabilidad funcional → persistencia

| **Necesidad**         | **Entidades principales**                                                   |
|-----------------------|-----------------------------------------------------------------------------|
| Gobierno y acceso     | group, farm, user, role, permission, assignments, session                   |
| Identidad de activo   | asset, asset_identifier, asset_relation, asset_attribute_value              |
| Ciclo de vida         | lifecycle_definition/version/stage, asset_lifecycle_assignment, transition  |
| Definición de captura | form, form_version, section, field, validation, key, catalog                |
| Planificación         | task, target, assignment, schedule, condition, state_history                |
| Ejecución             | task_asset_result, form_execution_result/value/evidence, exception          |
| Offline/intercambio   | sync_package, inbox/outbox, checkpoint, attempt, dead_letter                |
| Control               | indicator_definition/result, situation, alert, notification                 |
| Knowledge             | rule/version, evaluation, source, recommendation                            |
| Advisory              | interaction, response, recommendation/action proposal                       |
| Transversal           | audit_event, file/file_link, import/export, preferences, integration status |

# 38. Decisiones que quedan explícitamente cerradas

- PostgreSQL es la referencia de persistencia transaccional del MVP conforme a la Arquitectura oficial.

- No se crean microservicios/bases independientes por dominio en este modelo inicial.

- El ownership lógico por dominio es obligatorio aunque exista una BD física común.

- Inventory es maestro de identidad del activo.

- Forms es maestro de la definición/versionamiento de captura.

- Planning es maestro de tareas, población y ejecución planificada.

- Mobile es canal de ejecución offline; no es maestro de identidad.

- STAGING es frontera de intercambio; no es maestro de negocio.

- Knowledge es dueño de reglas/indicadores/situaciones/recomendaciones gobernadas.

- Advisory interpreta y propone; Planning ejecuta el trabajo.

- Los archivos pesados no deben convertirse en blobs transaccionales por defecto.

- El modelo debe priorizar trazabilidad, seguridad, rendimiento y conectividad rural.

# 39. Pendientes para el artefacto DDL

Estos puntos no son vacíos del modelo; son decisiones de implementación que deben resolverse en el DDL y migrations.

| **Tema**           | **Decisión requerida en DDL**                                                  |
|--------------------|--------------------------------------------------------------------------------|
| Tipo exacto de PK  | UUID/estrategia aprobada por Arquitectura.                                     |
| Enums vs catálogo  | Según necesidad de evolución de cada estado.                                   |
| JSONB              | Campos permitidos y límites.                                                   |
| Índices compuestos | Confirmar con consultas reales.                                                |
| Particionamiento   | Solo si volumen/retención lo justifica.                                        |
| RLS                | Definir si se usa como defensa adicional al control de contexto de aplicación. |
| Storage            | Proveedor/configuración concreta de object storage.                            |
| Retención          | Políticas legales/operativas definitivas.                                      |
| Outbox/inbox       | Implementación exacta y esquema de reintentos.                                 |
| Migrations         | Secuencia inicial y estrategia de rollback/recovery.                           |

# 40. Regla final del modelo

**La base de datos de GANUS debe representar la realidad empresarial y operacional sin convertirse en la arquitectura del producto. Cada dato debe tener un dueño, cada relación debe tener un propósito, cada cambio importante debe ser trazable y cada intercambio debe poder procesarse de forma segura e idempotente. La BD soporta la experiencia simple de GANUS; no debe imponerle complejidad al usuario.**

Documento 10 · Modelo BD Referencia MVP GANUS · v1.0
