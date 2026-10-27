# GANUS — Registro de decisiones pendientes (Formularios, Planificación y STAGING)

Este registro concentra las decisiones que **ningún documento resuelve**, o en las que **dos documentos se contradicen**.
Las skills de `.claude/skills/` las citan por su código `DP-xx`.

**Regla para Claude Code y para el equipo (Constitución §2 y §16):** mientras una decisión esté `ABIERTA`,
no se implementa el comportamiento afectado ni se inventa una regla para completarlo. Se implementa lo que sí
está definido y se deja la decisión visible aquí. Una "Propuesta" es solo una recomendación: no se aplica hasta
que el dueño la apruebe.

| Tipo | Significado |
| --- | --- |
| Funcional | La decide Producto. Cambia el comportamiento del sistema. |
| Técnica | Se resuelve con un ADR (Constitución §17). No cambia el comportamiento funcional. |

## Resumen

| Código | Tema | Tipo | Decide | Skills afectadas | Estado |
| --- | --- | --- | --- | --- | --- |
| DP-01 | Qué documento manda: 04/05 frente a v3.1, 00 y Master Context | Funcional | Producto | 1, 6, 8 | ABIERTA |
| DP-02 | Claves de contexto: cuáles son y cómo encajan con D-04 | Funcional | Producto | 6, 7 | ABIERTA |
| DP-03 | Contrato de consulta de población a Inventario | Funcional + Técnica | Inventario + Planificación | 2, 8 | ABIERTA |
| DP-04 | Recurrencia: instancias, dataset y versión del formulario | Funcional | Producto | 7, 8, 10 | ABIERTA |
| DP-05 | Disparadores de transición de estados de la tarea | Funcional | Producto | 9, 10 | ABIERTA |
| DP-06 | Dueño y estados del ciclo de la alerta | Funcional | Producto | 11 | ABIERTA |
| DP-07 | Contenido y formato del paquete de trabajo | Funcional + Técnica | Planificación + STAGING + Mobile | 6, 13, 14 | ABIERTA |
| DP-08 | Dónde vive el mapeo campo → atributo maestro | Funcional | Inventario + Formularios | 2, 6, 14 | ABIERTA |
| DP-09 | Coexistencia de versiones, obsolescencia y borradores | Funcional | Producto | 7 | ABIERTA |
| DP-10 | Varios tipos de activo, potrero (espacio o activo) y tareas sin activo | Funcional | Producto | 6, 8 | ABIERTA |
| DP-11 | Lenguaje de condiciones entre campos y secciones repetibles | Funcional + Técnica | Formularios + Mobile | 6, 7 | ABIERTA |
| DP-12 | Unidad de medida en campos y límites de evidencia | Funcional | Producto | 4, 6 | ABIERTA |
| DP-13 | ¿La web ejecuta formularios o solo los previsualiza y prueba? | Funcional | Producto | 6, 13 | ABIERTA |
| DP-14 | Cómo decide Mobile el mecanismo de captura de cada campo | Funcional | Mobile + Formularios | 6, 13 | ABIERTA |
| DP-15 | Exclusión automática de activos inactivos en el dataset | Funcional | Producto | 8 | ABIERTA |
| DP-16 | Capacidad: duración estimada, disponibilidad, umbrales y cuadrilla | Funcional | Producto | 9 | ABIERTA |
| DP-17 | Reprogramar o reasignar una tarea ya publicada | Funcional | Producto | 10, 13 | ABIERTA |
| DP-18 | Casos de sincronización: rechazo del servidor, recapturas, activo fuera del dataset | Funcional | Producto | 14 | ABIERTA |
| DP-19 | Formulario estándar de alerta: ¿plantilla de Formularios o fijo? | Funcional | Producto | 6, 11 | ABIERTA |
| DP-20 | Catálogo de causas de no ejecución/cancelación: quién lo parametriza | Funcional | Producto | 13, 14 | ABIERTA |
| DP-21 | Estados de mensaje de STAGING (09 frente a Master Context) | Técnica | Arquitectura (ADR) | 12 | ABIERTA |
| DP-22 | Esquemas físicos para Mobile (servidor), Control, Knowledge y Advisory | Técnica | Arquitectura (ADR) | 2, 14 | ABIERTA |
| DP-23 | Matriz de permisos por rol en Formularios y Planificación | Funcional | Producto | 5, 6, 7, 9 | ABIERTA |
| DP-24 | Alcance de un formulario: organización, grupo o finca | Funcional | Producto | 6 (esquema `forms`) | ABIERTA |
| DP-25 | Idioma de nombres de tablas y columnas (español del equipo o inglés del documento 10) | Técnica | Arquitectura (ADR) | Modelo de datos | ABIERTA |
| DP-26 | Formularios para definir estructuras que no se pueden estandarizar | Funcional | Producto | 6 | ABIERTA |

