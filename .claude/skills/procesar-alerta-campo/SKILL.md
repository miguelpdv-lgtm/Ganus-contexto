---
name: procesar-alerta-campo
description: Recibe, prioriza y muestra en el Centro de Control las alertas de campo enviadas desde el móvil, y permite que un supervisor las convierta manualmente en una tarea vinculada sin cerrar la alerta. Usar al implementar el panel de alertas, el formulario estándar de alerta, el consumo de AlertCreated.v1 o la acción "crear tarea desde alerta".
---

# Procesar alerta de campo

## Propósito
Una alerta es una **situación extraordinaria** reportada desde el campo. **No es una tarea** (05 §23, regla 26;
CONST §8). Puede originar una tarea si una persona lo decide.

## Fuentes
- 05 §23 (alertas), §24 (formulario estándar), §25 (conversión en tarea), §27 reglas 26–27, §34.
- 06 §17–§19 (botón de alerta, captura y conectividad).
- 09C §6 (AlertCreated.v1), §9 ("no se convierte automáticamente en tarea"), §10.6 (contrato Alert), §17 (CT-MOB-02).
- 10 §20 (`alert`, `alert_task_link`).
- D-08 (Novedades = alerta tipo "Otro"); v3.1 §9.5 (conversión manual en el MVP).

## Cuándo usarla
- Al implementar el panel de alertas del Centro de Control.
- Al consumir AlertCreated.v1.
- Al implementar "Crear tarea desde alerta".

## Contenido mínimo de una alerta (05 §23–§24, 09C §10.6)
Tipo, prioridad, situación o descripción, contexto (finca, tarea, espacio), activo relacionado opcional, ubicación
cuando exista, usuario, fecha y hora, y evidencias (foto, video, nota de voz, texto).

```json
{ "alertId": "uuid", "farmId": "uuid", "taskId": "uuid|null", "assetId": "uuid|null",
  "type": "EXCEPTION", "priority": "HIGH", "channel": "VOICE", "message": "...",
  "evidence": [{ "evidenceId": "uuid" }], "occurredAt": "UTC" }
```

## Prioridades (05 §23)
| Prioridad | Tratamiento |
| --- | --- |
| Crítica | Atención inmediata |
| Alta | Atención prioritaria |
| Media | Atención programable |
| Baja | Seguimiento |

## Reglas
1. El panel de alertas está **siempre visible** en el Centro de Control, ordenado por prioridad y estado (05 §23).
2. Las alertas llegan por STAGING con prioridad de transmisión; offline se guardan en cola prioritaria (06 §19).
3. Una alerta se conserva aunque la tarea relacionada siga abierta (09C §9, CI-010).
4. **Ninguna alerta se convierte en tarea automáticamente** (09C §9). En el MVP la conversión es manual (v3.1 §9.5).
5. Al convertir, la alerta queda como origen de la tarea (`alert_task_link`) (05 regla 27).
6. **Crear la tarea no cierra la alerta**: su estado refleja si fue atendida, convertida, resuelta o descartada (05 §25).
7. La tarea creada sigue el flujo normal completo: formulario → dataset → validar → publicar (05 §25).
8. Puede ser unitaria o masiva y asignarse a otro responsable (05 §25).
9. "Novedades" no es un módulo: es una alerta de tipo "Otro" (D-08).

## Flujo de conversión (05 §25)
```
ALERTA → REVISAR → DECIDIR → CREAR TAREA → FORMULARIO → DATASET → VALIDAR → PUBLICAR
```
1. El supervisor abre la alerta y su evidencia.
2. Decide: atender, convertir en tarea, resolver o descartar.
3. Si convierte: se abre el asistente de tarea en Borrador, precargando contexto (finca, espacio, activo si existe).
4. Al guardar la tarea se crea `alert_task_link`.
5. La alerta cambia de estado según la decisión (ver DP-06).

## Trazabilidad (05 §34)
Alerta → origen, usuario y contexto → decisión → tarea relacionada → resultado.

## Prohibido
- Tratar una alerta como tarea o crear tareas automáticamente desde alertas.
- Cerrar la alerta solo porque se creó la tarea.
- Saltar el flujo normal de validación de la tarea creada.
- Perder una alerta crítica por agrupación de notificaciones (08).

## Decisiones pendientes que afectan esta skill
- **DP-06:** dueño del ciclo de la alerta (Planificación según 05 §29; Control/Knowledge según 07, 09C y 10) y su
  máquina de estados en el Centro de Control. Hasta resolverlo: guardar el estado de envío del móvil
  (Pendiente de envío → Enviada → Recibida → Atendida/gestionada, 06 §19) y registrar la decisión del supervisor
  (atendida, convertida, resuelta, descartada) como historial, sin automatismos.
- **DP-19:** si el formulario estándar de alerta es una plantilla de Formularios o una estructura fija.

## Verificación
- Una alerta enviada offline llega al reconectar, antes que los datos no críticos.
- Una alerta llega aunque su tarea siga en ejecución (CI-010).
- Crear una tarea desde una alerta crea el vínculo y no cierra la alerta.
- Ninguna alerta genera tareas sin intervención humana.
- El panel ordena por prioridad y estado.
