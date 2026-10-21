---
name: calcular-dataset-poblacion
description: Calcula, muestra y congela el dataset (población objetivo) de una tarea GANUS a partir de los filtros que habilita el formulario, consultando a Inventario por contrato y nunca por sus tablas. Usar al implementar la selección de población de tareas unitarias o masivas, los filtros de Planificación, la tabla task_target o la vista de incluidos y excluidos.
---

# Calcular dataset de población

## Propósito
Responder "¿sobre quién se ejecuta la tarea?". El dataset pertenece a la tarea, no al formulario, y se calcula
**solo en Planificación** (D-09). Mobile lo recibe y no lo cambia.

## Fuentes
- 05 §6 (conceptos), §10 pasos 4–5, §12 (selección y construcción del dataset), §27 reglas 15, 17 y 18.
- 04 §6.2, §6.4, §6.6, §10, §21.
- 10 §12 (`task_target`: población calculada y congelada), §16, §27 (índices).
- 09C §5 (Inventario → Planificación: AssetReference), §10.2, §10.4.
- CONST §7.4; MC §21 regla 24 (no asumir bovinos).

## Cuándo usarla
- Al construir el paso "Criterios → Dataset" del asistente de tarea.
- Al implementar la consulta a Inventario.
- Al diseñar `task_target`.

## Conceptos
- **Clave:** dimensión habilitada por el formulario (sexo, edad, raza…).
- **Filtro / criterio:** condición concreta sobre una clave (`Sexo = Hembra`).
- **Dataset:** activos que cumplen los criterios.
- **CLAVE ≠ FILTRO** (04 §10, 05 §12).

## Tipos de tarea
| Tipo | Dataset |
| --- | --- |
| Unitaria | Un activo identificado por una clave de búsqueda (05 §6) |
| Masiva | Resultado de combinar criterios (05 §6) |

## Reglas
1. Solo se puede filtrar por dimensiones que la versión del formulario declara como filtros disponibles (04 §10).
2. Los valores y operadores los pone Planificación (04 §6.4).
3. Se pueden combinar varios criterios (05 §12). Operadores y combinación O: **DP-03**.
4. Los activos se obtienen **por contrato de Inventario** (AssetReference / consulta autorizada), nunca leyendo
   tablas de `inventory` (CONST §10, 09C §1).
5. Siempre dentro de la finca de la tarea y del ámbito autorizado del usuario (09C §14).
6. El resultado muestra **cantidad y detalle**, y permite revisar incluidos y, cuando aplique, excluidos o
   inconsistentes (05 §12).
7. Una tarea masiva **no se puede confirmar sin dataset calculado y visible** (05 regla 15).
8. Al publicar, el dataset queda **congelado** en `task_target` y es parte de la trazabilidad (05 regla 17, 10 §12).
9. Mobile no recalcula, amplía ni reduce el dataset (D-09, 09C §10.4).
10. No se asume que los activos son animales: el cálculo funciona para cualquier tipo de activo (MC regla 24).
11. Cambios en Inventario después de publicar no recalculan la tarea automáticamente (05 §4). Si un activo ya no
    puede atenderse, se registra en su resultado individual (no ejecutado o cancelado con causa).

## Qué se guarda al congelar (`task_target`)
- `task_id`, `asset_id` y estado inicial del resultado por activo (`Pendiente`).
- Los criterios aplicados (dimensión, operador, valor) y el `formVersionId` usado.
- Cantidad objetivo, fecha y hora del cálculo, actor.
- Una referencia a la versión o instante de los datos de Inventario consultados (propuesta técnica, para explicar
  después por qué un activo entró).
- **No** se guarda una copia editable de la identidad del activo (10 §33). Los datos que viajan al móvil son una
  instantánea de solo lectura dentro del paquete (`construir-paquete-trabajo`).
Índices: `task_target (task_id, asset_id)`, `task_target (task_id, status)` (10 §27).

## Procedimiento
1. Leer de la versión del formulario las claves y los filtros disponibles.
2. Mostrar al usuario solo esos filtros.
3. Recibir los criterios y validarlos (dimensión permitida, tipo de valor correcto).
4. Pedir a Inventario los activos que cumplen, con paginación, dentro de la finca.
5. Mostrar la cantidad, el detalle y los excluidos o inconsistentes que Inventario informe.
6. Permitir recalcular mientras la tarea no esté publicada.
7. Al confirmar y publicar (`validar-publicacion-tarea`), congelar en `task_target` dentro de la misma transacción.

## Ejemplo (04 §30)
Formulario EVAL-REPRO v3, filtros disponibles: Sexo, Edad, Estado, jerarquía física.
Criterios: `Sexo = Hembra`, `Edad >= 24 meses`, `Estado = Activo`, `Sector = Norte`, `Potrero = P-03`.
Resultado: N activos, que se muestran antes de confirmar y se congelan al publicar.

## Prohibido
- `JOIN` o lecturas directas a tablas de Inventario.
- Filtros sobre dimensiones que el formulario no habilitó.
- Que Mobile modifique o amplíe la población.
- Recalcular en silencio el dataset de una tarea publicada.
- Columnas o lógica específicas de bovinos.

## Decisiones pendientes que afectan esta skill
- **DP-03:** contrato de consulta a Inventario (operadores, Y/O, edad y ubicación calculadas). Hasta resolverlo:
  definir el puerto `ConsultaPoblacionPort` en Application y una implementación con solo igualdad y combinación Y.
- **DP-15:** ¿se excluyen solos los activos inactivos? Hasta resolverlo: no excluir implícitamente; el usuario
  agrega el filtro de estado.
- **DP-04:** recurrencia (¿congelar por ocurrencia?).
- **DP-10:** varios tipos de activo en un dataset; tareas sin activo.
- **DP-01:** "muestra por porcentaje" (v3.1 §9.2) no está en el 05. No se implementa hasta resolverlo.

## Verificación
- Un filtro sobre una dimensión no habilitada → VALIDATION_ERROR.
- Criterios → cantidad y detalle correctos.
- Un usuario de otra finca no obtiene activos ajenos.
- Publicar congela `task_target`; un cambio posterior en Inventario no lo modifica.
- No existen consultas a tablas de `inventory` desde `planning` (prueba de arquitectura).
