---
name: versionar-formulario
description: Gestiona el ciclo de versiones de los formularios dinámicos GANUS (Borrador, Publicado, Obsoleto), la validación antes de publicar, la inmutabilidad de lo publicado, la creación de nuevas versiones y la relación de cada tarea con su versión exacta. Usar al publicar, editar, copiar u obsoletar un formulario, o al decidir qué versión usa una tarea.
---

# Versionar formulario

## Propósito
Garantizar que cada resultado de campo pueda interpretarse con la versión exacta del formulario con que se capturó.

## Fuentes
- 04 §24 (versionamiento), §17 (opciones por versión), §25 reglas 28 y 32.
- 05 §11 (acceso rápido al constructor), §27 reglas 13, 16 y 29.
- 10 §9, §16 (versión de formulario), §26 (integridad), §34 (publicar formulario).
- 09C §6 (FormVersionPublished.v1), §10.3 (FormVersionReference.v1), §17 (CT-FRM-01).
- 06 §24 (cambios posteriores a la descarga).

## Cuándo usarla
- Al implementar "Publicar", "Nueva versión" u "Obsoletar".
- Al editar un formulario.
- Al decidir qué versión usa una tarea.

## Estados (04 §24)

| Estado | Significado | Se puede editar | Se puede usar en una tarea nueva |
| --- | --- | --- | --- |
| Borrador | En construcción | Sí | No |
| Publicado | Versión cerrada | **No, nunca** | Sí |
| Obsoleto | Retirada para trabajo nuevo | No | No; sigue visible para trazabilidad |

Transiciones: Borrador → Publicado → Obsoleto. No hay vuelta atrás.

## Reglas
1. **Una versión publicada es inmutable**, se haya usado o no (04 §24, 10 §26). La regla no es "si ya se usó".
2. Todo cambio a una versión publicada crea una **versión nueva** en Borrador, con `versionNo + 1`, copiando la última
   publicada (04 §24, 05 §11).
3. La identidad del formulario (`formId`) es estable; cada versión tiene su `formVersionId`.
4. Un campo que sigue siendo el mismo conserva su `fieldId` entre versiones, para comparar resultados históricos.
5. Las opciones de catálogo usadas en resultados no se borran; se desactivan (04 §17).
6. Una tarea guarda el `formVersionId` exacto y **nunca** se cambia en silencio (10 §16, 05 regla 29).
7. Una tarea en construcción puede elegir explícitamente una versión nueva; las tareas ya publicadas conservan la suya (05 §11).
8. Un paquete ya descargado no cambia por una versión nueva (06 §24).
9. Un formulario obsoleto no se usa en tareas nuevas, pero sus resultados siguen consultables (04 §24).
10. Los registros no se eliminan físicamente (05 regla 28).

## Validación antes de publicar
Bloquean la publicación:
- La versión no tiene nombre o no tiene ningún campo.
- Un campo no tiene `key` única dentro de la versión o no tiene `order`.
- Un campo de selección no tiene catálogo con al menos una opción activa.
- Una condición se refiere a un campo inexistente (o posterior, si se aprueba la propuesta de DP-11).
- Una clave de búsqueda ya no está publicada como apta por Inventario.
- Una clave de contexto apunta a una dimensión inexistente en Organización.
- Una validación no corresponde al tipo de campo (por ejemplo `min` en un `BOOLEAN`).

## Procedimiento

### Publicar
1. Ejecutar la validación anterior.
2. En una transacción: cambiar la versión a Publicado con `publicado_por` y `publicado_en` (no hay tabla aparte de
   publicaciones) y escribir FormVersionPublished.v1 en el Outbox.
3. A partir de ese momento cualquier intento de edición devuelve CONFLICT.

### Crear nueva versión
1. Tomar la última versión publicada.
2. Copiar el tipo de activo, las claves, las secciones, los campos y las opciones a una versión nueva en Borrador
   (`versionNo + 1`), con ids nuevos.
3. Conservar el `codigo` de los campos y opciones que no cambian de significado: es lo que permite comparar
   resultados entre versiones. Una opción que ya no se ofrece simplemente no se copia.

### Obsoletar
1. Un usuario autorizado (DP-23) marca la versión como Obsoleto.
2. Las tareas existentes no cambian.
3. El selector de formularios de Planificación deja de ofrecerla.

## Contrato publicado (09C §10.3)
```json
{ "formId": "uuid", "formVersionId": "uuid", "status": "PUBLISHED",
  "context": { "processId": "...", "activityTypeId": "..." } }
```
Según DP-02 (acordada, por confirmar con Producto), proceso y actividad no son parte del formulario: los fija la
tarea. El evento debería llevar el tipo de activo y los niveles de contexto; el bloque `context` del 09C §10.3 queda
como observación a STAGING. El evento FormVersionPublished.v1 lo consume Planificación.

## Prohibido
- Editar o borrar una versión publicada.
- Reemplazar la versión de una tarea existente.
- Editar el formulario desde la tarea (05 §4, regla 16).
- Borrar opciones de catálogo usadas en resultados.

## Decisiones pendientes que afectan esta skill
- **DP-09:** ¿pueden coexistir dos versiones publicadas? ¿Publicar v2 vuelve obsoleta a v1? ¿Un solo borrador por
  formulario? Mientras esté abierta: no obsoletar automáticamente; permitir varias publicadas; la tarea elige.
- **DP-04:** qué versión usan las ocurrencias futuras de una tarea recurrente.
- **DP-23:** quién publica y quién obsoleta.

## Verificación
- Editar una versión publicada → error CONFLICT, sin cambios.
- Publicar emite exactamente un FormVersionPublished.v1 (y ninguno si la transacción falla).
- Nueva versión → `versionNo + 1`, en Borrador, con los mismos `fieldId` donde corresponde.
- Publicar v2 no cambia la versión de tareas existentes.
- Una versión obsoleta no aparece para tareas nuevas y sus resultados siguen consultables.