## Detalle

### DP-01 — Qué documento manda
**Conflicto.** El 04 v1.3 y el 05 v1.0 son más recientes y precisos que la v3.1, el 00 y el Master Context, pero
la Constitución (§24) y el 00 (§14) nombran la v3.1 como criterio de cierre.

| Tema | v3.1 / 00 / Master Context | 04 v1.3 / 05 v1.0 / 06 / 10 |
| --- | --- | --- |
| Métodos de captura por campo | Sí (FE-004, 00 §8, 03) | No: pertenecen a Mobile (04 §5, §12; 06 §10; 10 §10) |
| Tipos "ubicación" y "activo relacionado" | Sí (v3.1 §8.4) | No (04 §12) |
| Agenda/recurrencia en el formulario | FE-008, MK-003 | No: D-03, 05 §26 |
| Crear tipos de activo y de actividad desde Formularios | FE-001 | No: Organización e Inventario (D-05, 02) |
| Muestra por porcentaje en tarea masiva | Sí (v3.1 §9.2) | No se menciona (05 §12) |
| Calendario | Día / semana / mes | Día / semana / lista |
| Replanificación automática al modificar una orden | MK-010 (P0) | Fuera del MVP (05 §4) |

**Propuesta.** Para Formularios y Planificación mandan 04 v1.3 y 05 v1.0. Se corrigen 00, Master Context y v3.1.

### DP-02 — Claves de contexto
v3.1 §8.3: activo principal, responsable, ubicación, fecha y código del registro.
04 §9: proceso, tipo de actividad y segmentación física (de Organización).
D-04: "el formulario no asigna procesos ni actividades a la plantilla".
**Pregunta.** ¿La clave de contexto "proceso/actividad" del 04 es solo una dimensión que se resuelve en la tarea
(compatible con D-04), o contradice D-04?
**Información recibida (2026-10-27).** Los procesos se establecen en Organización y Formularios los trae como
contexto. Organización debe agregar la relación finca–proceso (ver `modelo-datos/README.md`). Sigue abierto si esto
es compatible con D-04.

### DP-03 — Contrato de consulta de población a Inventario
Falta definir: operaciones de filtro (=, ≠, >, ≥, <, ≤, entre, en lista), combinación (solo Y, o también O),
atributos calculados (edad desde fecha de nacimiento, ubicación actual), paginación y si se consulta por API en línea
o por una proyección de lectura. La Constitución prohíbe consultar tablas de Inventario.

### DP-04 — Recurrencia
v3.1 §9.3: "cada ocurrencia recurrente puede reevaluar las condiciones de elegibilidad".
05 §26: "conservar la trazabilidad de cada instancia".
**Preguntas.** ¿Cada ocurrencia es una tarea propia con su estado? ¿El dataset se congela por ocurrencia o al crear la
serie? Si se publica una nueva versión del formulario, ¿la usan las ocurrencias futuras?

