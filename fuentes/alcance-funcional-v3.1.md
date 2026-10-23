# Alcance y Especificación Funcional Completa v3.1 (decisiones D-01 a D-10)

> **Fuente de solo lectura.** Convertido a Markdown desde `GANUS_Alcance_Especificacion_Funcional_Completa_v3.1_Ciclo_de_Vida.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**GANUS**

**ALCANCE Y ESPECIFICACIÓN FUNCIONAL COMPLETA**

Ecosistema + MVP + decisiones cerradas + ciclo de vida de activos

*Documento maestro para Producto, UX, Arquitectura y Desarrollo*

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>ESTADO DEL DOCUMENTO<br />
</strong>Versión 3.0. Esta versión consolida la Visión Maestra v2.0 y los documentos funcionales de Organización, Inventario, Formularios y Planificación, incorporando como criterio de decisión final los hallazgos de revisión más recientes contenidos en el Documento Funcional de Alcance del MVP de 1 de octubre de 2026. Las contradicciones D-01 a D-10 quedan cerradas en este documento.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

| **Control**           | **Valor**                                                                                                                                                                   |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Producto              | GANUS                                                                                                                                                                       |
| Documento             | Alcance y Especificación Funcional Completa                                                                                                                                 |
| Versión               | 3.0                                                                                                                                                                         |
| Fecha                 | 07 octubre 2026                                                                                                                                                             |
| Nivel                 | Maestro funcional                                                                                                                                                           |
| Criterio de prioridad | Hallazgos más recientes para el MVP; Visión Maestra para el ecosistema                                                                                                      |
| Regla                 | No inventar comportamiento no respaldado; cuando falta detalle técnico se define la necesidad funcional y se reserva el contrato técnico para el artefacto correspondiente. |

# 1. Propósito, carácter y jerarquía de esta especificación

Este documento es la fuente funcional consolidada de GANUS. Su objetivo es eliminar la dispersión entre la visión estratégica y los documentos detallados del MVP, establecer una única arquitectura funcional y dejar cerradas las decisiones que el último documento de hallazgos identificó como contradictorias.

| **Fuente**                                                | **Uso en esta versión**                                                                                                                                                     |
|-----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visión, Alcance y Arquitectura Unificada v2.0             | Define qué es GANUS, arquitectura completa, responsabilidades, límites, interoperabilidad y evolución.                                                                      |
| Documento Funcional de Alcance del MVP v1.0 (01-oct-2026) | Es el criterio de cierre de las contradicciones y el detalle del flujo MVP: Organización, Usuarios, Inventario, Formularios, Planificación, Móvil, Seguimiento y Dashboard. |
| GANUS Organización                                        | Detalle de Gobierno Organizacional.                                                                                                                                         |
| GANUS Inventario                                          | Detalle de identidad y administración de activos.                                                                                                                           |
| GANUS Formularios                                         | Detalle de Field Engine/Formularios y captura.                                                                                                                              |
| GANUS Planificación                                       | Detalle de Make Control/Planificación, ejecución y seguimiento.                                                                                                             |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>REGLA DE JERARQUÍA<br />
</strong>Cuando una capacidad de la Visión pertenece al ecosistema pero no está en el MVP, sigue siendo parte de GANUS pero no se asume como funcionalidad del primer release. Cuando el documento de hallazgos modifica una definición del MVP, esta v3.0 adopta la definición de los hallazgos.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 2. Definición maestra de GANUS

GANUS es una plataforma B2B de gestión empresarial y operacional para fincas ganaderas. Su propósito es transformar la finca en una empresa organizada, controlada, trazable y capaz de tomar decisiones basadas en datos, conectando personas, activos, territorio, actividades, automatización, conocimiento, analítica, inteligencia y procesos empresariales.

## 2.1 Problema

La finca puede operar con experiencia de campo, cuadernos, WhatsApp, hojas de cálculo y conocimiento distribuido entre personas. GANUS convierte esa realidad en estructura, identidad digital, trabajo planificado, captura trazable, conocimiento y decisión.

## 2.2 Propuesta de valor

- Configurar una vez y reutilizar.

- Registrar cada activo una sola vez.

- Planificar antes de ejecutar.

- Entregar al operador exactamente el trabajo que debe realizar.

- Capturar con el menor número de acciones y con operación offline.

- Convertir eventos y resultados en conocimiento gobernado.

- Presentar información según nivel de decisión.

- Conectar la operación con la dimensión empresarial sin construir un ERP paralelo.

## 2.3 Principio rector del campo

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>EL OPERADOR NO ADMINISTRA GANUS<br />
</strong>GANUS le entrega el trabajo, le facilita capturarlo y le permite informar lo extraordinario.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

**Base documental:** Documento Funcional de Alcance del MVP, sección 2.1.

# 3. Cadena funcional y arquitectura

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>ACTIVO → ACTIVIDAD → DATO → REGLA DE NEGOCIO → CONOCIMIENTO → INDICADOR / ALERTA / RECOMENDACIÓN → DECISIÓN → ACCIÓN → NUEVO DATO<br />
</strong>Esta cadena es el hilo conductor de todo el ecosistema. La realidad física no debe quedarse aislada en un módulo: debe poder alimentar una decisión y esa decisión debe poder regresar a la operación.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

| **Dominio**               | **Responsabilidad**                                                        | **Consume**                                   | **Entrega**                                                                         |
|---------------------------|----------------------------------------------------------------------------|-----------------------------------------------|-------------------------------------------------------------------------------------|
| ORGANIZACIÓN              | Gobierno empresarial, grupos/fincas, usuarios, roles, permisos y contexto. | —                                             | Contexto, seguridad y autorización.                                                 |
| INVENTARIO                | Fuente única de verdad de activos e identidad.                             | Organización; Formularios.                    | Activos, identificadores, relaciones, ubicación, atributos, documentos e historial. |
| FORMULARIOS DINÁMICOS     | Define cómo se captura.                                                    | Inventario; configuración; catálogos.         | Formularios publicados/versionados.                                                 |
| PLANIFICACIÓN             | Define qué trabajo hacer, sobre quién, cuándo y quién lo ejecuta.          | Activos; formularios; conocimiento; recursos. | Trabajo programado/asignado.                                                        |
| APLICACIÓN MÓVIL          | Ejecuta y captura en campo.                                                | Trabajo; activos; formularios; contexto.      | Resultados, evidencias, novedades y alertas.                                        |
| GANUS TRACK               | Territorio, posición, permanencia, geocercas y eventos.                    | Activos; espacios; mapas; dispositivos.       | Eventos espaciales y alertas.                                                       |
| POTREROS / AUTOMATIZACIÓN | Detecta eventos físicos.                                                   | Activos; dispositivos; espacios.              | Eventos automáticos normalizados.                                                   |
| CENTRO DE CONOCIMIENTO    | Gobierna reglas y conocimiento.                                            | Datos autorizados y fuentes.                  | Indicadores, alertas, recomendaciones y publicaciones.                              |
| ANALÍTICA                 | Presenta conocimiento publicado.                                           | Knowledge + operación.                        | Vistas estratégicas/tácticas/operativas.                                            |
| ADVISORY                  | Interpreta y recomienda.                                                   | Knowledge + datos autorizados.                | Respuestas, hallazgos y acciones sugeridas.                                         |
| GANUS CORE / ODOO         | Dimensión ERP.                                                             | GANUS + ODOO.                                 | Procesos administrativos, financieros, comerciales y de recursos.                   |
| PLATAFORMA DE DATOS       | Consolida, historiza e integra.                                            | Todos + fuentes externas autorizadas.         | Datos gobernados para explotación.                                                  |

# 4. Actores y niveles de gestión

| **Actor**             | **Nivel**              | **Responsabilidad principal**                                                                         | **Experiencia**         |
|-----------------------|------------------------|-------------------------------------------------------------------------------------------------------|-------------------------|
| Superusuario GANUS    | Plataforma             | Parametriza roles/permisos, secuencias, taxonomías y configuración técnica; no se muestra al cliente. | Backoffice GANUS        |
| Dueño                 | Estratégico            | Consulta indicadores, alertas críticas y estado del negocio.                                          | Dashboard + Advisory    |
| Administrador         | Táctico/administrativo | Gestiona usuarios, organización, inventario, formularios, planificación y seguimiento.                | Web GANUS               |
| Supervisor            | Táctico                | Planifica, valida/publica tareas, atiende alertas y controla ejecución.                               | Web GANUS + Seguimiento |
| Operador              | Operativo              | Recibe trabajo, identifica activos, captura, guarda y reporta alertas.                                | Móvil                   |
| Veterinario           | Perfil de negocio      | No es rol independiente del MVP; utiliza los permisos del rol asignado según su responsabilidad.      | Web/Móvil según rol     |
| Consultor             | Especializado          | Accede a conocimiento autorizado.                                                                     | Knowledge/Advisory      |
| Sistemas/dispositivos | Automatización         | Intercambian eventos.                                                                                 | API/Eventos             |

# 5. Decisiones funcionales cerradas — criterio definitivo del MVP

Estas decisiones sustituyen las alternativas anteriores. Se adoptan con base en las recomendaciones del último documento de hallazgos. No quedan como preguntas abiertas de producto.

| **ID** | **Tema**              | **Decisión final**                            | **Regla resultante**                                                                                                                                                                                                                                               |
|--------|-----------------------|-----------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| D-01   | Grupo Empresarial     | OPCIONAL                                      | Una finca puede existir de manera independiente o asociada a un Grupo Empresarial. La segregación de información debe funcionar en ambos casos.                                                                                                                    |
| D-02   | Roles y permisos      | PREDEFINIDOS PARA CLIENTE                     | El cliente selecciona roles predefinidos y consulta permisos. La capacidad técnica de configurar roles/permisos existe solo para el Superusuario GANUS y no se expone al cliente.                                                                                  |
| D-03   | Recurrencia           | PLANIFICACIÓN                                 | Los Formularios definen captura; Planificación define periodicidad, recurrencia y generación de ocurrencias. La segmentación y la recurrencia son conceptos separados.                                                                                             |
| D-04   | Contexto de plantilla | ELIMINADO DEL FORMULARIO                      | Formulario no administra módulo consumidor, proceso, tipo de actividad ni asignación. La asignación y elegibilidad se resuelven en Planificación. El tipo de activo se usa como criterio de elegibilidad/segmentación, no como contexto operativo de la plantilla. |
| D-05   | Taxonomía             | CATÁLOGO MAESTRO                              | Dominio → Categoría → Tipo → Subtipo es un catálogo maestro parametrizable en Configuración del Sistema, consumido por Inventario y Formularios.                                                                                                                   |
| D-06   | Estados de tarea      | CICLO ÚNICO EN DOS FASES                      | Planificación: Borrador → Validada → Programada → Disponible para campo. Ejecución: En ejecución → Pausada → Finalizada → Por validar → Cerrada. Cancelada es salida alterna. Aceptada y En desplazamiento no pertenecen al MVP.                                   |
| D-07   | Dashboard táctico     | INDICADORES DEL ONBOARDING                    | El MVP entrega un conjunto inicial de indicadores definido durante onboarding. Canvas Builder y biblioteca de widgets quedan para Knowledge Studio fase 2.                                                                                                         |
| D-08   | Navegación móvil      | DISEÑO SIMPLIFICADO                           | El móvil usa Tareas, Alertas e Historial. Novedades se absorbe en Alertas como tipo “Otro”; no se agrega una cuarta navegación.                                                                                                                                    |
| D-09   | Selección de activos  | SOLO DENTRO DE POBLACIÓN RECIBIDA             | La segmentación ocurre exclusivamente en Planificación. El móvil no segmenta ni decide la población; puede seleccionar/identificar activos únicamente dentro del paquete recibido.                                                                                 |
| D-10   | Catálogo de roles     | DUEÑO · ADMINISTRADOR · SUPERVISOR · OPERADOR | Veterinario no es un rol independiente del MVP; es un perfil funcional que utiliza uno de los roles definidos según la responsabilidad y permisos asignados.                                                                                                       |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>EFECTO<br />
</strong>Con estas decisiones, el documento deja de tener bifurcaciones de producto. Lo que queda abierto en capítulos posteriores es principalmente detalle técnico de implementación, no una alternativa funcional que el desarrollador deba escoger.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 6. ORGANIZACIÓN — Especificación funcional

Define el contexto empresarial y de seguridad sobre el que opera todo GANUS.

## 6.1 Alcance definitivo

- Crear y administrar Grupos Empresariales con código automático.

- Crear fincas con o sin Grupo Empresarial.

- Filtrar fincas por grupo.

- Registrar ubicación mediante Departamento → Municipio; no se permite ubicación como texto libre.

- Registrar clasificación Industria → Sector → Negocio en ese orden.

- Definir segmentación física de la finca: Lote, Parcela, Potrero, Corral u otros niveles parametrizables.

- Definir procesos empresariales, tipos de actividad como subnivel de proceso y ciclos de vida configurables.

- Entregar el usuario inicial Administrador1 con rol Administrador.

- Usar roles predefinidos Dueño, Administrador, Supervisor y Operador.

- Mostrar roles/permisos al cliente como información; su administración es exclusiva del Superusuario.

- Desactivar usuarios conservando historial.

- Registrar auditoría de acceso y cambios.

- Permitir configuración corporativa heredable y configuración específica por finca, donde aplique.

## 6.2 Modelo de autorización

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>USUARIO → ROL → DOMINIO → PERMISO → ACCIÓN AUTORIZADA<br />
</strong>F3F6FA</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

## 6.3 Reglas de negocio

- Grupo Empresarial es opcional.

- Una operación siempre está contextualizada por una finca.

- Un usuario puede estar asociado a una o varias fincas según la configuración autorizada; la edición de Grupo/Finca/Rol en el MVP se muestra controlada.

- Ningún usuario ve información fuera de su ámbito autorizado.

- Los roles del cliente no son editables.

- Desactivar un usuario no elimina su historial.

## 6.4 Requerimientos funcionales heredados

| **ID**  | **Requerimiento definitivo**                                                                           | **Pantalla**    |
|---------|--------------------------------------------------------------------------------------------------------|-----------------|
| GOV-001 | Crear y administrar grupos propietarios de una o múltiples fincas                                      | GOV-01          |
| GOV-002 | Crear y administrar fincas                                                                             | GOV-01          |
| GOV-003 | Asociar una finca a un Grupo Empresarial cuando corresponda; la relación es opcional.                  | GOV-01          |
| GOV-004 | Crear usuarios                                                                                         | GOV-01          |
| GOV-005 | Asociar usuarios a una o múltiples fincas                                                              | GOV-01          |
| GOV-006 | Consultar roles predefinidos; la configuración técnica de roles queda reservada al Superusuario GANUS. | GOV-01 / GOV-02 |
| GOV-007 | Consultar permisos por módulo, entidad y acción; la gestión queda reservada al Superusuario GANUS.     | GOV-02          |
| GOV-008 | Consultar permisos por módulo, entidad y acción; la gestión queda reservada al Superusuario GANUS.     | GOV-02          |
| GOV-009 | Consultar permisos por módulo, entidad y acción; la gestión queda reservada al Superusuario GANUS.     | GOV-02          |
| GOV-010 | Registrar auditoría de acceso                                                                          | GOV-03          |
| GOV-011 | Registrar actividad de usuarios                                                                        | GOV-03          |
| GOV-012 | Segregar información entre grupos                                                                      | Todo el dominio |
| GOV-013 | Desactivar usuarios manteniendo historial                                                              | GOV-01          |
| GOV-014 | Heredar configuraciones corporativas desde el grupo                                                    | GOV-01          |
| GOV-015 | Permitir configuraciones específicas por finca                                                         | GOV-01          |

**Base documental:** GANUS_Organizacion + hallazgos consolidados.

# 7. INVENTARIO — Especificación funcional

Inventario es la fuente única de verdad de la identidad digital de los activos. No administra tipos/plantillas: los consume desde la configuración maestra.

## 7.1 Modelo de activo

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>TIPO DE ACTIVO → PLANTILLA → VERSIÓN → ACTIVO → IDENTIFICADORES → UBICACIÓN → RELACIONES → ATRIBUTOS → DOCUMENTOS → HISTORIAL<br />
</strong>F3F6FA</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

## 7.2 Tipos y taxonomía

La taxonomía maestra es parametrizable: Dominio → Categoría → Tipo → Subtipo. Debe servir para activos biológicos, territoriales, infraestructura, equipos, humanos cuando aplique e identidades/dispositivos digitales.

## 7.3 Funcionalidades definitivas

- Consultar tipos y plantillas publicadas.

- Crear activos usando una plantilla publicada.

- Generar automáticamente el código interno por secuencia lógica predefinida; no editable.

- Capturar múltiples identificadores: RFID, código interno, chapeta, código oficial, QR u otros.

- Resolver todos los identificadores al mismo ActivoID.

- Para activos biológicos, disponer de sexo, raza, edad/fecha aplicable y relaciones Padre/Hijo.

- Tomar la finca del contexto del usuario y seleccionar la ubicación física de la finca; si no se selecciona, la ubicación es la finca.

- Adjuntar documentos y evidencias.

- Editar, buscar, filtrar y cambiar estado sin eliminación física.

- Conservar historial completo de cambios.

- Importar desde Excel con estandarización/mapeo, validación previa y reporte.

## 7.4 CICLO DE VIDA DEL ACTIVO

Referencia conceptual: imagen “Ciclo de Vida en GANUS Operativo” aportada para esta versión. Se adopta su lógica de etapa + tareas referenciales + condiciones + indicadores + alertas/decisiones.

Todo activo puede tener asociado un Ciclo de Vida desde el momento de su creación. El ciclo no es un simple estado manual: es una definición parametrizable que establece las etapas esperadas que atraviesa un tipo de activo a lo largo del tiempo, las tareas referenciales, las condiciones a evaluar y los indicadores relevantes para cada etapa.

En activos biológicos, por ejemplo, el ciclo puede representar cría, predestete, levante, desarrollo, pre-servicio, gestación, lactancia y descarte. La solución debe permitir otros ciclos para otros tipos de activo y no debe codificar estas etapas como lógica exclusiva de la vaca lechera.

El ciclo se asigna al activo al crearlo, pero la etapa actual se determina dinámicamente según la definición del ciclo, la edad/tiempo transcurrido, las fechas relevantes y las condiciones que estén parametrizadas. El sistema debe conservar la etapa actual y su trazabilidad histórica.

### 7.4.1 Componentes del Ciclo de Vida

- Ciclo de Vida: plantilla aplicable a una familia/tipo de activo.

- Etapa: periodo o condición dentro del ciclo, con nombre, orden, inicio/fin esperado o condición de transición.

- Tareas referenciales: actividades que normalmente deberían ejecutarse durante una etapa; son guía para Planificación y no sustituyen una tarea programada.

- Condiciones a evaluar: situaciones esperadas, riesgos o desviaciones que Knowledge puede evaluar.

- Indicadores de etapa: variables que permiten observar desempeño y evolución.

- Eventos de transición: hechos o condiciones que pueden confirmar, adelantar, retrasar o cerrar una etapa.

- Historial de ciclo: registro de etapas atravesadas, fechas, eventos y resultados.

### 7.4.2 Comportamiento funcional

- Al crear el activo, el usuario autorizado selecciona el Ciclo de Vida aplicable o el sistema lo propone según la taxonomía y configuración del activo.

- El sistema determina la etapa actual y la muestra en la ficha del activo.

- Cada etapa puede exponer tareas referenciales, condiciones e indicadores relevantes.

- Las tareas referenciales no se convierten automáticamente en tareas de campo salvo que una regla de Planificación las genere o un usuario autorizado las programe.

- Knowledge evalúa los datos registrados contra las condiciones y expectativas de la etapa.

- Una desviación puede generar indicador, alerta, recomendación o propuesta de acción según la regla configurada.

- Advisory puede explicar en lenguaje de negocio en qué etapa está el activo, qué debería estar ocurriendo, qué se ha registrado, qué falta y qué decisión se recomienda.

- Los resultados del ciclo alimentan el historial del activo y la analítica; no crean una segunda identidad ni un segundo historial.

### 7.4.3 Relación con Planificación, Knowledge y Advisory

La relación funcional es: INVENTARIO asigna y mantiene el contexto del ciclo → PLANIFICACIÓN utiliza las tareas referenciales para generar trabajo cuando corresponda → APLICACIÓN MÓVIL registra la ejecución → KNOWLEDGE compara lo esperado con lo observado y aplica reglas → ANALÍTICA publica indicadores/alertas → ADVISORY interpreta el estado y recomienda acciones.

Por tanto, el Ciclo de Vida pertenece al dominio del activo en Inventario, pero su comportamiento es transversal. Inventario es responsable de la definición/relación del ciclo y del estado histórico; Knowledge es responsable de evaluar las condiciones y reglas; Advisory es responsable de interpretar los resultados; Planificación es responsable de convertir trabajo necesario en ejecución.

### 7.4.4 Ejemplo funcional: vaca lechera

Para una vaca nacida el 15/01/2024, el sistema puede asociar el ciclo de vida de vaca lechera y, según la fecha actual y las reglas del ciclo, ubicarla en la etapa correspondiente. En cada etapa puede mostrar tareas referenciales como pesajes, vacunación, evaluación reproductiva, nutrición o control de producción; condiciones como bajo crecimiento, baja condición corporal o problemas sanitarios; e indicadores como peso, ganancia diaria, condición corporal, días de gestación o litros de leche.

La etapa no debe limitarse a una edad fija: las fechas, eventos y condiciones registradas pueden modificar la interpretación del ciclo cuando la configuración del negocio lo permita. La imagen de referencia es un modelo conceptual del comportamiento, no un catálogo rígido de etapas para todos los activos.

### 7.4.5 Reglas de negocio del ciclo

- RN-CV-01: Todo activo que tenga un ciclo definido debe mantener la referencia al CicloID aplicable.

- RN-CV-02: Un ciclo es versionable; los cambios de definición no deben reescribir retrospectivamente la historia del activo.

- RN-CV-03: La etapa actual debe poder explicarse mediante los parámetros/reglas que la determinaron.

- RN-CV-04: Las tareas referenciales son orientación del ciclo; Planificación controla la creación, asignación y ejecución real.

- RN-CV-05: Una condición incumplida no genera por sí sola una acción sin una regla de Knowledge que determine el resultado.

- RN-CV-06: Advisory solo debe recomendar a partir de datos, indicadores y reglas gobernadas.

- RN-CV-07: La transición de etapa debe conservar fecha, fuente/evento y trazabilidad.

- RN-CV-08: El ciclo es aplicable a cualquier familia de activo que tenga un modelo de evolución; no es exclusivo de animales.

## 7.5 Reglas críticas

- Un activo existe una sola vez.

- El código automático no se edita.

- Un identificador adicional no crea un nuevo activo.

- Inventario no define reglas, indicadores ni planificación.

- Inventario publica identidad digital para consumidores autorizados.

## 7.6 Requerimientos

| **ID**  | **Requerimiento**                                                                    | **Prioridad** | **Funcionalidad** |
|---------|--------------------------------------------------------------------------------------|---------------|-------------------|
| INV-001 | Consultar los tipos de activo publicados por Field Engine                            | P0            | F01               |
| INV-002 | Consultar las plantillas activas publicadas por Field Engine                         | P0            | F01               |
| INV-003 | Registrar un nuevo activo usando una plantilla publicada                             | P0            | F02               |
| INV-004 | Editar un activo respetando la estructura definida por la plantilla                  | P0            | F02               |
| INV-005 | Cambiar el estado operativo de un activo conservando el historial                    | P0            | F02               |
| INV-006 | Consultar la ficha digital completa del activo                                       | P0            | F02               |
| INV-007 | Buscar y filtrar activos por cualquier atributo disponible                           | P0            | F02               |
| INV-008 | Importar activos mediante archivos estructurados con validación previa               | P1            | F03               |
| INV-009 | Validar la estructura del archivo de migración contra la plantilla del activo        | P1            | F03               |
| INV-010 | Publicar automáticamente la identidad digital para MAKE, TRACK, Dashboard y Advisory | P0            | F04               |
| INV-011 | Mantener trazabilidad completa de todas las modificaciones de un activo              | P0            | Global            |
| INV-012 | No permitir eliminar físicamente un activo; solo cambiar su estado                   | P0            | Global            |

**Base documental:** GANUS_Inventario + hallazgos consolidados.

# 8. FORMULARIOS DINÁMICOS — Especificación funcional

Formularios Dinámicos es el nombre funcional de Field Engine. Su responsabilidad es definir cómo se captura información. No planifica ni asigna trabajo.

## 8.1 Responsabilidad definitiva

- Crear y administrar plantillas reutilizables.

- Construir campos, secciones, obligatoriedad, ayuda, valores por defecto, catálogos y validaciones.

- Configurar métodos de captura por campo.

- Definir claves de búsqueda y claves de contexto.

- Previsualizar.

- Versionar y publicar.

- Conservar versiones anteriores.

- Proporcionar definición de captura a Inventario y Planificación.

## 8.2 Lo que se elimina del formulario

- No administra módulos consumidores.

- No asigna procesos ni actividades a la plantilla.

- No programa recurrencia.

- No administra registros históricos.

- No decide qué activos reciben la tarea.

## 8.3 Claves de búsqueda y contexto

| **Concepto**      | **Función**                                                                                                               |
|-------------------|---------------------------------------------------------------------------------------------------------------------------|
| Clave de búsqueda | Cómo se localiza el activo: RFID, ID GANUS, ID animal, nombre, chapeta u otras claves configuradas.                       |
| Clave de contexto | Información mínima para trazabilidad del registro: activo principal, responsable, ubicación, fecha y código del registro. |
| Relación          | La clave de búsqueda permite resolver el activo que alimenta el contexto del registro.                                    |

## 8.4 Tipos/métodos de captura

El motor permite tipos de campo como texto, número, decimal, fecha, lista, booleano, fotografía, documento, firma, ubicación y activo relacionado; los métodos pueden ser manuales, selección, voz, RFID, cámara, dispositivo o integración, según configuración.

## 8.5 Versionamiento

- Una plantilla publicada y en uso no se modifica.

- Un cambio crea una nueva versión.

- La versión utilizada por una tarea queda identificada y no cambia durante su ejecución.

## 8.6 Voz

1.  Iniciar captura por voz desde una tarea activa.

2.  Identificar el contexto de la tarea.

3.  Interpretar lenguaje natural.

4.  Completar campos inferibles.

5.  Preguntar solo por información faltante.

6.  Validar con las reglas del formulario.

7.  Confirmar verbalmente el resumen.

8.  Permitir correcciones conversacionales.

9.  Registrar fecha, hora, usuario y ubicación.

10. Guardar en el formulario correspondiente.

11. Permitir operación web/móvil y, donde aplique, offline.

## 8.7 Requerimientos

| **ID** | **Requerimiento**                                          | **Prioridad** | **Funcionalidad** |
|--------|------------------------------------------------------------|---------------|-------------------|
| FE-001 | Crear tipos de activo y tipos de actividad                 | P0            | F01               |
| FE-002 | Crear y administrar plantillas reutilizables               | P0            | F02               |
| FE-003 | Diseñar formularios con un constructor dinámico de campos  | P0            | F03               |
| FE-004 | Configurar métodos de captura por campo                    | P0            | F03               |
| FE-005 | Configurar catálogos reutilizables                         | P0            | F04               |
| FE-006 | Definir reglas de validación por campo                     | P0            | F04               |
| FE-007 | Definir identificadores y claves de contexto               | P0            | F05               |
| FE-008 | Configurar agenda de ejecución para formularios periódicos | P0            | F06               |
| FE-009 | Versionar plantillas y mantener historial                  | P0            | F07               |
| FE-010 | Publicar versiones para consumo por Inventario y MAKE      | P0            | F07               |

## 8.8 Requerimientos de voz

| **ID** | **Requerimiento**                                                 | **Prioridad** |
|--------|-------------------------------------------------------------------|---------------|
| OP-075 | Iniciar captura por voz desde cualquier orden operativa           | P0            |
| OP-076 | Interpretar lenguaje natural con IA                               | P0            |
| OP-077 | Identificar el contexto de la orden operativa activa              | P0            |
| OP-078 | Completar automáticamente los campos que puedan inferirse         | P0            |
| OP-079 | Pedir únicamente la información faltante                          | P0            |
| OP-080 | Validar con las reglas definidas en Field Engine                  | P0            |
| OP-081 | Confirmar verbalmente el resumen antes de guardar                 | P0            |
| OP-082 | Permitir correcciones por conversación                            | P0            |
| OP-083 | Registrar fotografías, videos y ubicación durante la conversación | P1            |
| OP-084 | Registrar automáticamente fecha, hora, usuario y ubicación        | P0            |
| OP-085 | Guardar en el formulario dinámico correspondiente                 | P0            |
| OP-086 | Funcionar en web y en dispositivos móviles                        | P0            |
| OP-087 | Operar sin conexión guardando la conversación hasta sincronizar   | P1            |

**Base documental:** GANUS_Formularios + hallazgos consolidados.

# 9. PLANIFICACIÓN — Especificación funcional

Planificación es el centro de generación de trabajo. Formularios define la captura; Planificación define qué trabajo se hará, sobre quién, cuándo y quién lo ejecuta.

## 9.1 Tarea unitaria

Una tarea unitaria vincula un formulario publicado, un activo, un recurso y una ejecución/captura. El activo llega al móvil ya identificado.

## 9.2 Tarea masiva

- Seleccionar todos los activos elegibles.

- Seleccionar porcentaje/muestra.

- Filtrar combinando claves de búsqueda y atributos del activo.

- Mostrar población encontrada antes de publicar.

- Mostrar muestra y población final.

- Exigir previsualización.

- Permitir “Probar tarea” para simular la experiencia del operador.

## 9.3 Periodicidad y recurrencia

La segmentación responde a “sobre quién”; la periodicidad responde a “cuándo”. Cada ocurrencia recurrente puede reevaluar las condiciones de elegibilidad.

## 9.4 Estados definitivos

| **Fase**      | **Estados**                                                 | **Regla**                                                                                                              |
|---------------|-------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Planificación | Borrador → Validada → Programada → Disponible para campo    | Guardar no envía al móvil; solo una tarea disponible puede entregarse.                                                 |
| Ejecución     | En ejecución → Pausada → Finalizada → Por validar → Cerrada | Finalizada significa que el operador terminó; Por validar corresponde a control posterior; Cerrada confirma el cierre. |
| Alterno       | Cancelada                                                   | Salida terminal alternativa.                                                                                           |

## 9.5 Funcionalidades MVP definitivas

- Crear tareas unitarias y masivas.

- Seleccionar formulario publicado; la clave de búsqueda se hereda y no se modifica en la tarea.

- Asignar recurso.

- Definir fechas, prioridad y descripción.

- Filtrar población objetivo.

- Previsualizar población.

- Probar tarea.

- Programar y reprogramar.

- Calendario día/semana/mes.

- Filtrar por recurso.

- Recibir alertas de campo.

- Convertir una alerta en tarea de forma manual en el MVP.

- Consultar seguimiento y cumplimiento.

## 9.6 Optimización avanzada

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>NO ES PARTE DEL PRIMER RELEASE<br />
</strong>El balanceador, motor de optimización y bandeja inteligente aparecen en el documento detallado de Planificación, pero los hallazgos más recientes no los consideran parte cerrada del MVP. Se conservan como evolución funcional, no como requisito de implementación del primer release.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

## 9.7 Requerimientos de Planificación

| **ID** | **Requerimiento**                                                          | **Prioridad** | **Funcionalidad** |
|--------|----------------------------------------------------------------------------|---------------|-------------------|
| MK-001 | Crear una nueva orden operativa                                            | P0            | F01               |
| MK-002 | Programar una orden operativa manualmente                                  | P0            | F01               |
| MK-003 | Generar órdenes automáticamente desde la agenda publicada por Field Engine | P0            | F01               |
| MK-004 | Permitir programaciones recurrentes                                        | P0            | F01               |
| MK-005 | Visualizar la agenda en vista día, semana y mes                            | P0            | F02               |
| MK-006 | Consultar disponibilidad de recursos                                       | P0            | F03               |
| MK-007 | Consultar carga operacional por persona y cuadrilla                        | P0            | F03               |
| MK-008 | Detectar conflictos de programación                                        | P0            | F03               |
| MK-009 | Reasignar recursos                                                         | P0            | F03               |
| MK-010 | Actualizar automáticamente la planificación al modificar una orden         | P0            | Global            |

## 9.8 Requerimientos de ejecución

| **ID** | **Requerimiento**                                                  | **Prioridad** |
|--------|--------------------------------------------------------------------|---------------|
| MK-046 | Iniciar una orden operativa                                        | P0            |
| MK-047 | Solicitar automáticamente el formulario publicado por Field Engine | P0            |
| MK-048 | Construir dinámicamente la pantalla de captura                     | P0            |
| MK-049 | Registrar información usando el formulario dinámico                | P0            |
| MK-050 | Registrar evidencias (foto, video, audio, documentos, firma, GPS)  | P0            |
| MK-051 | Validar la información antes del cierre de la orden                | P0            |
| MK-052 | Actualizar la identidad digital del activo en Inventario           | P0            |
| MK-053 | Publicar eventos para Knowledge Studio                             | P0            |
| MK-054 | Cambiar automáticamente el estado de la orden                      | P0            |
| MK-055 | Mantener trazabilidad completa de la ejecución                     | P0            |

## 9.9 Requerimientos de seguimiento

| **ID** | **Requerimiento**                    | **Prioridad** |
|--------|--------------------------------------|---------------|
| MK-056 | Visualizar órdenes programadas       | P0            |
| MK-057 | Visualizar órdenes en ejecución      | P0            |
| MK-058 | Visualizar órdenes finalizadas       | P0            |
| MK-059 | Visualizar órdenes canceladas        | P1            |
| MK-060 | Visualizar órdenes vencidas          | P0            |
| MK-061 | Calcular cumplimiento diario         | P0            |
| MK-062 | Calcular cumplimiento semanal        | P0            |
| MK-063 | Calcular productividad por recurso   | P1            |
| MK-064 | Calcular productividad por cuadrilla | P1            |

**Base documental:** GANUS_Planificacion + Documento Funcional de Alcance del MVP + hallazgos.

# 10. APLICACIÓN MÓVIL — Entrega y ejecución

El móvil es el centro de ejecución. No planifica, no segmenta poblaciones y no administra la configuración.

## 10.1 Paquete de trabajo

Al validar y publicar una tarea, el dispositivo recibe como mínimo la definición necesaria para ejecutar exactamente el trabajo asignado: formulario/version, clave de búsqueda, recurso, fechas, prioridad, contexto y población objetivo con los datos necesarios para identificar y precargar cada activo.

## 10.2 Flujo de ejecución

12. Recibir únicamente tareas asignadas.

13. Priorizar por criticidad y vencimiento.

14. Identificar activo mediante clave, escaneo o voz.

15. Cargar contexto precargado.

16. Diligenciar formulario.

17. Capturar evidencias.

18. Guardar.

19. Continuar con el siguiente activo cuando corresponda.

20. Reportar alerta/novedad.

21. Sincronizar cuando haya conectividad.

## 10.3 Offline

- Los datos necesarios para identificar activos, precargar contexto y ejecutar el formulario deben residir localmente.

- Las capturas se guardan localmente.

- Debe existir estado visible de sincronización.

- La cola se sincroniza al recuperar conectividad.

- Si existe conflicto, se registra para resolución según el mecanismo definido por la solución; no se pierde silenciosamente información.

## 10.4 Navegación

| **Elemento** | **Definición MVP**                                                     |
|--------------|------------------------------------------------------------------------|
| Tareas       | Buzón de trabajo asignado.                                             |
| Alertas      | Alertas críticas y novedades; Novedades se maneja como tipo “Otro”.    |
| Historial    | Consulta de actividad/historial disponible al operador según permisos. |

## 10.5 Voz

La voz se integra dentro de la ejecución del formulario, no como una aplicación separada. Debe permitir identificación, captura y corrección conversacional.

## 10.6 Límite

No contiene dashboard estratégico ni segmentación de población.

# 11. SEGUIMIENTO OPERATIVO — Especificación funcional MVP

Seguimiento es una sección de consulta y control; no administra ni redefine las tareas.

- Tarjetas: total de tareas, vencidas, en curso y pendientes.

- Indicador de cumplimiento = tareas ejecutadas / total de tareas.

- Filtros por estado, tipo, vencimiento y recurso.

- Consulta de una tarea específica.

- Acceso al detalle de lo planificado, asignado, ejecutado y resultado.

- Recepción de alertas provenientes del campo.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>OBJETIVO DE TRAZABILIDAD<br />
</strong>Debe poder responderse: ¿qué se planificó?, ¿qué se asignó?, ¿qué se ejecutó?, ¿qué resultado se obtuvo?</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 12. DASHBOARD — Especificación funcional MVP

El Dashboard táctico presenta indicadores y estado operativo. Los indicadores son definidos en el onboarding y publicados por Knowledge Studio; el Dashboard no inventa indicadores.

## 12.1 Alcance MVP

- Dashboard táctico.

- Conjunto inicial de indicadores definido en onboarding.

- Resumen operativo.

- Advisory contextual.

- Filtros globales.

- Drill-down al origen.

- Exportación cuando esté contemplada por el MVP.

## 12.2 Fuera del primer release

- Canvas Builder.

- Biblioteca completa de widgets.

- Configuración libre de dashboards.

- Construcción autónoma de indicadores desde Analytics.

**Base documental:** Decisión D-07 de los hallazgos.

# 13. GANUS TRACK — Alcance del ecosistema

TRACK es la capa territorial. La especificación consolidada exige comenzar por el Gemelo Digital y no por el dispositivo.

22. Construir Gemelo Digital territorial.

23. Definir Espacios Territoriales jerárquicos.

24. Asociar activos a espacios.

25. Definir geocercas cuando exista propósito operacional.

26. Recibir posiciones/eventos.

27. Calcular permanencia, entrada, salida y movimiento.

28. Evaluar reglas.

29. Generar alertas.

30. Enviar eventos/alertas a Planificación y móvil.

## 13.1 Espacios Territoriales

Finca, sector, lote, parcela, potrero, corral, zona, instalación u otra unidad definida por la finca. Un Espacio Territorial es una entidad del negocio; una geocerca es una condición geométrica.

## 13.2 Dispositivos

La arquitectura puede utilizar GPS, BLE, LoRa/LoRaWAN, nodos líderes, TAG inteligentes y Gateway GANUS según cobertura, autonomía, costo y caso de uso. GANUS debe abstraer fabricante y normalizar eventos.

## 13.3 Límite

TRACK no es maestro de identidad ni contiene reglas empresariales complejas.

# 14. POTREROS / AUTOMATIZACIÓN — Alcance del ecosistema

- RFID, portales y puntos de paso.

- Cámaras y eventos derivados.

- Básculas y pesaje.

- Entrada/salida automática.

- Resolución a ActivoID.

- Normalización de eventos.

- Envío a Centro de Conocimiento.

- Disparo de tareas cuando una regla habilitada lo determine.

Regla: el dispositivo detecta; la regla determina el significado empresarial.

# 15. CENTRO DE CONOCIMIENTO — Alcance del ecosistema

- Plantillas de conocimiento por tipo de negocio.

- Objetivos estratégicos.

- Catálogo de reglas.

- Constructor de condiciones.

- Fuentes internas GANUS.

- Fuentes empresariales ODOO.

- Fuentes externas autorizadas.

- Fuentes documentales.

- Resultados: indicadores, alertas, recomendaciones, visualizaciones, publicaciones y automatizaciones.

- Simulación/validación, activación, desactivación y versionamiento.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>FLUJO<br />
</strong>DATO → REGLA → RESULTADO → PUBLICACIÓN → ANALÍTICA / ADVISORY → DECISIÓN</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

La IA puede proponer conocimiento a partir de documentos, pero no publica reglas productivas sin validación.

# 16. ANALÍTICA Y VISUALIZACIÓN — Alcance del ecosistema

| **Experiencia**   | **Usuarios**              | **Responsabilidad**                                                                                                     |
|-------------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Estratégica / Web | Dueño, ganadero, gerente  | Estado del negocio, indicadores publicados, alertas estratégicas, tendencias, comparaciones, recomendaciones, Advisory. |
| Táctica / Web     | Administrador, Supervisor | Cumplimiento, seguimiento, indicadores de gestión, Kanban, alertas, filtros y drill-down.                               |
| Operativa / Móvil | Operador                  | Trabajo asignado, pendientes, ejecución, notificaciones y captura.                                                      |

Analytics consume conocimiento publicado; no crea reglas paralelas.

# 17. ADVISORY / ASISTENTE INTELIGENTE — Alcance del ecosistema

- Consultar activos, operación y resultados.

- Interpretar indicadores.

- Explicar alertas.

- Consultar pendientes.

- Encontrar activos que cumplen condiciones.

- Explicar origen de resultados.

- Recomendar acciones fundamentadas.

- Interactuar por web, móvil y WhatsApp cuando esté habilitado.

- Asistir captura por voz.

Debe distinguir dato, cálculo, interpretación y recomendación; no inventar información.

# 18. GANUS CORE / ODOO — Alcance del ecosistema

- Compras, ventas, costos, inventarios empresariales, contabilidad, tesorería, RRHH y producción cuando corresponda.

- GANUS aporta datos de campo, activos, actividades y resultados.

- ODOO aporta la dimensión financiera, administrativa y comercial.

- Knowledge puede combinar ambas fuentes.

ODOO no sustituye la captura de campo ni la lógica especializada de activos biológicos y territorio.

# 19. PLATAFORMA DE DATOS — Alcance del ecosistema

- Ingesta.

- Normalización.

- Histórico.

- Trazabilidad de origen.

- Calidad.

- Gobierno.

- Explotación analítica.

- Preparación para IA.

- Interoperabilidad.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>FUENTES DE VERDAD<br />
</strong>Inventario = identidad de activos. Planificación = trabajo programado. ODOO = procesos ERP. Plataforma de Datos = consolidación/histórico/explotación.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 20. Interoperabilidad

## 20.1 Identificadores comunes

| **ID**                 | **Uso**                                 |
|------------------------|-----------------------------------------|
| GrupoID                | Contexto empresarial.                   |
| FincaID                | Unidad operacional.                     |
| UsuarioID              | Identidad de acceso.                    |
| ActivoID               | Identidad transversal.                  |
| Identificador externo  | RFID, código, QR, chapeta, etc.         |
| ActividadID            | Identidad del trabajo.                  |
| FormularioID + versión | Definición exacta de captura.           |
| ReglaID + versión      | Regla que generó resultado.             |
| EventoID               | Evento operacional/espacial/automático. |
| EspacioTerritorialID   | Contexto geográfico.                    |

## 20.2 Eventos

| **Evento**                    | **Origen**           | **Consumidores**                    |
|-------------------------------|----------------------|-------------------------------------|
| Activo creado/actualizado     | Inventario           | Dominios autorizados.               |
| Actividad programada          | Planificación        | Móvil, Analítica.                   |
| Actividad iniciada/completada | Móvil                | Planificación, Knowledge, Datos.    |
| RFID detectado                | Automatización       | Inventario, Track, Knowledge.       |
| Entrada/salida                | Track                | Knowledge, Planificación, móvil.    |
| Permanencia fuera de espacio  | Track                | Knowledge, alertas, Planificación.  |
| Pesaje                        | Automatización/móvil | Inventario, Knowledge, Analítica.   |
| Regla activada                | Knowledge            | Analítica, Advisory, Planificación. |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>PATRÓN<br />
</strong>FUENTE → EVENTO/API → NORMALIZACIÓN → OBJETO GANUS → REGLA → RESULTADO → DESTINO</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 21. Flujos integrales

## 21.1 Actividad sanitaria

31. Inventario identifica el animal.

32. Una rutina/regla determina necesidad.

33. Planificación genera y asigna tarea.

34. Móvil entrega trabajo.

35. Operador identifica animal y captura.

36. Resultado actualiza historial.

37. Knowledge evalúa nuevas condiciones.

38. Seguimiento/Analítica muestran cumplimiento.

39. Advisory explica estado.

## 21.2 Movimiento territorial

40. Activo identificado en Inventario.

41. Track conoce espacio.

42. Dispositivo registra posición.

43. Track calcula entrada/salida/permanencia.

44. Knowledge evalúa regla.

45. Se genera alerta.

46. Planificación genera trabajo si corresponde.

47. Móvil ejecuta.

## 21.3 RFID + pesaje

48. Portal detecta RFID.

49. Se resuelve ActivoID.

50. Báscula entrega peso.

51. Evento queda registrado.

52. Knowledge evalúa condición.

53. Indicador/alerta se publica.

## 21.4 Rentabilidad

54. GANUS aporta datos productivos.

55. ODOO aporta ingresos/costos.

56. Knowledge calcula regla.

57. Se genera indicador.

58. Dashboard publica.

59. Advisory interpreta.

# 22. Fronteras funcionales

| **Componente** | **Sí hace**                                           | **No hace**                         |
|----------------|-------------------------------------------------------|-------------------------------------|
| Organización   | Contexto, usuarios, roles, permisos.                  | Operación de activos.               |
| Inventario     | Identidad, activos, relaciones, atributos, historial. | Planificación/indicadores.          |
| Formularios    | Definir captura.                                      | Agenda/segmentación/ejecución.      |
| Planificación  | Generar, segmentar, programar y asignar trabajo.      | Ser maestro de activos.             |
| Móvil          | Ejecutar y capturar.                                  | Segmentar población/BI estratégico. |
| Track          | Territorio y eventos.                                 | Identidad maestra/reglas complejas. |
| Automatización | Detectar eventos físicos.                             | Decidir significado empresarial.    |
| Knowledge      | Reglas y conocimiento.                                | Dibujar dashboards manualmente.     |
| Analytics      | Presentar conocimiento.                               | Crear reglas paralelas.             |
| Advisory       | Interpretar/recomendar.                               | Inventar datos.                     |
| ODOO           | ERP.                                                  | Captura especializada de campo.     |
| Data Platform  | Consolidar/explotar.                                  | Segunda fuente maestra.             |

# 23. Criterios de aceptación consolidados

| **Caso** | **Prueba**                        | **Criterio de aceptación**                                                 |
|----------|-----------------------------------|----------------------------------------------------------------------------|
| CP-01    | Crear finca sin Grupo Empresarial | Lo permite y mantiene el modelo consistente.                               |
| CP-02    | Crear finca asociada a un grupo   | Muestra relación y permite filtrar por grupo.                              |
| CP-03    | Crear activo                      | Código automático y taxonomía consistente.                                 |
| CP-04    | Crear activo biológico            | Sexo, raza, edad y relaciones aplicables disponibles.                      |
| CP-05    | Formulario con clave RFID         | Clave configurada como búsqueda y código automático.                       |
| CP-06    | Publicar formulario               | Pasa a publicada y deja de ser editable.                                   |
| CP-07    | Tarea unitaria                    | Un activo y un recurso reciben exactamente una captura.                    |
| CP-08    | Tarea masiva por filtros          | Población calculada antes de publicar y alcance visible.                   |
| CP-09    | Tarea masiva por muestra          | Porcentaje calculado sobre población elegible y resultado visible.         |
| CP-10    | Probar tarea                      | Supervisor simula experiencia del operador antes de publicar.              |
| CP-11    | Ejecución sin conexión            | Operador identifica, captura, guarda y sincroniza al recuperar conexión.   |
| CP-12    | Alerta de campo                   | Llega a Planificación y puede convertirse en trabajo según reglas futuras. |
| CP-13    | Seguimiento                       | Administrador filtra, ve estados, cumplimiento y detalle.                  |
| CP-14    | Trazabilidad                      | Se determina qué se planificó, asignó, ejecutó y resultado obtenido.       |

## 23.0A Criterios de aceptación — Ciclo de Vida

## 23.1 Criterios transversales

| **Dimensión**     | **Criterio**                                                           |
|-------------------|------------------------------------------------------------------------|
| Identidad         | Múltiples identificadores resuelven un único ActivoID.                 |
| Configuración     | Un usuario autorizado configura un formulario estándar sin desarrollo. |
| Versionamiento    | Una versión publicada no se modifica.                                  |
| Trazabilidad      | Se reconstruye qué/cuándo/dónde/sobre quién/quién.                     |
| Offline           | El trabajo puede ejecutarse sin conectividad.                          |
| Planificación     | La población objetivo se define antes del campo.                       |
| Conocimiento      | Cada resultado relevante tiene regla/fuente identificable.             |
| Interoperabilidad | No se duplican identidades entre módulos.                              |
| Auditoría         | Cambios relevantes tienen autor y fecha.                               |

# 24. Mapa de alcance: MVP vs ecosistema

| **Dominio**    | **MVP final**                                                                                                             | **Ecosistema / evolución**                  |
|----------------|---------------------------------------------------------------------------------------------------------------------------|---------------------------------------------|
| Organización   | Grupo opcional, fincas, ubicación, segmentación física, usuarios, roles predefinidos, procesos/ciclos de vida, auditoría. | Herencia/gobierno avanzado.                 |
| Inventario     | Activos, taxonomía maestra, códigos automáticos, identificadores múltiples, relaciones, documentos, migración.            | Ciclo de vida/valoración avanzada.          |
| Formularios    | Constructor, métodos, claves, validaciones, versiones, publicación, voz.                                                  | Más integraciones y capacidades de captura. |
| Planificación  | Tareas unitarias/masivas, población, previsualización, prueba, periodicidad, calendario, asignación, estados.             | Balanceador/optimización avanzada.          |
| Móvil          | Paquete de trabajo, ejecución, voz, evidencias, alertas, offline, historial.                                              | Asistente contextual avanzado.              |
| Seguimiento    | Estado, cumplimiento, detalle y trazabilidad.                                                                             | Analítica avanzada.                         |
| Dashboard      | Táctico con indicadores del onboarding, Advisory, filtros y drill-down.                                                   | Canvas/Widgets fase 2.                      |
| Track          | Modelo territorial/Gemelo Digital como capacidad del ecosistema; release específico se controla aparte.                   | Tracking, geocercas, eventos y hardware.    |
| Automatización | Arquitectura preparada.                                                                                                   | RFID, portales, cámaras, pesaje.            |
| Knowledge      | Dependencia funcional; indicadores iniciales publicados.                                                                  | Studio completo, simulación, plantillas.    |
| Advisory       | Panel/interpretación inicial.                                                                                             | Agente empresarial avanzado.                |
| ODOO           | Dependencia/integraciones prioritarias.                                                                                   | Integración profunda.                       |
| Data Platform  | Modelo/eventos/histórico mínimo.                                                                                          | Plataforma avanzada.                        |

# 25. Matriz maestra de requerimientos por módulo

Se conservan los identificadores originales para trazabilidad. Cuando un requerimiento original queda modificado por una decisión de hallazgos, la regla definitiva de este documento prevalece.

## 25.1 Organización

| **ID**  | **Requerimiento**                                                                                      | **Pantalla**    |
|---------|--------------------------------------------------------------------------------------------------------|-----------------|
| GOV-001 | Crear y administrar grupos propietarios de una o múltiples fincas                                      | GOV-01          |
| GOV-002 | Crear y administrar fincas                                                                             | GOV-01          |
| GOV-003 | Asociar una finca a un Grupo Empresarial cuando corresponda; la relación es opcional.                  | GOV-01          |
| GOV-004 | Crear usuarios                                                                                         | GOV-01          |
| GOV-005 | Asociar usuarios a una o múltiples fincas                                                              | GOV-01          |
| GOV-006 | Consultar roles predefinidos; la configuración técnica de roles queda reservada al Superusuario GANUS. | GOV-01 / GOV-02 |
| GOV-007 | Consultar permisos por módulo, entidad y acción; la gestión queda reservada al Superusuario GANUS.     | GOV-02          |
| GOV-008 | Consultar permisos por módulo, entidad y acción; la gestión queda reservada al Superusuario GANUS.     | GOV-02          |
| GOV-009 | Consultar permisos por módulo, entidad y acción; la gestión queda reservada al Superusuario GANUS.     | GOV-02          |
| GOV-010 | Registrar auditoría de acceso                                                                          | GOV-03          |
| GOV-011 | Registrar actividad de usuarios                                                                        | GOV-03          |
| GOV-012 | Segregar información entre grupos                                                                      | Todo el dominio |
| GOV-013 | Desactivar usuarios manteniendo historial                                                              | GOV-01          |
| GOV-014 | Heredar configuraciones corporativas desde el grupo                                                    | GOV-01          |
| GOV-015 | Permitir configuraciones específicas por finca                                                         | GOV-01          |

## 25.2 Inventario

| **ID**  | **Requerimiento**                                                                    | **Prioridad** | **Funcionalidad** |
|---------|--------------------------------------------------------------------------------------|---------------|-------------------|
| INV-001 | Consultar los tipos de activo publicados por Field Engine                            | P0            | F01               |
| INV-002 | Consultar las plantillas activas publicadas por Field Engine                         | P0            | F01               |
| INV-003 | Registrar un nuevo activo usando una plantilla publicada                             | P0            | F02               |
| INV-004 | Editar un activo respetando la estructura definida por la plantilla                  | P0            | F02               |
| INV-005 | Cambiar el estado operativo de un activo conservando el historial                    | P0            | F02               |
| INV-006 | Consultar la ficha digital completa del activo                                       | P0            | F02               |
| INV-007 | Buscar y filtrar activos por cualquier atributo disponible                           | P0            | F02               |
| INV-008 | Importar activos mediante archivos estructurados con validación previa               | P1            | F03               |
| INV-009 | Validar la estructura del archivo de migración contra la plantilla del activo        | P1            | F03               |
| INV-010 | Publicar automáticamente la identidad digital para MAKE, TRACK, Dashboard y Advisory | P0            | F04               |
| INV-011 | Mantener trazabilidad completa de todas las modificaciones de un activo              | P0            | Global            |
| INV-012 | No permitir eliminar físicamente un activo; solo cambiar su estado                   | P0            | Global            |

## 25.3 Formularios

| **ID** | **Requerimiento**                                          | **Prioridad** | **Funcionalidad** |
|--------|------------------------------------------------------------|---------------|-------------------|
| FE-001 | Crear tipos de activo y tipos de actividad                 | P0            | F01               |
| FE-002 | Crear y administrar plantillas reutilizables               | P0            | F02               |
| FE-003 | Diseñar formularios con un constructor dinámico de campos  | P0            | F03               |
| FE-004 | Configurar métodos de captura por campo                    | P0            | F03               |
| FE-005 | Configurar catálogos reutilizables                         | P0            | F04               |
| FE-006 | Definir reglas de validación por campo                     | P0            | F04               |
| FE-007 | Definir identificadores y claves de contexto               | P0            | F05               |
| FE-008 | Configurar agenda de ejecución para formularios periódicos | P0            | F06               |
| FE-009 | Versionar plantillas y mantener historial                  | P0            | F07               |
| FE-010 | Publicar versiones para consumo por Inventario y MAKE      | P0            | F07               |

## 25.4 Voz

| **ID** | **Requerimiento**                                                 | **Prioridad** |
|--------|-------------------------------------------------------------------|---------------|
| OP-075 | Iniciar captura por voz desde cualquier orden operativa           | P0            |
| OP-076 | Interpretar lenguaje natural con IA                               | P0            |
| OP-077 | Identificar el contexto de la orden operativa activa              | P0            |
| OP-078 | Completar automáticamente los campos que puedan inferirse         | P0            |
| OP-079 | Pedir únicamente la información faltante                          | P0            |
| OP-080 | Validar con las reglas definidas en Field Engine                  | P0            |
| OP-081 | Confirmar verbalmente el resumen antes de guardar                 | P0            |
| OP-082 | Permitir correcciones por conversación                            | P0            |
| OP-083 | Registrar fotografías, videos y ubicación durante la conversación | P1            |
| OP-084 | Registrar automáticamente fecha, hora, usuario y ubicación        | P0            |
| OP-085 | Guardar en el formulario dinámico correspondiente                 | P0            |
| OP-086 | Funcionar en web y en dispositivos móviles                        | P0            |
| OP-087 | Operar sin conexión guardando la conversación hasta sincronizar   | P1            |

## 25.5 Planificación — núcleo

| **ID** | **Requerimiento**                                                          | **Prioridad** | **Funcionalidad** |
|--------|----------------------------------------------------------------------------|---------------|-------------------|
| MK-001 | Crear una nueva orden operativa                                            | P0            | F01               |
| MK-002 | Programar una orden operativa manualmente                                  | P0            | F01               |
| MK-003 | Generar órdenes automáticamente desde la agenda publicada por Field Engine | P0            | F01               |
| MK-004 | Permitir programaciones recurrentes                                        | P0            | F01               |
| MK-005 | Visualizar la agenda en vista día, semana y mes                            | P0            | F02               |
| MK-006 | Consultar disponibilidad de recursos                                       | P0            | F03               |
| MK-007 | Consultar carga operacional por persona y cuadrilla                        | P0            | F03               |
| MK-008 | Detectar conflictos de programación                                        | P0            | F03               |
| MK-009 | Reasignar recursos                                                         | P0            | F03               |
| MK-010 | Actualizar automáticamente la planificación al modificar una orden         | P0            | Global            |

## 25.6 Planificación — ejecución

| **ID** | **Requerimiento**                                                  | **Prioridad** |
|--------|--------------------------------------------------------------------|---------------|
| MK-046 | Iniciar una orden operativa                                        | P0            |
| MK-047 | Solicitar automáticamente el formulario publicado por Field Engine | P0            |
| MK-048 | Construir dinámicamente la pantalla de captura                     | P0            |
| MK-049 | Registrar información usando el formulario dinámico                | P0            |
| MK-050 | Registrar evidencias (foto, video, audio, documentos, firma, GPS)  | P0            |
| MK-051 | Validar la información antes del cierre de la orden                | P0            |
| MK-052 | Actualizar la identidad digital del activo en Inventario           | P0            |
| MK-053 | Publicar eventos para Knowledge Studio                             | P0            |
| MK-054 | Cambiar automáticamente el estado de la orden                      | P0            |
| MK-055 | Mantener trazabilidad completa de la ejecución                     | P0            |

## 25.7 Planificación — seguimiento

| **ID** | **Requerimiento**                    | **Prioridad** |
|--------|--------------------------------------|---------------|
| MK-056 | Visualizar órdenes programadas       | P0            |
| MK-057 | Visualizar órdenes en ejecución      | P0            |
| MK-058 | Visualizar órdenes finalizadas       | P0            |
| MK-059 | Visualizar órdenes canceladas        | P1            |
| MK-060 | Visualizar órdenes vencidas          | P0            |
| MK-061 | Calcular cumplimiento diario         | P0            |
| MK-062 | Calcular cumplimiento semanal        | P0            |
| MK-063 | Calcular productividad por recurso   | P1            |
| MK-064 | Calcular productividad por cuadrilla | P1            |

## 25.8 Requerimientos de evolución: optimización

| **ID** | **Requerimiento**                                            | **Prioridad** |
|--------|--------------------------------------------------------------|---------------|
| MK-032 | Analizar continuamente toda la programación operacional      | P0            |
| MK-033 | Detectar conflictos de programación                          | P0            |
| MK-034 | Detectar recursos sobrecargados                              | P0            |
| MK-035 | Detectar recursos subutilizados                              | P1            |
| MK-036 | Detectar tiempos muertos entre actividades                   | P1            |
| MK-037 | Detectar actividades duplicadas                              | P1            |
| MK-038 | Detectar incumplimiento de prioridades                       | P0            |
| MK-039 | Generar propuestas automáticas de optimización               | P0            |
| MK-040 | Recomendar reasignación de recursos                          | P0            |
| MK-041 | Recomendar cambio de fecha u hora                            | P0            |
| MK-042 | Recomendar agrupación de actividades cercanas                | P1            |
| MK-043 | Recomendar división de actividades cuando hay sobrecarga     | P1            |
| MK-044 | Mostrar el impacto esperado de cada recomendación            | P0            |
| MK-045 | Permitir aceptar o rechazar recomendaciones                  | P0            |
| MK-065 | Mostrar actividades sin asignar                              | P0            |
| MK-066 | Mostrar conflictos de programación                           | P0            |
| MK-067 | Mostrar recursos sobrecargados                               | P0            |
| MK-068 | Mostrar actividades críticas                                 | P0            |
| MK-069 | Mostrar actividades próximas a vencer                        | P0            |
| MK-070 | Mostrar recomendaciones pendientes del motor de optimización | P0            |
| MK-071 | Permitir reasignar actividades                               | P0            |
| MK-072 | Permitir reprogramar actividades                             | P0            |
| MK-073 | Permitir cambiar prioridad                                   | P1            |
| MK-074 | Permitir aceptar recomendaciones automáticas                 | P0            |

Estos requerimientos se mantienen documentados pero no son parte del primer release funcional cerrado por los hallazgos.

# 26. Contrato funcional del paquete de trabajo móvil

| **Elemento**       | **Regla funcional**                                                            |
|--------------------|--------------------------------------------------------------------------------|
| Identidad de tarea | ActividadID/tarea única.                                                       |
| Formulario         | FormularioID + versión publicada.                                              |
| Clave de búsqueda  | Heredada del formulario; no editable desde la tarea.                           |
| Recurso            | Usuario/cuadrilla asignada.                                                    |
| Programación       | Fecha inicial/final y prioridad.                                               |
| Contexto           | Información de finca/proceso/actividad/segmento cuando corresponda.            |
| Población objetivo | Lista calculada por Planificación antes de la entrega.                         |
| Datos precargados  | Información necesaria para identificar y contextualizar los activos incluidos. |
| Ejecución          | El móvil no cambia la población objetivo.                                      |
| Offline            | El paquete y datos necesarios deben estar disponibles localmente.              |
| Sincronización     | Resultados se conservan localmente y se sincronizan al recuperar conexión.     |
| Trazabilidad       | Cada captura conserva tarea, formulario/versión, activo, usuario y tiempo.     |

# 27. Modelo conceptual funcional

| **Entidad**                    | **Responsabilidad**                     | **Relaciones principales**                                     |
|--------------------------------|-----------------------------------------|----------------------------------------------------------------|
| Grupo                          | Contexto empresarial opcional.          | 0..1 Grupo por Finca; múltiples Fincas.                        |
| Finca                          | Unidad operacional.                     | Puede pertenecer o no a Grupo; contiene espacios y activos.    |
| Usuario                        | Identidad de acceso.                    | Roles y ámbito de finca.                                       |
| Rol                            | Conjunto de permisos predefinido.       | Usuario → Rol.                                                 |
| Taxonomía                      | Clasificación maestra.                  | Dominio → Categoría → Tipo → Subtipo.                          |
| Plantilla                      | Definición versionable de captura.      | Tipo/criterios de elegibilidad; versiones.                     |
| Formulario                     | Versión publicada de captura.           | Usado por Inventario/Planificación.                            |
| Activo                         | Identidad operacional única.            | Identificadores, relaciones, ubicación, atributos, documentos. |
| Identificador                  | Clave externa/física.                   | Muchos → un ActivoID.                                          |
| Espacio Territorial            | Contexto físico del negocio.            | Jerarquía dentro de Finca.                                     |
| Tarea                          | Trabajo a ejecutar.                     | Formulario + población + recurso + programación.               |
| Población objetivo             | Resultado de segmentación.              | Pertenece a Tarea.                                             |
| Ejecución                      | Resultado de trabajo.                   | Tarea + Activo + usuario + captura.                            |
| Evento                         | Hecho operacional/espacial/automático.  | Fuente + timestamp + objeto.                                   |
| Regla                          | Lógica de negocio.                      | Datos → resultado.                                             |
| Indicador/Alerta/Recomendación | Resultado gobernado.                    | Regla + fuente.                                                |
| Documento/Evidencia            | Soporte del dato.                       | Activo/Ejecución/Evento.                                       |
| Ciclo de Vida                  | Modelo de evolución asociado al activo. | Activo → Ciclo → Etapas → eventos/condiciones → resultados.    |

# 28. Máquinas de estado funcionales

## 28.1 Tarea

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>Borrador → Validada → Programada → Disponible para campo → En ejecución → Pausada → Finalizada → Por validar → Cerrada<br />
↘ Cancelada (salida alterna)<br />
</strong>F3F6FA</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

## 28.2 Formulario

Borrador → Publicada → Nueva versión cuando se modifica. La versión publicada queda inmutable.

## 28.3 Usuario

Activo → Desactivado, conservando historial.

## 28.4 Activo

El documento funcional exige estados e historial, pero no fija un catálogo operativo completo de estados de activo; el catálogo concreto debe parametrizarse sin alterar la regla de no eliminación física.

# 29. Reglas de negocio transversales

| **ID** | **Regla**                    | **Definición**                                                                      |
|--------|------------------------------|-------------------------------------------------------------------------------------|
| RN-01  | Activo único                 | Nunca crear duplicado por cambiar de identificador.                                 |
| RN-02  | Código automático            | Códigos de grupo, activo y formulario se generan por secuencia y no se editan.      |
| RN-03  | Ubicación estructurada       | La ubicación de finca no es texto libre.                                            |
| RN-04  | Seguridad                    | El usuario solo accede al ámbito autorizado.                                        |
| RN-05  | Formulario inmutable         | Una versión publicada no se modifica.                                               |
| RN-06  | Planificación previa         | La población objetivo se determina antes de llegar al móvil.                        |
| RN-07  | Móvil sin segmentación       | El móvil no decide sobre quién trabajar.                                            |
| RN-08  | Recurrencia en Planificación | La periodicidad no pertenece al formulario.                                         |
| RN-09  | Clave de búsqueda heredada   | La tarea no puede eliminar/modificar la clave de búsqueda.                          |
| RN-10  | Recurso obligatorio          | Una tarea no queda disponible para campo sin recurso asignado.                      |
| RN-11  | Offline                      | La pérdida de conexión no debe perder una captura confirmada localmente.            |
| RN-12  | Trazabilidad                 | Toda ejecución conserva el vínculo con tarea, activo, usuario y formulario/version. |
| RN-13  | Conocimiento gobernado       | Un indicador/alerta/recomendación debe poder explicar regla y fuente.               |
| RN-14  | IA asistida                  | La IA no es fuente maestra ni publica reglas productivas sin validación.            |
| RN-15  | ERP                          | No duplicar procesos administrativos que correspondan a ODOO.                       |

## 29.1 Reglas específicas de Ciclo de Vida

- El ciclo se asigna al activo al crearlo y puede ser cambiado únicamente mediante una acción autorizada y trazable.

- La etapa actual es un resultado del ciclo y sus datos; no debe tratarse como un campo aislado que el usuario edita sin control.

- Las tareas referenciales, condiciones e indicadores pertenecen a la definición de la etapa.

- Knowledge decide qué desviaciones son relevantes y qué resultado producen.

- Advisory explica y recomienda; no modifica por sí mismo el ciclo ni crea una tarea sin una acción/regla autorizada.

# 30. Experiencia de usuario

## 30.1 Web de administración

- Orientada a Administrador y Supervisor.

- Organización, Inventario, Formularios, Planificación, Seguimiento y Dashboard.

- El sistema debe ocultar capacidades técnicas que corresponden al Superusuario.

- Las listas y filtros deben utilizar catálogos estructurados, no campos de texto libre donde exista un catálogo.

## 30.2 Móvil

- Trabajo primero.

- Identificación rápida.

- Contexto precargado.

- Captura de mínima fricción.

- Voz dentro del formulario.

- Alertas accesibles durante la jornada.

- Offline visible.

# 31. Trazabilidad de la operación

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>DEFINICIÓN → SEGMENTACIÓN → VALIDACIÓN → PROGRAMACIÓN → ENTREGA → EJECUCIÓN → RESULTADO → SEGUIMIENTO<br />
</strong>GANUS debe permitir reconstruir el ciclo completo de cada tarea y relacionarlo con el activo, usuario, formulario/version y evidencias.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 32. Fuera del MVP inicial

- Detalle completo de Knowledge Studio, simulador y biblioteca de widgets.

- Agente Advisory avanzado.

- TRACK como implementación avanzada de geolocalización/hardware.

- Integración profunda con ODOO.

- Balanceador de recursos y optimización avanzada.

- Canvas Builder del Dashboard.

- Modelos predictivos avanzados.

- Automatizaciones complejas no especificadas.

- Hardware/IoT como dependencia obligatoria del primer release.

# 33. Secuencia recomendada de construcción

| **Fase**            | **Resultado**                                                                  |
|---------------------|--------------------------------------------------------------------------------|
| 1\. Organizar       | Organización + identidad + taxonomía + migración.                              |
| 2\. Configurar      | Formularios dinámicos + claves + validaciones + publicación.                   |
| 3\. Planificar      | Tareas unitarias/masivas + población + previsualización + prueba + calendario. |
| 4\. Ejecutar        | Paquete móvil + ejecución + voz + evidencias + offline.                        |
| 5\. Controlar       | Seguimiento + estados + cumplimiento + trazabilidad.                           |
| 6\. Decidir         | Dashboard táctico + indicadores del onboarding + Advisory inicial.             |
| 7\. Conectar        | Knowledge, ODOO, eventos e integraciones.                                      |
| 8\. Territorializar | Gemelo Digital + espacios + geocercas + tracking.                              |
| 9\. Escalar         | Optimización, benchmarks, IA avanzada y automatización.                        |

# 34. Evaluación de calidad para uso como cerebro de desarrollo

| **Dimensión**              | **Nota** | **Evaluación**                                                                                    |
|----------------------------|----------|---------------------------------------------------------------------------------------------------|
| Visión de producto         | 9.5/10   | Propósito, diferenciación y ecosistema claramente definidos.                                      |
| Arquitectura funcional     | 9.3/10   | Responsabilidades y fronteras muy claras.                                                         |
| Decisiones MVP             | 9.2/10   | D-01 a D-10 cerradas con criterio de hallazgos.                                                   |
| Detalle funcional MVP      | 9.0/10   | Los cuatro módulos y el flujo móvil/seguimiento están suficientemente descritos.                  |
| Trazabilidad               | 9.0/10   | IDs, reglas, casos de aceptación y relaciones entre módulos.                                      |
| UX funcional               | 8.7/10   | Flujo web/móvil definido; quedan detalles visuales para diseño.                                   |
| Modelo conceptual          | 8.5/10   | Entidades y ownership claros; el ERD físico todavía es un artefacto posterior.                    |
| Contrato técnico           | 7.0/10   | Faltan payloads API/eventos, esquema físico, sincronización detallada y RBAC técnico.             |
| Autonomía para Claude Code | 8.8/10   | Excelente cerebro funcional; todavía requiere artefactos técnicos antes de codificación autónoma. |
| Calidad global             | 9.0/10   | Muy sólida como especificación funcional maestra.                                                 |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>VEREDICTO<br />
</strong>Con D-01 a D-10 cerradas, esta versión sí puede considerarse la fuente funcional de verdad de GANUS. Para Claude Code debe usarse como contrato de producto/funcionalidad, acompañado de un segundo nivel de artefactos técnicos. El agente no debe inventar una decisión funcional que no esté aquí.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 35. Reglas de uso de este documento por Claude Code

**Regla adicional crítica:** el Ciclo de Vida del Activo es parte del dominio Inventario y debe modelarse como una entidad funcional de primer nivel, no como un campo libre ni como lógica hardcodeada de la especie bovina.

60. Tratar esta v3.0 como fuente funcional de verdad del MVP.

61. Tratar la Visión como definición del ecosistema completo.

62. Respetar las decisiones D-01 a D-10 sin reabrirlas.

63. No crear módulos paralelos para conceptos que son tipos de Activo.

64. No duplicar identidad de activos.

65. No mover recurrencia de Planificación a Formularios.

66. No permitir segmentación en el móvil.

67. No exponer al cliente administración de roles/permisos.

68. No asumir optimización avanzada como requisito del primer release.

69. No implementar Track/ODOO/Knowledge avanzado como parte del MVP si no existe un backlog específico.

70. Si falta un detalle técnico, marcarlo como decisión técnica/contrato pendiente; no cambiar el comportamiento funcional.

71. Cada implementación debe poder rastrearse a un requisito, regla o caso de aceptación.

# 36. Artefactos técnicos que deben derivarse de esta especificación

| **Artefacto**               | **Propósito**                                          | **Dependencia**    |
|-----------------------------|--------------------------------------------------------|--------------------|
| ERD / modelo de dominio     | Entidades, cardinalidades, constraints y ownership.    | Este documento.    |
| Diccionario de datos        | Campos, tipos, obligatoriedad, catálogos y origen.     | ERD + requisitos.  |
| Máquinas de estado técnicas | Estados, eventos de transición y permisos.             | Sección 28.        |
| API Contract                | Endpoints, request/response, errores y versionamiento. | Modelo de dominio. |
| Event Contract              | Payload, productor, consumidores, idempotencia.        | Sección 20.        |
| RBAC Matrix                 | Permiso por rol, dominio, acción y finca.              | Sección 4/6.       |
| Offline/Sync Spec           | Dataset, versionado, cola, conflictos y recuperación.  | Sección 10/26.     |
| Migration Spec              | Formato, mapping, validación, duplicados y reportes.   | Inventario.        |
| Test Matrix                 | Requisito → caso → prueba → resultado.                 | Sección 23.        |
| Backlog técnico             | Historias, dependencias y Definition of Done.          | Todo el documento. |

# 37. Glosario oficial

**Ciclo de Vida —** Modelo parametrizable de etapas, tareas referenciales, condiciones e indicadores que permite interpretar la evolución de un activo en el tiempo.

| **Término**            | **Definición**                                                          |
|------------------------|-------------------------------------------------------------------------|
| Activo                 | Elemento físico, biológico, territorial, humano o digital administrado. |
| ActivoID               | Identidad única transversal de un activo.                               |
| Clave de búsqueda      | Mecanismo para localizar un activo.                                     |
| Clave de contexto      | Información mínima de trazabilidad de un registro.                      |
| Formulario dinámico    | Definición configurable de captura.                                     |
| Planificación          | Generación, segmentación, programación y asignación de trabajo.         |
| Población objetivo     | Conjunto de activos resultante de la segmentación de una tarea.         |
| Paquete de trabajo     | Conjunto de definición, contexto y población que recibe el móvil.       |
| Espacio Territorial    | Unidad geográfica del negocio.                                          |
| Geocerca               | Condición geométrica para detectar eventos.                             |
| Seguimiento Operativo  | Consulta y control del avance y resultado de tareas.                    |
| Centro de Conocimiento | Gobierno de reglas y conocimiento.                                      |
| Advisory               | Interfaz conversacional de interpretación y recomendación.              |
| Dashboard táctico      | Vista de indicadores y operación para administración/supervisión.       |
| Superusuario           | Usuario técnico del equipo GANUS, oculto para clientes.                 |
| Offline                | Capacidad de ejecutar y capturar sin conectividad.                      |

# 38. Trazabilidad documental

| **Fuente**                                        | **Uso**                                            |
|---------------------------------------------------|----------------------------------------------------|
| GANUS_Vision_Alcance_Unificado_Ecosistema_v2.0_ES | Arquitectura y ecosistema.                         |
| GANUS_Documento_Funcional_Alcance_MVP             | Criterio de cierre de hallazgos y alcance MVP.     |
| GANUS_Organizacion                                | Detalle de gobierno.                               |
| GANUS_Inventario                                  | Detalle de activos.                                |
| GANUS_Formularios                                 | Detalle de formularios y captura.                  |
| GANUS_Planificacion                               | Detalle de planificación, ejecución y seguimiento. |

Actualización v3.1: incorporación del Ciclo de Vida de Activos como capacidad transversal del Inventario, integrada con Planificación, Centro de Conocimiento y Advisory.

# 39. Definición final oficial

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>GANUS<br />
GANUS transforma la finca ganadera en una empresa organizada, controlada, trazable y basada en datos. El activo es la identidad transversal; los formularios definen cómo capturar; Planificación define sobre quién, qué, cuándo y quién ejecuta; el móvil ejecuta; Seguimiento controla; el conocimiento convierte datos en resultados; Analytics presenta; Advisory interpreta; ODOO gestiona la dimensión empresarial; Track territorializa; y la Plataforma de Datos integra el histórico.</strong></th>
</tr>
</thead>
<tbody>
</tbody>
</table>

Mensaje de producto: el siguiente nivel del negocio ganadero: más control, más trazabilidad, más visión y mejores decisiones, sin perder la experiencia del campo.

| **Caso** | **Prueba**                    | **Criterio de aceptación**                                                                          |
|----------|-------------------------------|-----------------------------------------------------------------------------------------------------|
| CP-CV-01 | Asignar ciclo al crear activo | El activo queda vinculado a un CicloID y la relación es visible en su ficha.                        |
| CP-CV-02 | Determinar etapa actual       | GANUS identifica y muestra la etapa actual según las reglas/fechas/datos configurados.              |
| CP-CV-03 | Mostrar guía de etapa         | La ficha muestra tareas referenciales, condiciones e indicadores aplicables.                        |
| CP-CV-04 | Evaluar desviación            | Knowledge puede comparar lo esperado con lo registrado y producir el resultado configurado.         |
| CP-CV-05 | Advisory contextual           | Advisory puede explicar etapa, situación, desviaciones y recomendaciones basadas en reglas y datos. |
| CP-CV-06 | Historial de transición       | Cada cambio de etapa conserva fecha, fuente/evento y trazabilidad.                                  |
