---
name: procesar-sincronizacion-resultados
description: Procesa en el núcleo los resultados que el móvil envía por STAGING (TaskExecutionResult), validándolos, evitando duplicados, detectando conflictos, actualizando el resultado por activo y el avance de la tarea, vinculando evidencias y pasando a Inventario solo los campos con mapeo autorizado. Usar al implementar la recepción de capturas, la sincronización offline o el avance de tareas en Planificación.
---

# Procesar sincronización de resultados

## Propósito
Que todo lo capturado en campo llegue al núcleo **una sola vez, completo y trazable**, aunque el móvil lo envíe
tarde, desordenado o repetido.

## Fuentes
- 09C §9 (reglas de sincronización), §10.5 (TaskExecutionResult), §12.3 (resultado de campo), §13 (idempotencia y
  concurrencia), §19 (CI-008, CI-009), §20.
- 06 §14 (resultado por activo), §15 (cancelación), §16 (evidencias), §20–§23 (sincronización, estados, tiempos).
- 05 §17–§20 (resultado, cancelación, retorno, control), §27 reglas 18–24.
- 10 §11 (`form_execution_result`, `form_execution_value`, `form_execution_evidence`, `form_execution_validation`,
  `asset_form_result_link`), §12 (`task_asset_result`, `task_exception`), §34.
- 03 §6.0 (actualización maestra solo con mapeo autorizado).

## Cuándo usarla
- Al consumir TaskExecutionResult.v1.
- Al actualizar el avance de una tarea.
- Al vincular evidencias.
- Al propagar datos a Inventario.

## Contrato de entrada — mínimo del 09C §10.5, ampliado como propuesta (DP-07, DP-18)
```json
{
  "captureId": "uuid",
  "taskId": "uuid",
  "taskVersion": 1,
  "assetId": "uuid",
  "formVersionId": "uuid",
  "resultStatus": "SUCCESS",
  "cause": null,
  "startedAt": "UTC",
  "capturedAt": "UTC",
  "capturedBy": { "userId": "uuid", "deviceId": "uuid" },
  "values": { "<fieldId>": 412.0 },
  "evidence": [ { "evidenceId": "uuid", "fieldId": "uuid" } ],
  "source": "MOBILE_OFFLINE"
}
```
`resultStatus`: `SUCCESS` (Ejecutado exitosamente), `INCONSISTENT` (Ejecutado con inconsistencia),
`NOT_EXECUTED` (No ejecutado), `CANCELLED` (Cancelado). `cause` es obligatoria en los dos últimos.
Los valores se indexan por `fieldId` de la versión de la tarea, no por etiqueta. Para una selección se guarda el
código de la opción.
El contrato del 09C §10.5 no trae `cause` ni la observación: observación registrada a STAGING y Mobile en
`modelo-datos/README.md`.

## Procedimiento
1. **STAGING** valida sobre, contrato e idempotencia y registra Inbox (`definir-envelope-staging`).
2. **Duplicado:** si `captureId` o `idempotencyKey` ya se aplicó → devolver el resultado existente, sin efecto nuevo.
3. **Ámbito:** la tarea existe, pertenece a la finca del mensaje y el actor es su responsable (09C §14).
4. **Población:** el `assetId` está incluido en `activo_tarea` de la tarea. Si no está → rechazar (D-09). Ver DP-18.
5. **Versión:** `formVersionId` coincide con la versión de la tarea y `taskVersion` con un paquete emitido. Si no
   coincide → CONFLICT; no se decide en silencio (09C §9). La versión del formulario no se guarda en la captura.
6. **Estado de la tarea:** si la tarea está Cancelada o Cerrada → conflicto registrado, no se aplica (09C §9).
7. **Revalidación:** el servidor vuelve a aplicar las validaciones de la versión (06 §11: la validación final usa
   las condiciones recibidas). Qué hacer si falla: DP-18.
8. **Aplicar** en una transacción (caso de uso de Planificación/Ejecución):
   - `captura` (equivale a `form_execution_result`, `form_execution_validation` y `task_exception` del 10): resultado
     reportado, causa, observación, validaciones fallidas, origen, tiempos (`iniciado_en`, `capturado_en`) y
     `aplicacion`;
   - `valor_captura` (equivale a `form_execution_value`), un valor jsonb por campo;
   - `activo_tarea.estado` con el resultado consolidado (equivale a `task_asset_result`);
   - avance de la tarea y, si es la primera actividad, transición a En ejecución (`gestionar-estados-tarea`);
   - Outbox con los eventos derivados para Control y Knowledge.
9. **Evidencias:** el archivo se registra en el servicio de Archivos; aquí solo se vincula su `evidenceId`
   (en `valor_captura` si es un campo de foto, documento, video, nota de voz o firma; en `captura.evidencias` si
   respalda una no ejecución o cancelación) (06 §16, 10 §21). Modelo: `modelo-datos/planificacion.md`.
10. **Inventario:** solo los campos con mapeo explícito y autorizado se envían a Inventario como comando o contrato
    (no escritura directa), con valor anterior, nuevo, fecha efectiva, origen, tarea, formulario/versión y usuario
    (03 §6.0). El resto queda como resultado histórico (10 §11).

## Reglas
- Una captura enviada dos veces no duplica el resultado (09C §9, CI-009).
- Un mensaje tardío no pisa un estado más nuevo (09C §13).
- Los conflictos se marcan para revisión; Mobile no decide en silencio (09C §9).
- La tarea **no se cierra por recibir resultados** (09C §9).
- Las alertas se procesan con prioridad y aparte (`procesar-alerta-campo`).
- Los errores de sincronización no se ocultan: quedan en RETRYING, REJECTED o DLQ con causa (CONST §3).

## Estados de sincronización en el móvil (06 §21)
Pendiente local → En cola → Enviando → Enviado → Confirmado; Error temporal; Error que requiere atención.
El núcleo responde con un ACK para que el móvil pase a Confirmado.

## Prohibido
- Escribir en tablas de Inventario desde Planificación.
- Aceptar resultados de activos fuera del dataset.
- Cerrar la tarea automáticamente al recibir todos los resultados.
- Descartar en silencio un resultado inválido o en conflicto.

## Decisiones pendientes que afectan esta skill
- **DP-18:** rechazo del servidor sobre un valor aceptado offline (¿se guarda como inconsistencia?), recapturas de un
  mismo activo y activo encontrado fuera del dataset. Hasta resolverlo: registrar como conflicto para revisión, sin
  sobrescribir.
- **DP-08:** dónde vive el mapeo campo → atributo maestro. Hasta resolverlo: no actualizar atributos de Inventario.
- **DP-20:** catálogo de causas. Acordado: cada causa indica si exige observación y evidencia; el caso de uso lo valida
  al recibir la captura.
- **DP-22:** esquema físico de `form_execution_*` y de las tablas de Mobile del servidor.

## Verificación
- CI-008: un resultado offline se sincroniza después y queda trazable.
- CI-009: reenviar la misma captura no la duplica.
- Activo fuera del dataset → rechazado.
- Versión de formulario distinta → CONFLICT.
- Resultados en desorden → queda el estado más reciente según la regla.
- Recibir todos los resultados no cierra la tarea.
- Sin mapeo autorizado, ningún atributo de Inventario cambia.
