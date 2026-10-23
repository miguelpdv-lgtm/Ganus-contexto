# 08 · Servicios Transversales v1.0

> **Fuente de solo lectura.** Convertido a Markdown desde `08_SERVICIOS_TRANSVERSALES_GANUS_OPERATIVO_v1.0.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**GANUS**

**08 — SERVICIOS TRANSVERSALES GANUS OPERATIVO**

**ESPECIFICACIÓN FUNCIONAL Y DE IMPLEMENTACIÓN — CAPA COMÚN DE LA APLICACIÓN**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>PROPÓSITO<br />
</strong>Definir la capa común que hace que GANUS OPERATIVO sea una aplicación empresarial completa y coherente. Estos servicios no pertenecen a un dominio de negocio concreto, pero son necesarios para que Organización, Inventario, Formularios, Planificación, Mobile, Seguimiento y Knowledge funcionen como un solo producto.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

Versión 1.0 · 08 octubre 2026

*Principio rector: GANUS debe sentirse como una sola aplicación, no como un conjunto de módulos pegados.*

# 0. CONTROL DEL DOCUMENTO

| **Elemento** | **Definición**                                                                                                                     |
|--------------|------------------------------------------------------------------------------------------------------------------------------------|
| Documento    | 08 — Servicios Transversales GANUS Operativo                                                                                       |
| Producto     | GANUS OPERATIVO                                                                                                                    |
| Versión      | 1.0                                                                                                                                |
| Estado       | Especificación base para desarrollo                                                                                                |
| Depende de   | 00 Cerebro; 01 Arquitectura; 02 Organización; 03 Inventario; 04 Formularios; 05 Planificación; 06 Mobile; 07 Seguimiento/Knowledge |
| Backend      | NestJS + TypeScript                                                                                                                |
| Frontend     | Angular modular                                                                                                                    |
| Persistencia | PostgreSQL; ownership por dominio                                                                                                  |
| Alcance      | Web transversal + capacidades comunes de backend. Mobile conserva sus capacidades propias offline.                                 |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>REGLA DE ARQUITECTURA<br />
</strong>Los servicios transversales son capacidades compartidas. No deben convertirse en un nuevo 'mega-módulo' que termine siendo dueño de datos de negocio de otros dominios.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 1. POR QUÉ EXISTE ESTE DOCUMENTO

Los documentos 02–07 definen capacidades de negocio. Sin una capa transversal, cada módulo terminaría resolviendo por separado navegación, permisos, búsqueda, notificaciones, auditoría, archivos, estados, errores, configuración y experiencia de usuario. Eso produciría una aplicación inconsistente y difícil de mantener.

La capa transversal debe resolver una sola vez lo que todos los dominios necesitan de la misma manera.

> GANUS OPERATIVO  
> ┌───────────────────────────────────────────────┐  
> │ APPLICATION SHELL + SERVICIOS TRANSVERSALES │  
> │ │  
> │ navegación · búsqueda · notificaciones │  
> │ auditoría · archivos · configuración │  
> │ import/export · estados · errores · UX │  
> ├───────────────────────────────────────────────┤  
> │ Organization │ Inventory │ Forms │ Planning │  
> │ Mobile │ Control │ Knowledge │  
> └───────────────────────────────────────────────┘  
> STAGING / CONTRATOS / API

# 2. PRINCIPIO DE FRONTERA

| **Es transversal**                      | **NO es transversal**    |
|-----------------------------------------|--------------------------|
| Shell y layout de aplicación            | Identidad del animal     |
| Sesión y contexto activo                | Reglas de ciclo de vida  |
| Navegación                              | Dataset de una tarea     |
| Búsqueda global                         | Definición de formulario |
| Notificaciones                          | Cálculo de KPI           |
| Auditoría                               | Programación de tareas   |
| Archivos/evidencias como servicio común | Lógica de Inventario     |
| Importación/exportación                 | Lógica de Planning       |
| Estados UI y manejo de errores          | Reglas de Knowledge      |
| Preferencias de usuario                 | Resultados de Mobile     |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>REGLA<br />
</strong>Si una capacidad tiene una regla de negocio específica de un dominio, pertenece al dominio. Si resuelve una necesidad común de varios dominios sin apropiarse de sus datos, puede ser transversal.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 3. SERVICIOS TRANSVERSALES OFICIALES

| **ID** | **Servicio**                    | **Responsabilidad**                                          |
|--------|---------------------------------|--------------------------------------------------------------|
| TS-01  | Application Shell               | Marco común de la aplicación web.                            |
| TS-02  | Identity / Session / Context    | Sesión, usuario, finca/contexto activo y permisos efectivos. |
| TS-03  | Navigation                      | Menú, rutas, breadcrumbs, acciones contextuales.             |
| TS-04  | Global Search                   | Búsqueda transversal sobre entidades autorizadas.            |
| TS-05  | Notifications                   | Centro de notificaciones y entrega de eventos de atención.   |
| TS-06  | Audit / History                 | Trazabilidad común de acciones y cambios.                    |
| TS-07  | Files / Evidence                | Servicio común de archivos, evidencias y adjuntos.           |
| TS-08  | Shared Configuration / Catalogs | Configuraciones comunes y catálogos técnicos compartidos.    |
| TS-09  | Import / Export                 | Carga masiva, plantillas, validación y exportación.          |
| TS-10  | UX States / Errors              | Estados, validaciones, errores, vacíos, confirmaciones.      |
| TS-11  | Sync / Connectivity Status      | Estado de sincronización e integración visible.              |
| TS-12  | User Preferences                | Preferencias no funcionales del usuario.                     |
| TS-13  | Help / Contextual Guidance      | Ayuda contextual y soporte de uso.                           |

# 4. APPLICATION SHELL

Es la estructura visual persistente de la aplicación web. Todos los módulos se renderizan dentro del mismo Shell.

> ┌──────────────────────────────────────────────────────────────┐  
> │ HEADER: logo \| contexto finca \| búsqueda \| alertas \| usuario │  
> ├───────────────┬──────────────────────────────────────────────┤  
> │ NAVIGATION │ │  
> │ │ CONTENT AREA │  
> │ Dashboard │ │  
> │ Operación │ módulo/pantalla actual │  
> │ Inventario │ │  
> │ Formularios │ │  
> │ Planificación │ │  
> │ Seguimiento │ │  
> │ Knowledge │ │  
> │ Configuración │ │  
> └───────────────┴──────────────────────────────────────────────┘

- El Shell conoce sesión, contexto, navegación, notificaciones y preferencias.

- El Shell NO conoce la lógica de Inventario, Planning o Knowledge.

- El menú visible depende de permisos.

- El contexto activo debe estar visible.

- Los cambios de contexto requieren confirmación cuando puedan afectar la lectura o acción actual.

- El Shell debe ser responsive para escritorio y tablet web.

- Las rutas deben ser profundas/directamente navegables cuando el permiso lo permita.

# 5. IDENTIDAD, SESIÓN Y CONTEXTO ACTIVO

Organization define usuario, rol, permiso y relación con finca. El servicio transversal aplica esa autorización a cada solicitud; no redefine el modelo de roles.

| **Concepto**     | **Regla**                                                            |
|------------------|----------------------------------------------------------------------|
| Usuario          | Identidad autenticada.                                               |
| Sesión           | Estado temporal de autenticación y contexto.                         |
| Finca activa     | Finca sobre la que se está trabajando.                               |
| Rol efectivo     | Rol asignado al usuario para el contexto.                            |
| Permiso efectivo | Acciones autorizadas en ese contexto.                                |
| Contexto activo  | Combinación de usuario + finca + permisos + preferencias relevantes. |

## 5.1 Inicio de sesión

1.  Autenticar usuario.

2.  Resolver fincas disponibles.

3.  Resolver roles/permisos.

4.  Seleccionar contexto activo.

5.  Cargar configuración mínima.

6.  Registrar inicio de sesión.

7.  Mostrar aplicación.

## 5.2 Cambio de finca/contexto

- Mostrar únicamente contextos autorizados.

- Al cambiar, invalidar datos de pantalla dependientes del contexto.

- Recalcular navegación/acciones si los permisos cambian.

- No mezclar resultados entre fincas.

- Registrar cambio de contexto en auditoría cuando sea relevante.

# 6. NAVEGACIÓN

| **Elemento**         | **Comportamiento**                                               |
|----------------------|------------------------------------------------------------------|
| Menú principal       | Agrupa capacidades por lenguaje de negocio.                      |
| Submenú              | Permite acceder a funciones autorizadas.                         |
| Breadcrumb           | Muestra dónde está el usuario y permite regresar.                |
| Acciones de contexto | Acciones sobre el objeto actual.                                 |
| Back                 | Regresa sin perder contexto cuando sea posible.                  |
| Deep link            | Permite abrir directamente un activo/tarea/situación autorizado. |
| Recent               | Puede mostrar elementos recientes sin crear un nuevo dominio.    |
| Favorites            | Opcional en MVP; si se implementa, es preferencia de usuario.    |

Orden conceptual del menú MVP:

> INICIO / CENTRO DE CONTROL  
> OPERACIÓN  
> ├─ Inventario  
> ├─ Formularios  
> ├─ Planificación  
> SEGUIMIENTO  
> ├─ Centro de Control  
> ├─ Situaciones / Alertas  
> INTELIGENCIA  
> ├─ Knowledge  
> └─ Advisory  
> CONFIGURACIÓN  
> └─ Organización / configuración autorizada

El menú es una representación de permisos y producto; no define ownership de dominio.

# 7. BÚSQUEDA GLOBAL

Debe permitir encontrar rápidamente información sin obligar al usuario a navegar por cada módulo.

| **Debe buscar** | **Ejemplos**                                  |
|-----------------|-----------------------------------------------|
| Activos         | ID GANUS, RFID, nombre/código, código antiguo |
| Tareas          | TaskID, nombre, estado, fecha                 |
| Formularios     | nombre, versión, estado                       |
| Espacios        | finca, sector, lote, potrero, corral, etc.    |
| Situaciones     | título, tipo, estado                          |
| Documentos      | nombre, tipo, referencia                      |
| Usuarios        | nombre cuando el permiso lo permita           |

- Los resultados se filtran por permisos y contexto.

- Buscar por texto libre y claves conocidas.

- Mostrar tipo de entidad y contexto.

- Permitir navegación al detalle.

- No realizar búsquedas indiscriminadas sobre datos que el usuario no puede consultar.

- El ranking puede priorizar coincidencia exacta de identificadores.

> GET /api/search?q=...  
> Resultado:  
> \[  
> {type:"ASSET", id:"...", label:"...", context:"..."},  
> {type:"TASK", id:"...", label:"...", context:"..."}  
> \]

# 8. NOTIFICACIONES

Servicio común para entregar situaciones, eventos y comunicaciones que requieren atención. La lógica que decide por qué existe una alerta pertenece al dominio origen.

| **Tipo**       | **Origen típico** | **Ejemplo**                          |
|----------------|-------------------|--------------------------------------|
| Operativa      | Planning/Mobile   | Tarea bloqueada / alerta de campo    |
| Conocimiento   | Knowledge         | Situación prioritaria                |
| Sistema        | Plataforma        | Error de integración                 |
| Administrativa | Organization      | Cambio relevante de usuario/permisos |
| Sincronización | STAGING/Mobile    | Error de sincronización              |

## 8.1 Estado

> CREATED → DELIVERED → READ  
> ↘ ACKNOWLEDGED (cuando aplique)  
> ↘ DISMISSED (cuando aplique)

- Una notificación no reemplaza la entidad origen.

- Debe conservar sourceType + sourceId.

- Puede agruparse cuando existan múltiples eventos similares.

- Debe respetar permisos.

- El usuario puede marcar como leída.

- Las alertas críticas no deben perderse por agrupación.

# 9. AUDITORÍA Y TRAZABILIDAD

Auditoría responde quién hizo qué, cuándo, sobre qué objeto y desde qué contexto.

| **Campo**          | **Descripción**                                                                     |
|--------------------|-------------------------------------------------------------------------------------|
| AuditID            | Identificador                                                                       |
| Timestamp          | Fecha/hora                                                                          |
| UserID             | Usuario                                                                             |
| FarmID / ContextID | Contexto                                                                            |
| Action             | CREATE / UPDATE / DELETE-LOGICAL / ACTIVATE / DEACTIVATE / PUBLISH / CONFIRM / etc. |
| EntityType         | Tipo de objeto                                                                      |
| EntityID           | Objeto afectado                                                                     |
| Before             | Estado anterior cuando aplique                                                      |
| After              | Estado posterior cuando aplique                                                     |
| Source             | Web / Mobile / API / Integration / AI                                               |
| CorrelationID      | Trazabilidad transversal                                                            |

- No permitir borrado físico de registros de auditoría desde la aplicación.

- Auditoría no sustituye historial de dominio.

- Cada dominio conserva su historial funcional; el servicio transversal conserva el evento común.

- Las acciones de IA relevantes deben registrar modelo/versión de interacción cuando corresponda.

- Las acciones administrativas deben ser auditables.

# 10. ARCHIVOS Y EVIDENCIAS

Servicio común para almacenar y relacionar archivos; el significado funcional de cada archivo sigue perteneciendo al dominio.

| **Tipo**           | **Ejemplos**                      |
|--------------------|-----------------------------------|
| Identificación     | Foto de chapeta, placa, marca     |
| Evidencia de campo | Foto/video de ejecución           |
| Documento          | Certificado, factura, soporte     |
| Resultado          | Documento producido por actividad |
| Knowledge          | Documento fuente para análisis    |
| Configuración      | Plantilla/archivo de importación  |

## 10.1 Contrato mínimo

> File {  
> id, name, mimeType, size, storageKey,  
> checksum, uploadedBy, uploadedAt,  
> contextId, entityType, entityId,  
> category, metadata, status  
> }

- Usar storage externo/objeto según arquitectura; PostgreSQL conserva metadatos.

- Validar extensión/MIME/tamaño.

- Generar identificador único.

- Evitar duplicados cuando checksum permita detectarlos.

- Respetar permisos del objeto padre.

- Permitir descarga/visualización solo autorizada.

- Conservar vínculo con entidad origen.

- El servicio no decide si un archivo es 'foto principal', 'evidencia' o 'documento de identidad'; recibe category desde el dominio.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>LÍMITE IMPORTANTE<br />
</strong>La clasificación funcional de una foto de Inventario o una evidencia de Mobile pertenece al dominio que la usa. El servicio transversal solo resuelve almacenamiento, seguridad, metadatos y acceso.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 11. CONFIGURACIÓN Y CATÁLOGOS COMPARTIDOS

No crear un 'catálogo universal' que termine absorbiendo los maestros de negocio.

| **Transversal puede manejar**   | **Debe pertenecer al dominio**  |
|---------------------------------|---------------------------------|
| Formatos técnicos               | Especies como maestro funcional |
| Unidades de medida técnicas     | Tipos de activo                 |
| Idiomas / locale                | Procesos                        |
| Tipos de archivo                | Tipos de actividad              |
| Estados técnicos comunes        | Estados de tarea                |
| Parámetros de plataforma        | Estados de activos              |
| Opciones de UI                  | Catálogos de formularios        |
| Configuraciones de notificación | Reglas de Knowledge             |

Los catálogos de negocio se mantienen en su dominio propietario y se consumen mediante contrato.

# 12. IMPORTACIÓN Y EXPORTACIÓN

Capacidad transversal para mover datos de manera controlada. El dominio define qué se importa y cómo se valida.

> ARCHIVO  
> ↓  
> LECTURA  
> ↓  
> MAPEO DE COLUMNAS  
> ↓  
> NORMALIZACIÓN  
> ↓  
> VALIDACIÓN  
> ↓  
> PREVIO / RESUMEN  
> ↓  
> CONFIRMACIÓN  
> ↓  
> CARGA  
> ↓  
> RESULTADO + ERRORES

| **Paso**     | **Regla**                                           |
|--------------|-----------------------------------------------------|
| Plantilla    | El dominio publica columnas esperadas.              |
| Mapeo        | Usuario relaciona columnas cuando sea necesario.    |
| Validación   | No cargar registros inválidos como válidos.         |
| Preview      | Mostrar cantidad válidos, inválidos y advertencias. |
| Confirmación | Carga masiva requiere confirmación.                 |
| Resultado    | Entregar resumen y archivo/listado de errores.      |
| Trazabilidad | Registrar usuario, archivo, fecha y operación.      |
| Exportación  | Respetar permisos y contexto.                       |

Inventory ya define su carga Excel/CSV; este servicio provee la infraestructura común para que Forms, Planning u otros dominios puedan usar el mismo patrón.

# 13. ESTADOS, VALIDACIONES Y ERRORES

La experiencia debe distinguir estados funcionales de estados técnicos.

| **Categoría**     | **Ejemplos**                      |
|-------------------|-----------------------------------|
| Loading           | Cargando información              |
| Empty             | No hay registros                  |
| Partial           | Información parcial               |
| Offline           | Sin conectividad                  |
| Sync pending      | Cambios pendientes                |
| Success           | Operación completada              |
| Warning           | Operación posible con advertencia |
| Validation error  | Datos incorrectos                 |
| Permission denied | Sin autorización                  |
| Not found         | Objeto no existe/no accesible     |
| Conflict          | Conflicto de versión/dato         |
| System error      | Falla inesperada                  |
| Integration error | Falla con servicio externo        |

## 13.1 Mensajes

- El mensaje debe explicar qué pasó.

- Debe indicar qué puede hacer el usuario.

- No mostrar stack traces al usuario.

- El detalle técnico queda en logs/correlación.

- Errores de validación deben apuntar al campo o elemento.

- Errores de integración deben permitir reintento cuando sea seguro.

- Mensajes deben mantener lenguaje de negocio.

# 14. CONFIRMACIONES Y ACCIONES DE RIESGO

No todas las acciones necesitan confirmación. Deben confirmarse las que puedan producir impacto relevante.

| **Acción**                 | **Confirmación**                   |
|----------------------------|------------------------------------|
| Guardar edición simple     | No necesariamente                  |
| Activar/desactivar regla   | Sí cuando tenga impacto productivo |
| Publicar tarea             | Sí                                 |
| Carga masiva               | Sí                                 |
| Cancelar tarea masiva      | Sí                                 |
| Eliminar lógicamente       | Sí                                 |
| Cambiar contexto/finca     | Cuando pueda perder cambios        |
| Crear tarea desde Advisory | Sí                                 |
| Enviar alerta crítica      | Sí según flujo                     |
| Cambiar permisos           | Sí                                 |

# 15. ESTADO DE SINCRONIZACIÓN Y CONECTIVIDAD

Aunque Mobile tiene su propio motor offline, Web y la plataforma necesitan una representación común del estado de integración.

| **Estado** | **Significado**                     |
|------------|-------------------------------------|
| ONLINE     | Conectividad disponible.            |
| DEGRADED   | Servicio parcialmente disponible.   |
| OFFLINE    | No hay conectividad.                |
| SYNCING    | Intercambio en progreso.            |
| PENDING    | Hay información esperando envío.    |
| ERROR      | Existe error que requiere atención. |
| CONFIRMED  | Intercambio confirmado.             |

- El estado visible no debe bloquear innecesariamente la operación.

- Mobile conserva su cola local; el transversal no reemplaza esa cola.

- El usuario debe poder ver si una acción quedó pendiente.

- Cuando un error requiera intervención, debe existir detalle accionable.

# 16. PREFERENCIAS DEL USUARIO

Solo preferencias de experiencia, no reglas de negocio.

| **Preferencia**   | **Ejemplo**                          |
|-------------------|--------------------------------------|
| Idioma            | Español                              |
| Formato fecha     | DD/MM/YYYY                           |
| Formato numérico  | 1.234,56                             |
| Zona horaria      | Contexto de finca/usuario            |
| Vista inicial     | Centro de Control / Operación        |
| Filtros recientes | Preferencias de consulta             |
| Notificaciones    | Canales/agrupación permitidos        |
| Densidad visual   | Compacta/normal cuando se implemente |

No guardar en preferencias criterios que deban ser compartidos por toda la finca; esos pertenecen a configuración/dominio.

# 17. AYUDA Y GUÍA CONTEXTUAL

- Tooltips para conceptos no obvios.

- Texto de ayuda junto a campos complejos.

- Mensajes de primer uso.

- Explicaciones breves de indicadores.

- Enlaces a documentación cuando exista.

- En pantallas vacías, explicar qué debe hacer el usuario.

- No crear un módulo de capacitación completo en MVP.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>PRINCIPIO UX<br />
</strong>La ayuda debe aparecer donde surge la duda. No obligar al usuario a salir de la pantalla para entender una acción.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 18. MODELO DE INTERACCIÓN TRANSVERSAL

> USUARIO  
> ↓  
> SHELL  
> ↓  
> CONTEXTO + PERMISOS  
> ↓  
> MÓDULO DE NEGOCIO  
> ↕  
> SERVICIOS TRANSVERSALES  
> ├─ búsqueda  
> ├─ notificaciones  
> ├─ auditoría  
> ├─ archivos  
> ├─ import/export  
> ├─ UX/errores  
> └─ preferencias  
> ↓  
> API / STAGING / INTEGRACIONES

El servicio transversal no debe saltarse los casos de uso del dominio.

# 19. API / CONTRATOS FUNCIONALES

Los nombres siguientes son referenciales; Documento 09 fijará contratos técnicos definitivos.

| **Servicio**  | **Endpoints/casos de uso orientativos**                 |
|---------------|---------------------------------------------------------|
| Session       | login, refresh, logout, current-context, switch-context |
| Navigation    | get-navigation                                          |
| Search        | global-search                                           |
| Notifications | list, unread-count, read, acknowledge, dismiss          |
| Audit         | query-audit (solo autorizados)                          |
| Files         | upload, metadata, download-url, delete-logical          |
| Import        | create-job, preview, validate, confirm, result          |
| Export        | create-export, status, download                         |
| Preferences   | get, update                                             |
| System status | health-summary, integration-status                      |

# 20. EVENTOS TRANSVERSALES

- USER_LOGIN

- USER_LOGOUT

- CONTEXT_CHANGED

- NOTIFICATION_CREATED

- NOTIFICATION_READ

- AUDIT_RECORDED

- FILE_UPLOADED

- FILE_DELETED_LOGICAL

- IMPORT_STARTED

- IMPORT_VALIDATED

- IMPORT_COMPLETED

- IMPORT_FAILED

- EXPORT_CREATED

- SYNC_STATUS_CHANGED

- INTEGRATION_ERROR

Estos eventos sirven para trazabilidad e integración. No todos requieren persistencia como entidad de negocio.

# 21. SEGURIDAD

- Autenticación centralizada.

- Autorización por contexto y permisos definidos por Organization.

- Filtrado server-side; nunca confiar solo en ocultar botones.

- Todas las APIs deben validar autorización.

- Archivos protegidos por permisos.

- Búsqueda global filtra por autorización.

- Auditoría protegida contra modificación desde UI.

- Sesiones y tokens gestionados según arquitectura de seguridad.

- Acciones sensibles requieren autorización vigente.

- No exponer IDs o datos de otras fincas por búsquedas globales.

# 22. OBSERVABILIDAD Y CORRELACIÓN

Los servicios transversales deben facilitar la trazabilidad técnica.

| **Dato**         | **Uso**                                                    |
|------------------|------------------------------------------------------------|
| CorrelationID    | Seguir una operación entre frontend/backend/integraciones. |
| RequestID        | Identificar petición.                                      |
| UserID           | Actor.                                                     |
| ContextID/FarmID | Ámbito.                                                    |
| EntityID         | Objeto.                                                    |
| Source           | Web/Mobile/API/Integration.                                |
| Timestamp        | Orden temporal.                                            |
| ErrorCode        | Clasificación técnica.                                     |

Los logs técnicos no deben convertirse en la única fuente de auditoría funcional.

# 23. RESPONSIVE Y ACCESIBILIDAD

- Desktop como experiencia principal para administración/seguimiento.

- Tablet web debe permitir operación cómoda en campo cuando se use navegador.

- No depender únicamente de hover.

- Foco visible y navegación por teclado para funciones administrativas.

- Contraste y tamaños legibles.

- Estados de error no comunicados solo por color.

- Controles táctiles adecuados.

- El diseño visual debe ser minimalista y consistente con GANUS.

# 24. REGLAS DE UX TRANSVERSALES

| **Regla**              | **Aplicación**                                     |
|------------------------|----------------------------------------------------|
| Una acción principal   | Cada pantalla debe tener acción primaria clara.    |
| Contexto visible       | Finca/contexto activo siempre reconocible.         |
| No perder trabajo      | Advertir ante navegación con cambios no guardados. |
| Feedback inmediato     | Toda acción muestra resultado.                     |
| No duplicar controles  | Un patrón común para guardar/cancelar/confirmar.   |
| Lenguaje de negocio    | Evitar lenguaje técnico al usuario.                |
| Progressive disclosure | Mostrar detalle cuando se necesita.                |
| Drill-down             | El resumen debe permitir profundizar.              |
| Consistencia           | Mismo comportamiento entre módulos.                |
| No sobrecargar         | No llenar pantalla con opciones secundarias.       |

# 25. MODELO LÓGICO DE DATOS TRANSVERSAL

> user_session  
> user_context_session  
> user_preference  
> notification  
> notification_delivery  
> audit_event  
> file  
> file_link  
> import_job  
> import_row_error  
> export_job  
> system_integration_status  
>   
> Nota:  
> - No duplicar User, Role, Permission: son responsabilidad de Organization/Identity.  
> - No duplicar Asset, Task, Form, Rule, Indicator: pertenecen a sus dominios.

El Documento 10 definirá modelo físico, constraints, índices, ownership y migraciones.

# 26. BACKLOG TÉCNICO

| **ID** | **Componente**        | **Objetivo**                                         |
|--------|-----------------------|------------------------------------------------------|
| TS-001 | Application Shell     | Layout, header, sidebar, contexto, área de contenido |
| TS-002 | Session Context       | Sesión + finca/contexto activo                       |
| TS-003 | Permission Guard      | Guards frontend/backend                              |
| TS-004 | Navigation Registry   | Menú/rutas según permisos                            |
| TS-005 | Global Search         | Búsqueda autorizada                                  |
| TS-006 | Notification Center   | Inbox, unread, read, priority                        |
| TS-007 | Audit Service         | Eventos funcionales                                  |
| TS-008 | File Service          | Upload/storage/metadata/access                       |
| TS-009 | Import Framework      | Jobs, preview, validation, result                    |
| TS-010 | Export Framework      | Export jobs + permissions                            |
| TS-011 | UX State Kit          | Loading/empty/error/offline/conflict                 |
| TS-012 | Sync Status           | Estado de integración                                |
| TS-013 | User Preferences      | Preferencias UI                                      |
| TS-014 | Context Help          | Ayuda contextual                                     |
| TS-015 | Correlation           | Request/correlation IDs                              |
| TS-016 | Global Error Handler  | Errores normalizados                                 |
| TS-017 | Confirmation Patterns | Acciones de riesgo                                   |
| TS-018 | Responsive Layout     | Desktop/tablet                                       |

# 27. CRITERIOS DE ACEPTACIÓN

**AC-01** Todos los módulos web se ejecutan dentro del mismo Application Shell.

**AC-02** El contexto de finca es visible y se mantiene durante la navegación.

**AC-03** Un usuario no puede consultar datos fuera de su autorización.

**AC-04** El menú cambia según permisos sin duplicar lógica de negocio.

**AC-05** Existe búsqueda global autorizada.

**AC-06** Las notificaciones muestran origen y permiten navegar al objeto correspondiente.

**AC-07** Las acciones relevantes quedan auditadas.

**AC-08** Archivos pueden asociarse a entidades sin duplicar el motor de almacenamiento.

**AC-09** Importaciones muestran preview, errores y resultado antes/después de confirmar.

**AC-10** Exportaciones respetan permisos y contexto.

**AC-11** Los estados de carga, vacío, error y conflicto tienen comportamiento consistente.

**AC-12** El usuario puede saber si una operación quedó pendiente por sincronización.

**AC-13** Preferencias no alteran reglas de negocio.

**AC-14** Las APIs validan autorización server-side.

**AC-15** Existe correlación técnica entre operaciones.

**AC-16** Una recomendación de Advisory puede navegar a Planning sin romper contexto.

**AC-17** Una alerta de Mobile puede terminar en Notification Center/Centro de Control.

**AC-18** El sistema no crea catálogos de negocio duplicados.

**AC-19** Los servicios transversales no se convierten en dueño de entidades de dominio.

**AC-20** La experiencia se percibe como una sola aplicación.

# 28. ESCENARIO E2E DE VALIDACIÓN

> 1\. Usuario inicia sesión.  
> 2. Selecciona Finca A.  
> 3. Shell carga menú según permisos.  
> 4. Busca un bovino por RFID.  
> 5. Abre Inventory.  
> 6. Adjunta foto/evidencia.  
> 7. Acción queda auditada.  
> 8. Regresa al Centro de Control.  
> 9. Recibe una alerta de Knowledge.  
> 10. Abre situación desde Notification Center.  
> 11. Advisory explica y propone una tarea.  
> 12. Usuario acepta.  
> 13. Planning recibe contexto y crea la tarea.  
> 14. Usuario navega entre módulos sin perder la finca activa.  
> 15. Mobile ejecuta la tarea y envía resultado.  
> 16. STAGING informa estado de sincronización.  
> 17. Control refleja resultado.  
> 18. Todo el recorrido puede rastrearse por CorrelationID/AuditID.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>PRUEBA DE PRODUCTO<br />
</strong>Si cada módulo presenta un menú diferente, un buscador diferente, un sistema de archivos diferente o mensajes de error diferentes, la capa transversal está mal implementada.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 29. REGLAS PARA IA DE DESARROLLO

**R01** No implementar un servicio transversal dentro de un dominio por comodidad.

**R02** No crear repositorios transversales que accedan directamente a tablas de todos los dominios.

**R03** Usar contratos/interfaces para integración.

**R04** No duplicar autenticación/autorización en cada módulo.

**R05** No confiar en guards únicamente del frontend.

**R06** No guardar lógica de negocio en componentes Angular.

**R07** No convertir el Shell en un componente con lógica de negocio.

**R08** No crear un 'SharedService' gigante sin responsabilidad clara.

**R09** Cada servicio transversal debe tener una responsabilidad única y observable.

**R10** Las entidades de dominio mantienen ownership.

**R11** Los eventos deben ser idempotentes cuando puedan repetirse.

**R12** Los archivos se almacenan fuera de la base cuando la arquitectura lo determine; DB guarda metadatos.

**R13** Los errores se normalizan en un contrato común.

**R14** Las importaciones son jobs controlables y auditables.

**R15** No bloquear toda la aplicación por un servicio externo no crítico.

**R16** El diseño debe soportar evolución sin microservicios prematuros.

**R17** Antes de agregar una nueva capacidad transversal, demostrar que al menos dos dominios la necesitan de forma común.

# 30. PRUEBAS OBLIGATORIAS

| **Tipo**     | **Validaciones**                                          |
|--------------|-----------------------------------------------------------|
| Unitarias    | Permisos, estados, validadores, normalización de errores  |
| Integración  | Shell + APIs + permisos + notificaciones + archivos       |
| Security     | Aislamiento por finca/rol                                 |
| E2E          | Login → contexto → búsqueda → módulo → acción → auditoría |
| Import       | Preview, validación, errores, confirmación, idempotencia  |
| Export       | Permisos, filtros, archivos                               |
| Files        | MIME, tamaño, permisos, vínculo                           |
| Notification | Creación, lectura, prioridad, origen                      |
| Regression   | Navegación entre todos los módulos                        |
| Responsive   | Desktop/tablet                                            |
| Failure      | Servicios externos caídos/degradados                      |

# 31. QUÉ NO DEBE HACER ESTE DOCUMENTO

- No define la lógica funcional de Inventario.

- No define la lógica funcional de Formularios.

- No define el ciclo de vida de activos.

- No define la programación de tareas.

- No define el cálculo de indicadores.

- No define las reglas de Knowledge.

- No define el modelo físico completo de BD.

- No define contratos técnicos finales de STAGING.

- No reemplaza la arquitectura oficial.

Es la capa común que permite que esas funcionalidades convivan de manera consistente.

# 32. CRITERIO DE CIERRE

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>GANUS COMO PRODUCTO<br />
</strong>GANUS OPERATIVO estará correctamente construido como aplicación empresarial cuando el usuario pueda entrar una sola vez, elegir su contexto, navegar todos los dominios, buscar información transversal, recibir atención/alertas, adjuntar evidencias, importar/exportar datos, entender errores, ver el estado de sincronización y auditar acciones sin sentir que está cambiando de sistema.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

La capa transversal debe ser invisible como arquitectura y evidente como experiencia: el usuario no debe pensar en 'servicios transversales'; debe percibir una única GANUS.