### DP-05 — Disparadores de transición de la tarea
Los estados están cerrados (D-06), pero no qué los dispara:
- Validada → Programada: ¿acción del usuario o automática al confirmar fecha?
- Programada → Disponible para campo: ¿al publicar o al llegar la fecha?
- ¿Quién pausa y reanuda?
- Finalizada → Por validar: ¿automática al sincronizar todos los resultados?
- Por validar → Cerrada: ¿puede cerrarse con activos en Pendiente? ¿Qué estados por activo son "compatibles con el cierre" (10 §34)?
- Cancelada: ¿desde qué estados?

### DP-06 — Dueño y estados de la alerta
05 §29 dice que Planificación produce "alertas de campo y su ciclo". 07 §13, 09 §10.6 y 10 §20 ubican la
interpretación en Control/Knowledge. 06 §19 define estados de envío; 05 §25 menciona atendida, convertida,
resuelta y descartada sin una máquina de estados.

### DP-07 — Paquete de trabajo
09 §10.4 da un mínimo. 06 §7 y 05 §16 piden además campos, validaciones, precarga, condiciones y datos offline.
Falta acordar: estructura completa, qué datos del activo viajan (AssetSnapshot), si los catálogos van dentro o
aparte, y quién es dueño del contenido frente al sobre. Ver propuesta v0 en la skill `construir-paquete-trabajo`.

### DP-08 — Mapeo campo → atributo maestro
03 §6.0 y 10 §11: solo los campos con mapeo explícito y autorizado actualizan atributos del activo.
Falta: ¿el mapeo se configura en el campo del formulario (Formularios) o en Inventario? ¿Quién lo aprueba?
**Información recibida (2026-10-27).** La raza y otras características del activo se capturan con formularios
dinámicos (formulario de levantamiento, 03 §6.0). Formularios no define la plantilla del activo. Sigue abierto dónde
vive el mapeo que actualiza el atributo maestro.

### DP-09 — Versiones del formulario
¿Pueden coexistir dos versiones publicadas? ¿Publicar v2 vuelve obsoleta a v1 automáticamente? ¿Se permite más de
un borrador abierto por formulario? ¿Quién marca Obsoleto?

### DP-10 — Tipos de activo, potrero y tareas sin activo
¿Un formulario aplica a varios tipos de activo? ¿Un dataset puede mezclar tipos? El potrero es espacio en
Organización (02) y puede ser activo territorial en la taxonomía (03, 10 §7). ¿Existen tareas sin activo
(05 §6 habla de "activo o unidad")?
**Propuesta.** Para el MVP: un formulario por tipo de activo y datasets de un solo tipo.

### DP-11 — Lenguaje de condiciones
04 §4 permite "dependencias sencillas" y "obligatoriedad condicional". Web y Mobile deben evaluarlas igual.
Falta la gramática cerrada (operadores, Y/O) y si existen secciones repetibles.
**Propuesta.** Ver la gramática mínima en la skill `disenar-plantilla-formulario`.

### DP-12 — Unidad de medida y límites de evidencia
El 04 no define unidad para Decimal/Entero (la voz la necesita: "432 kilos"), ni mínimos/máximos de fotos,
duración de video o tamaño de archivo.

### DP-13 — Canal web
04 §18 nombra "Mobile/Web – Ejecución". ¿Alguien captura resultados reales desde la web o la web solo hace vista
previa y "Probar tarea"? Define cuántos renderers completos existen.

### DP-14 — Mecanismo de captura en Mobile
Si manda el 04 (DP-01), el formulario no dice "usar báscula" o "usar RFID". ¿Mobile lo deduce del tipo de campo y
de la clave de búsqueda?

### DP-15 — Activos inactivos
¿El cálculo del dataset excluye automáticamente activos inactivos o dados de baja, o solo si el usuario agrega el
filtro "Estado = Activo" (04 §10)?

### DP-16 — Capacidad
05 §14 exige clasificar Normal / Alta carga / Sobrecarga, pero no define umbrales, de dónde sale la duración
estimada (por tipo de actividad, por activo, manual) ni dónde se registra la disponibilidad del operador.
"Cuadrilla" aparece (10 §12, MK-007) sin entidad en Organización.

