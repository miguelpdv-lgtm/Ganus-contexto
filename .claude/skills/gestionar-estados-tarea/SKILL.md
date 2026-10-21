---
name: gestionar-estados-tarea
description: Implementa la máquina de estados oficial de la tarea GANUS (D-06) y la del resultado por activo, con transiciones válidas, historial, eventos y rechazo de transiciones inválidas. Usar al crear la entidad Task, al cambiar el estado de una tarea, al procesar inicio, pausa, finalización, validación, cierre o cancelación, o al mostrar estados en web y móvil.
---

# Gestionar estados de la tarea

## Propósito
Que web, móvil, Planificación y Control usen **los mismos estados con el mismo significado** (05 §33) y que ninguna
tarea cambie de estado sin una regla y un rastro.

## Fuentes
- D-06 (v3.1 §5, §9.4); 05 §15 (estados), §17 (resultado por activo), §18 (cancelación individual), §27 reglas 19–23 y 28.
- 06 §14 (estados por activo); 10 §13 (estados y transiciones), §16, §34.
- 09C §6 (TaskCreated, TaskPublished, TaskStarted, TaskCompleted, TaskValidated), §9 (cierre), §17 (CT-PLN-02 TaskStateChanged.v1).
- CONST §19 (no cambiar estados oficiales); 05 §35 (máquina de estados explícita).

## Cuándo usarla
- Al modelar la entidad Task y su historial.
- Al implementar cualquier acción que cambie el estado.
- Al mostrar o filtrar estados.

## Estados de la tarea (D-06, 05 §15)

| Estado | Fase | Significado |
| --- | --- | --- |
| Borrador | Planificación | En construcción |
| Validada | Planificación | Superó la revisión previa |
| Programada | Planificación | Tiene programación confirmada |
| Disponible para campo | Planificación | Paquete preparado y entregable |
| En ejecución | Ejecución | Existe actividad de campo |
| Pausada | Ejecución | Ejecución suspendida temporalmente |
| Finalizada | Ejecución | El operador terminó la ejecución (v3.1 §9.4) |
| Por validar | Ejecución | Resultados disponibles para control |
| Cerrada | Ejecución | Resultados revisados y ciclo concluido |
| Cancelada | Alterna | Tarea anulada con causa trazable |

**No existen en el MVP:** Aceptada, En desplazamiento (D-06). "Vencida", "Retrasada" y "En plazo" son **indicadores
de cumplimiento**, no estados (05 §20). Tampoco existen `assigned`, `expired` ni `pending` como estados de tarea.

Guardar no envía al móvil; solo una tarea Disponible para campo puede entregarse (v3.1 §9.4).

## Transiciones

| De | A | Disparador | Estado de la regla |
| --- | --- | --- | --- |
| Borrador | Validada | Supera la validación previa (`validar-publicacion-tarea`) | Definido |
| Validada | Programada | Programación confirmada | Disparador abierto (DP-05) |
| Programada | Disponible para campo | Paquete generado | Disparador abierto: ¿al publicar o en la fecha? (DP-05) |
| Disponible para campo | En ejecución | Llega la primera actividad de campo (TaskStarted o primer resultado) | Definido por significado |
| En ejecución | Pausada | Suspensión temporal | Quién pausa: abierto (DP-05) |
| Pausada | En ejecución | Reanudación | Quién reanuda: abierto (DP-05) |
| En ejecución | Finalizada | El operador termina | Definido (v3.1 §9.4) |
| Finalizada | Por validar | Resultados disponibles para control | Disparador abierto (DP-05) |
| Por validar | Cerrada | Supervisor revisa; todos los resultados tienen estado compatible con cierre (10 §34) | Criterio de compatibilidad abierto (DP-05) |
| (varios) | Cancelada | Anulación con causa | Estados de origen abiertos (DP-05) |

Regla mientras DP-05 esté abierta: implementar solo las transiciones marcadas "Definido" y dejar las demás como
acciones explícitas de un usuario autorizado, sin automatismos.

## Resultado por activo (10 §13, 06 §14, 05 §17)

| Estado | Significado |
| --- | --- |
| Pendiente | Aún no ejecutado |
| En ejecución | Activo actualmente en proceso |
| Ejecutado exitosamente | Captura válida y completa |
| Ejecutado con inconsistencia | Hubo ejecución, pero requiere revisión |
| No ejecutado | No se produjo ejecución válida |
| Cancelado | Se canceló la ejecución sobre ese activo |

Transiciones: Pendiente → En ejecución → (Ejecutado exitosamente | Ejecutado con inconsistencia | No ejecutado | Cancelado).

Reglas:
- Cada activo del dataset tiene su propio resultado (05 regla 19).
- Una tarea puede terminar con resultados mixtos (05 regla 20).
- Cancelar un activo no cancela la tarea (05 regla 21, 10 §34).
- No ejecutado y Cancelado exigen causa; según la condición, observación o evidencia (05 §18, regla 22).
- **Captura exitosa ≠ ejecución:** se mide aparte (05 regla 23).

## Reglas de implementación
1. Máquina de estados **explícita en el dominio** (05 §35): una tabla de transiciones permitidas, no `if` dispersos.
2. Una transición inválida → error de dominio, traducido a CONFLICT (09C §4.2).
3. Cada cambio se guarda en `task_state_history`: estado anterior, nuevo, actor, fecha y hora, origen
   (WEB, MOBILE, SYSTEM), causa si aplica y `correlationId`.
4. Cada cambio emite TaskStateChanged.v1 por Outbox, más el evento específico cuando corresponda (TaskPublished,
   TaskStarted, TaskCompleted, TaskValidated).
5. La tarea **no se cierra por recibir resultados**; respeta el flujo de validación (09C §9).
6. Concurrencia optimista: un cambio sobre una versión vieja de la tarea se rechaza (10 §16).
7. Los nombres de los estados en la interfaz son los oficiales en español; los códigos técnicos se fijan en un ADR.

## Prohibido
- Agregar, renombrar o saltar estados oficiales (CONST §19).
- Cambiar estados desde el frontend sin pasar por el caso de uso.
- Que Mobile cambie el estado de la tarea fuera de lo que su resultado comunica.
- Borrar el historial de estados.

## Decisiones pendientes que afectan esta skill
DP-05 (disparadores y cierre), DP-04 (estados por ocurrencia recurrente), DP-17 (cambios después de publicar).

## Verificación
- Cada transición "Definido" tiene una prueba positiva.
- Toda transición fuera de la tabla → CONFLICT, sin cambios.
- Cancelar un activo deja el resto de la tarea igual.
- Recibir todos los resultados no cierra la tarea.
- Cada cambio deja una fila en `task_state_history` y un evento.
