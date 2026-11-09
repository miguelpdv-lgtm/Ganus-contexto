---
name: validar-publicacion-tarea
description: Ejecuta la validación previa de una tarea GANUS, arma el resumen ejecutivo (qué, cuándo, quién, dónde, sobre qué, cómo, capacidad, calidad y resultado esperado), advierte sobrecarga sin bloquear y ejecuta "Confirmar y publicar tarea". Usar al implementar el paso final del asistente de creación de tareas, la verificación de capacidad o el endpoint de publicación.
---

# Validar publicación de tarea

## Propósito
Que el administrador o supervisor sepa **exactamente** qué va a salir al campo antes de confirmarlo, y que ninguna
tarea incompleta llegue al móvil.

## Fuentes
- 05 §10 (definición de una tarea), §13 (validación previa), §14 (capacidad y sobrecarga), §16 (despacho),
  §27 reglas 13–17, §33 (UX).
- 10 §26 (integridad), §34 (crear y publicar tarea).
- 09C §12.2 (publicación de tarea al campo).
- 09F §3 (roles).

## Cuándo usarla
- Al implementar el resumen previo y el botón final del asistente.
- Al implementar el cálculo de carga del responsable.
- Al implementar el caso de uso `ConfirmarYPublicarTarea`.

## Verificaciones que bloquean la publicación
| # | Verificación | Fuente |
| --- | --- | --- |
| 1 | La tarea tiene formulario **publicado** y versión identificable (no obsoleta) | 05 regla 13 |
| 2 | La tarea tiene responsable (operador o supervisor) | 05 regla 14 |
| 3 | El responsable es un usuario activo con acceso a la finca de la tarea | 09C §14, CONST §13 |
| 4 | Tarea masiva: dataset calculado y revisado; tarea unitaria: activo identificado | 05 regla 15, §6 |
| 5 | Tiene fecha o periodo y prioridad | 05 §10 paso 8 |
| 6 | Tiene contexto de finca, proceso y tipo de actividad | 05 §10 paso 1 |
| 7 | No hay inconsistencias que impidan publicar (bloque "Calidad") | 05 §13 |
| 8 | El usuario que publica tiene permiso para hacerlo | 09F §3, DP-23 |

## Advertencias que NO bloquean
- **Capacidad:** Normal / Alta carga / Sobrecarga (05 §14). En el MVP la decisión final es del administrador o
  supervisor y la condición de carga se registra con la tarea.
- Otras observaciones de calidad que no impiden ejecutar.

## Resumen previo (05 §13)
| Bloque | Contenido mínimo |
| --- | --- |
| Qué | Proceso, actividad, formulario y versión |
| Cuándo | Fecha o periodo, prioridad, recurrencia si aplica |
| Quién | Operador o supervisor responsable |
| Dónde | Finca, proceso y espacio cuando aplique |
| Sobre qué | Dataset, cantidad objetivo, criterios y población |
| Cómo | Campos, obligatoriedad, validaciones, evidencias y datos precargados |
| Capacidad | Duración estimada, disponibilidad y carga existente |
| Calidad | Inconsistencias, faltantes o condiciones que impiden publicar |
| Resultado esperado | Cantidad, tiempos y condiciones de cierre |

El resumen debe ser visual y ejecutivo, no una pantalla técnica (05 §33). La acción final dice exactamente:
**CONFIRMAR Y PUBLICAR TAREA**.

## Procedimiento
1. Ejecutar las verificaciones que bloquean. Si alguna falla, mostrar cuál y no continuar.
2. Calcular la carga del responsable (si existen los datos; ver DP-16).
3. Armar el resumen.
4. Al confirmar, en una transacción:
   - congelar el dataset (`calcular-dataset-poblacion`);
   - cambiar el estado según `gestionar-estados-tarea`;
   - registrar la condición de carga, el actor y la fecha en `planning.historial` (evento de publicación);
   - generar el paquete y el evento por Outbox (`construir-paquete-trabajo`).
5. Registrar auditoría de validación, publicación y asignación (05 §35).

## Prohibido
- Publicar con una verificación bloqueante fallida.
- Bloquear por sobrecarga en el MVP (05 §14).
- Asignación automática por IA u optimización automática (05 §4).
- Enviar el trabajo directo al móvil sin pasar por STAGING (05 §16).

## Decisiones pendientes que afectan esta skill
- **DP-16:** de dónde sale la duración estimada, dónde vive la disponibilidad y qué umbrales separan Normal, Alta carga
  y Sobrecarga. Hasta resolverlo: mostrar la carga solo si existen los datos; no inventar umbrales.
- **DP-05:** si "Confirmar y publicar" lleva la tarea directo a Disponible para campo o pasa por Validada y Programada
  como acciones separadas.
- **DP-23:** qué roles pueden publicar.
- **DP-27:** qué son las condiciones de la tarea y las «condiciones de cierre» del resumen. Hasta resolverlo: el
  bloque «Resultado esperado» muestra cantidad y tiempos; no se inventan condiciones.

## Verificación
- Cada verificación bloqueante tiene su prueba: falla → no se publica y no hay evento.
- Sobrecarga → advertencia visible y la tarea igual se puede publicar.
- Publicar deja dataset congelado, estado actualizado, auditoría y un único evento.
- Un usuario sin permiso → FORBIDDEN.
