---
name: generar-pruebas-dod
description: Define y genera las pruebas obligatorias de GANUS (unitarias, integración, API, contrato, E2E, migraciones, offline/sync, seguridad por finca y regresión) y verifica la Definition of Done antes de abrir un PR. Usar al terminar una funcionalidad, al escribir pruebas en NestJS o Angular, o al revisar si un cambio está listo.
---

# Generar pruebas y Definition of Done

## Propósito
Una funcionalidad no está terminada porque compila (CONST §14). Debe tener evidencia de comportamiento, respetar
fronteras y poder recuperarse de errores.

## Fuentes
- CONST §14 (pruebas obligatorias), §15 (Definition of Done), §21 (checklist de PR), §23 (criterio final).
- 09C §16 (pruebas de contrato), §19 (criterios CI-001 a CI-015), §20 (matriz de pruebas de integración).
- 10 §34 (casos de consistencia críticos), §35 (reglas de migraciones).
- MC §20 (caso maestro de validación del MVP).

## Cuándo usarla
- Al cerrar una historia.
- Al escribir pruebas.
- Antes de abrir un PR.

## Niveles obligatorios (CONST §14)

| Nivel | Qué se prueba | Herramienta sugerida (técnica) |
| --- | --- | --- |
| Unitarias | Casos de uso, invariantes, máquinas de estado, validaciones | Jest (por defecto en NestJS) |
| Integración | Repositorio + PostgreSQL real, transacciones, Outbox | Jest + base de prueba en contenedor |
| API | Contrato HTTP, autorización, validación, clases de error | Supertest |
| Contrato | Productor y consumidor de eventos y DTOs; ejemplos de payload | Validación de JSON Schema |
| E2E | Flujos críticos del usuario | Playwright para Angular |
| Migraciones | Aplicar desde la versión base, validar y revertir cuando sea viable | Ejecución en CI |
| Offline/Sync | Desconexión, reintento, duplicado, conflicto, recuperación | Pruebas de integración de STAGING |
| Seguridad | Tenant, finca, rol y permiso; acceso con IDs manipulados | Pruebas de API |
| Regresión | Lo ya aceptado sigue funcionando | Suite completa en CI |

## Pruebas mínimas por dominio

### Formularios
- Crear borrador; editar borrador.
- Editar una versión publicada → error de dominio (CONFLICT).
- Publicar crea una versión inmutable y emite FormVersionPublished.v1 por Outbox.
- Cambiar una publicada crea la versión siguiente en Borrador.
- Una versión obsoleta no se puede usar en tareas nuevas.
- Un campo obligatorio sin valor bloquea completar el formulario.
- Una condición activa la visibilidad u obligatoriedad según la definición.
- La vista previa, "Probar tarea" y el paquete producen la misma definición (mismo JSON).

### Planificación
- Una tarea sin formulario publicado, sin responsable o sin dataset (si es masiva) no se publica.
- El dataset publicado queda congelado y trazable.
- Transiciones de estado inválidas se rechazan.
- Cancelar un activo no cancela la tarea (10 §34).
- Una tarea masiva puede tener resultados mixtos.
- La sobrecarga advierte y no bloquea.
- Una alerta puede originar una tarea sin cerrarse sola.

### STAGING y sincronización (09C §20)
- Mismo `messageId` dos veces → un solo efecto (CI-002, CI-009).
- Misma `idempotencyKey` → devuelve el resultado existente.
- Falla técnica → RETRYING; falla permanente → REJECTED → DLQ con causa (CI-004, CI-005).
- Transacción con rollback → no se publica evento.
- Resultado generado offline y sincronizado después (CI-008).
- Mobile no puede cambiar la población (CI-007).

### Seguridad
- Un usuario de la finca A no lee ni escribe datos de la finca B cambiando IDs en la petición.
- Un operador no puede publicar tareas ni formularios (según la matriz de DP-23).

## Contratos (09C §16)
Cada contrato tiene un ejemplo de payload que se valida en una prueba, y al menos una prueba de productor y una de
consumidor.

## Migraciones (10 §35)
- Una migración por cambio, documentada.
- Se prueba hacia adelante y la estrategia de recuperación.
- Nunca sincronización automática del ORM en producción.

## Caso maestro E2E (MC §20)
Para Formularios y Planificación cubrir como mínimo los pasos 5 a 14 (crear y publicar formulario, seleccionar
población, crear, programar, asignar y publicar la tarea, generar y enviar el paquete) y 21 a 26 (recibir el
resultado, validar, procesar, actualizar estado, registrar trazabilidad y mostrar seguimiento).

## Definition of Done (CONST §15)
- [ ] Requisito, regla o criterio de aceptación identificado y citado
- [ ] Dominio y ownership identificados
- [ ] Contrato afectado identificado
- [ ] Código en la frontera correcta, sin acceso indebido a otro dominio
- [ ] Migraciones creadas si aplica
- [ ] Pruebas creadas o actualizadas, y pasan
- [ ] Errores y estados contemplados
- [ ] Auditoría y trazabilidad contempladas
- [ ] Documentación técnica actualizada si cambió un contrato
- [ ] Build y pruebas pasan en CI
- [ ] Sin TODOs funcionales ocultos
- [ ] Sin decisiones de producto no aprobadas (ninguna `DP-xx` abierta implementada)

## Checklist de PR (CONST §21)
¿Requisito identificable? ¿Dominio claro? ¿Fronteras respetadas? ¿Sin acceso directo a otro dominio? ¿Contratos
revisados? ¿Tenant y finca respetados? ¿Prueba agregada? ¿Idempotencia y sync cubiertas si aplica? ¿Migración
actualizada? ¿ADR o documentación actualizada? ¿Sin funcionalidad fuera del MVP? ¿Build y pruebas pasan?

## Criterio final (CONST §23)
El cambio solo está terminado si se puede explicar: qué hace, por qué, qué dominio es dueño, qué contrato usa, qué
datos modifica, qué datos no puede modificar, cómo se prueba, cómo se audita y cómo se recupera de un error.