### DP-17 — Cambios a una tarea publicada
¿Se puede reprogramar o reasignar después de "Disponible para campo"? ¿Genera un nuevo paquete (taskVersion + 1)?

### DP-18 — Casos de sincronización
- El servidor rechaza un valor que Mobile aceptó offline: ¿se guarda como "Ejecutado con inconsistencia" o se rechaza?
- ¿Un activo puede tener más de una captura en la misma tarea (corrección)? ¿Cuál vale?
- El operador encuentra un activo que no está en el dataset (D-09 impide capturarlo): ¿lo reporta como alerta?

### DP-19 — Formulario estándar de alerta
05 §24 lo describe como "formulario estándar predefinido de GANUS". ¿Es una plantilla del módulo Formularios
(mismo renderer) o una estructura fija del sistema?

### DP-20 — Causas de no ejecución
05 §18 y 06 §15 listan causas más "otra causa parametrizada". ¿Quién mantiene ese catálogo y en qué dominio vive?

### DP-21 — Estados de mensaje STAGING
09 §8.1: RECEIVED → VALIDATED → PROCESSING → PROCESSED / RETRYING / REJECTED → DLQ.
Master Context §5.2: PENDIENTE → RECIBIDO → VALIDADO → PROCESADO → CONFIRMADO + ERROR / RECHAZADO / REINTENTO.
**Propuesta.** Usar los del 09 (documento de contratos) más un estado de confirmación (ACK) hacia el productor.

### DP-22 — Esquemas físicos
La Arquitectura §8 define 7 esquemas: organization, inventory, forms, planning, staging, audit, reporting.
El 10 asigna entidades a Mobile (servidor), Control, Knowledge, Advisory y Transversal sin esquema definido.

### DP-23 — Permisos por rol
09 Funcionamiento §3: Administrador gestiona formularios y planificación; Supervisor planifica, valida, publica y
controla. Falta la matriz exacta: quién crea, publica y obsoleta formularios; quién crea, valida, publica, cancela
y cierra tareas; qué ve el Dueño.

### DP-24 — Alcance de un formulario
Ningún documento dice si un formulario pertenece a una finca, a todo un grupo empresarial o a la organización.
Afecta la seguridad (quién lo ve) y la reutilización entre fincas del mismo grupo (D-01).
**Propuesta.** El formulario pertenece a la organización (tenant) y puede limitarse a una finca (`finca_id` opcional).

### DP-25 — Idioma de nombres en la base de datos
Los diagramas del equipo usan nombres en español (`tarea`, `version_formulario`). El documento 10 y los contratos
del 09 usan inglés (`task`, `form_version`, `TaskExecutionResult`). La Constitución §19 prohíbe renombrar entidades
oficiales por estética, así que hay que elegir uno y fijarlo en un ADR.
**Propuesta.** Elegir uno solo para tablas y columnas. Los códigos de valores (estados, tipos) se mantienen como en
los contratos (`PUBLISHED`, `SHORT_TEXT`) para que base, API y móvil usen los mismos.

### DP-26 — Formularios para definir estructuras que no se pueden estandarizar
**Información recibida (2026-10-27).** "Cualquier cosa que queramos definir en el sistema que no se pueda
estandarizar la manejamos con formularios dinámicos". El usuario confirma que esto incluye definir estructuras, no
solo capturar información.
**Conflicto.** El 04 §2 dice que Formularios "no debe convertirse en un administrador de maestros" y el 04 §5
excluye crear copias de maestros dentro del formulario.
**Falta.** Qué estructuras concretas se definen con Formularios (atributos, catálogos u otras), cómo se distinguen
de los maestros de Organización e Inventario, y actualizar el 04 en consecuencia.
**Mientras esté abierta.** No se implementa la definición de estructuras con Formularios más allá de lo que dice el 04.
