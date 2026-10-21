---
name: evaluar-decision-funcional
description: Clasifica un cambio o requisito de GANUS como definido, prohibido, abierto técnico o abierto funcional antes de implementarlo, y registra la decisión pendiente cuando falta. Usar al empezar cualquier funcionalidad, cuando un requisito sea ambiguo, cuando dos documentos se contradigan o cuando alguien pida algo que podría estar fuera del MVP.
---

# Evaluar decisión funcional

## Propósito
Impedir que Claude Code o un desarrollador invente producto. Antes de escribir código se responde una pregunta:
**¿esto ya está decidido?** Si no lo está, se detiene el cambio funcional y se registra la decisión.

## Fuentes
- CONST §0, §1 (jerarquía), §2 (mandato), §16 (protocolo ante ambigüedad), §19.
- MC §19 (fuera del MVP), §21 (reglas absolutas), §28 (no inventar).
- 05 §4 (fuera de alcance de Planificación), 04 §5 (fuera de alcance de Formularios).
- `docs/decisiones-pendientes.md`.

## Cuándo usarla
- Antes de implementar una historia, endpoint, pantalla o tabla nueva.
- Cuando un requisito admite dos lecturas.
- Cuando dos documentos dicen cosas distintas.
- Cuando alguien pide una mejora "porque sería útil".

## Cuándo no usarla
- Para decidir cómo organizar el código (eso es `validar-arquitectura-hexagonal`).
- Para revisar si se tocó otro dominio (eso es `verificar-ownership-dominio`).

## Jerarquía de autoridad (CONST §1)

| Nivel | Fuente |
| --- | --- |
| 1 | Decisiones funcionales cerradas del MVP (D-01 a D-10, en v3.1 §5) |
| 2 | 00 Cerebro GANUS MVP |
| 3 | Arquitectura oficial (ARQ) |
| 4 | Documentos funcionales 02 a 08 |
| 5 | 09C Contratos e Integraciones |
| 6 | 10 Modelo BD de referencia |
| 7 | ADR aprobados |
| 8 | Criterio de la IA: solo para elegir entre alternativas técnicas compatibles |

Conflicto conocido: para Formularios y Planificación, la v3.1, el 00 y el MC contradicen al 04 v1.3 y al 05 v1.0.
Hasta que se cierre **DP-01**, no se elige en silencio: se reporta el conflicto y se aplica solo lo que coincide.

## Decisiones cerradas que no se reabren (v3.1 §5)

| Código | Decisión |
| --- | --- |
| D-01 | Grupo empresarial opcional |
| D-02 | Roles y permisos predefinidos para el cliente; configuración solo para Superusuario GANUS |
| D-03 | Recurrencia y periodicidad pertenecen a Planificación |
| D-04 | El formulario no administra módulo consumidor, proceso, tipo de actividad ni asignación |
| D-05 | Taxonomía Dominio → Categoría → Tipo → Subtipo como catálogo maestro |
| D-06 | Estados de tarea: Borrador → Validada → Programada → Disponible para campo → En ejecución → Pausada → Finalizada → Por validar → Cerrada; Cancelada como salida alterna |
| D-07 | Dashboard táctico con indicadores del onboarding |
| D-08 | Móvil con Tareas, Alertas e Historial; Novedades es una alerta tipo "Otro" |
| D-09 | La segmentación ocurre solo en Planificación; el móvil selecciona dentro de la población recibida |
| D-10 | Roles: Dueño, Administrador, Supervisor y Operador |

## Procedimiento
1. **Identificar el dominio** dueño del comportamiento (ver `verificar-ownership-dominio`).
2. **Buscar la regla** en el documento funcional del dominio, luego en 00/MC, luego en 09C y 10.
3. **Revisar el alcance**: ¿está en "fuera de alcance" (04 §5, 05 §4, MC §19)?
4. **Clasificar** con la tabla siguiente.
5. **Actuar** según la clasificación.
6. **Reportar** con el formato de salida.

| Clasificación | Cuándo | Acción |
| --- | --- | --- |
| DEFINIDO | Un documento lo dice sin contradicción | Implementarlo y citar la fuente en el PR |
| PROHIBIDO | Un documento o regla lo excluye | No implementarlo; explicar por qué |
| ABIERTO TÉCNICO | Falta un detalle de implementación que no cambia el comportamiento | Proponer una alternativa compatible y documentarla; si es estructural, ADR |
| ABIERTO FUNCIONAL | Falta una regla de negocio o hay contradicción | Detener ese cambio, registrar o citar la `DP-xx` y seguir solo con lo definido |
| FUERA DEL MVP | Pertenece al ecosistema pero no al primer release | No implementarlo aunque sea sencillo |

## Fuera del MVP (resumen de MC §19 y 05 §4)
Tracking avanzado, hardware IoT avanzado, automatización compleja, modelos predictivos, IA autónoma, optimización
o balanceo automático de recursos, asignación por IA, agentes que publiquen tareas sin aprobación humana, dashboards
configurables, Canvas Builder, marketplace, replanificación automática masiva, edición del formulario publicado
desde la tarea, Mobile modificando el dataset.

## Prohibido
- Inventar una regla para "completar" el sistema.
- Elegir en silencio entre dos documentos que se contradicen.
- Ampliar el MVP por conveniencia técnica.
- Cambiar una decisión funcional para acomodar una librería o un framework.
- Agregar campos o tablas "porque podrían ser útiles después" (CONST §19).

## Formato de salida
```
Clasificación: DEFINIDO | PROHIBIDO | ABIERTO TÉCNICO | ABIERTO FUNCIONAL | FUERA DEL MVP
Dominio: <dominio dueño>
Fuente: <documento §sección>
Acción: <qué se implementa / qué no>
Decisión pendiente: <DP-xx o "ninguna">
```

## Registrar una decisión nueva
Si la ambigüedad no está en `docs/decisiones-pendientes.md`:
1. Asignar el siguiente código `DP-xx`.
2. Agregar una fila al resumen (tema, tipo, quién decide, skills bloqueadas, estado `ABIERTA`).
3. Agregar el detalle: conflicto o vacío, documentos involucrados y, si aplica, una propuesta marcada como tal.

## Verificación
- Todo PR cita la fuente de cada comportamiento implementado.
- Ningún PR contiene un comportamiento clasificado como ABIERTO FUNCIONAL.
- Las decisiones nuevas aparecen en el registro.
