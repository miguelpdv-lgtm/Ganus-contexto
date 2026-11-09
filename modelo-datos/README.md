# Modelo de datos GANUS Operativo — esquemas por módulo

Cada responsable de módulo entrega el **diagrama entidad-relación (ERD)** de su esquema. Un ERD muestra las tablas,
sus columnas, sus claves y cómo se relacionan. Es el paso entre el modelo lógico del documento 10 y el código:

```
Documento 10 (lógico) → ERD por esquema (este directorio) → entidades Code First → migraciones
```

Sirve para revisar en equipo, antes de programar, que cada tabla esté en el esquema de su dueño, que no falte
información de los documentos funcionales y que los módulos se conecten por referencias y contratos, no por tablas
compartidas.

| Esquema | Responsable | Documento |
| --- | --- | --- |
| `forms` | Formularios y Planificación | [formularios.md](formularios.md) |
| `planning` | Formularios y Planificación | [planificacion.md](planificacion.md) |
| `organization`, `inventory`, `staging`… | Otros responsables | Pendiente |

## Convenciones comunes (propuesta para acordar en ADR)

| Tema | Convención | Fuente |
| --- | --- | --- |
| Identificadores | `uuid` en todas las PK. Recomendado UUID v7, que el móvil puede generar offline | 10 §3, §39 |
| Referencias entre dominios | **Referencia lógica**: columna `uuid` sin FK física. Se valida en la aplicación. Una FK física entre esquemas solo con ADR | CONST §10, ARQ §8 |
| FK dentro del mismo esquema | Sí, siempre | 10 §26 |
| Tenant y finca | Toda tabla raíz lleva `organizacion_id` y, cuando aplique, `finca_id` | 10 §3, 09C §14 |
| Estados | Código controlado (`CHECK` o enum), nunca texto libre | 10 §3, §33 |
| Fechas | `timestamptz` en UTC | 10 §3 |
| Auditoría | `creado_por`, `creado_en`, `actualizado_en` en tablas editables | 10 §3 |
| Concurrencia | Columna `revision` en entidades editables | 10 §16 |
| Borrado | Nunca físico en datos con trazabilidad; estado o baja lógica | 10 §30 |
| JSONB | Solo para estructuras realmente variables (reglas, condiciones, snapshots); nunca para esconder entidades | 10 §3, §33 |
| Idioma de nombres | Los diagramas del equipo usan español; el documento 10 y los contratos usan inglés. Decidir uno en ADR (DP-25) | CONST §19 |

En los diagramas, el comentario **"ref lógica → esquema.tabla"** indica una referencia a otro dominio sin FK física.

## Observaciones a otros módulos que afectan a Formularios y Planificación

Del borrador de **Inventario** recibido:
1. No hay dónde marcar qué atributos tienen **aptitud de búsqueda**. Formularios solo puede elegir claves que
   Inventario publique como aptas (04 §8). Los atributos están dentro del JSON de `version_plantilla_activo`; hace
   falta una tabla de atributos de plantilla o un contrato que los publique con esa marca.
2. `valor_atributo_activo.valor_tipado` es `text`. Para filtrar por "Edad ≥ 24" o "Peso > 400" el contrato de
   consulta necesita valores tipados (DP-03).
3. Las FK hacia Organización (`finca_id`, `espacio_id`) deben ser referencias lógicas, según la convención anterior.

Para **Organización** (información recibida el 2026-10-27):
1. Falta la relación **finca–proceso**: qué procesos tiene cada finca. Hoy el proceso no está ligado a una finca
   (02 §11). Formularios la necesita para ofrecer los procesos como claves de contexto, y Planificación para listar
   solo los procesos de la finca al crear una tarea (05 §10, paso 1). Dirigida al responsable de Organización.

Para **STAGING y Mobile** (ejercicio «Pesaje», 2026-11-09):
1. El contrato `TaskExecutionResult` (09C §10.5) no tiene campos para la **causa** ni la **observación** de una no
   ejecución o cancelación, que el 05 §18 exige. Planificación los guarda en `planning.captura` (`causa_codigo`,
   `observacion`). Dirigida a los responsables de STAGING y Mobile.
2. El contrato `FormVersionPublished` (09C §10.3) lleva `context: {processId, activityTypeId}`. Con DP-02 acordada,
   el formulario no tiene proceso ni actividad (los fija la tarea); el evento debería llevar el tipo de activo
   (`assetTypeId`) y los niveles de espacio de contexto. Dirigida al responsable de STAGING.

Del borrador de **Ciclo de vida**:
1. `tarea_etapa_ciclo_vida` es una **tarea referencial**: guía a Planificación, pero no crea tareas solas
   (CONST §7.2, 10 §8). Planificación necesita un contrato para consultarlas.
2. `condicion_ciclo_vida.accion_al_fallar` (ALERTA, REVISE, TRANSICION) es lógica de reglas; según CONST §7.2 y
   10 §8, las condiciones las evalúa Knowledge. Conviene validar con ese responsable.
