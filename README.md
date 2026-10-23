# GANUS — Contexto de Formularios y Planificación

Repositorio de consulta y de preparación de prompts para los módulos **Formularios dinámicos** y **Planificación** de
GANUS Operativo. No contiene código de la aplicación: solo documentación en Markdown, el modelo de datos de los dos
módulos, las skills del proyecto y el estado del trabajo.

Las reglas que sigue Claude en este repositorio están en [`CLAUDE.md`](CLAUDE.md).

## Cómo usarlo

Abrir Claude Code en la carpeta del repositorio (`claude`) y pedir en lenguaje natural.

| Para | Ejemplo |
| --- | --- |
| Consultar | "¿Qué dice el 05 sobre la cancelación individual en campo?" · "¿Quién es dueño de la alerta?" · "¿Cómo funciona el ciclo de vida en Inventario?" |
| Preparar un prompt | "Prepárame el prompt para implementar la publicación de versiones de formulario" |
| Registrar contexto | "Estoy en la rama feature/GAN-123-publicar-version y el ticket es GAN-123" |
| Registrar avance | "Ya quedó implementada la tabla task_target" · "Se decidió la DP-05: …" |

Claude solo actualiza el repositorio cuando se le indica, y no recuerda conversaciones anteriores: todo lo que deba
recordarse queda en `contexto/`.

## Estructura

| Ruta | Contenido |
| --- | --- |
| [`CLAUDE.md`](CLAUDE.md) | Rol, alcance, memoria, forma de actualizar y plantilla de prompts |
| [`contexto/estado-actual.md`](contexto/estado-actual.md) | Rama, Jira, trabajo en curso y entregables |
| [`contexto/bitacora.md`](contexto/bitacora.md) | Registro de cambios |
| [`contexto/decisiones-pendientes.md`](contexto/decisiones-pendientes.md) | Decisiones abiertas (DP-01 a DP-25) |
| [`modelo-datos/`](modelo-datos/) | Modelo de datos de `forms` y `planning` y convenciones comunes |
| [`fuentes/`](fuentes/) | Documentos oficiales en Markdown (solo lectura) |
| [`.claude/skills/`](.claude/skills/) | Skills del proyecto |

## Documentos fuente y abreviaturas

| Código | Documento | Archivo |
| --- | --- | --- |
| MC | Master Context Claude Code v1.0 | [`fuentes/master-context.md`](fuentes/master-context.md) |
| 00 | Cerebro GANUS MVP v1.1 | [`fuentes/00-cerebro-mvp.md`](fuentes/00-cerebro-mvp.md) |
| ARQ | Arquitectura de referencia v1.1 | [`fuentes/01-arquitectura-referencia.md`](fuentes/01-arquitectura-referencia.md) |
| 02 | Organización v1.2 | [`fuentes/02-organizacion.md`](fuentes/02-organizacion.md) |
| 03 | Inventario y Ciclo de Vida v1.2 | [`fuentes/03-inventario-ciclo-vida.md`](fuentes/03-inventario-ciclo-vida.md) |
| 04 | Formularios Dinámicos v1.3 | [`fuentes/04-formularios-dinamicos.md`](fuentes/04-formularios-dinamicos.md) |
| 05 | Planificación v1.0 | [`fuentes/05-planificacion.md`](fuentes/05-planificacion.md) |
| 06 | Mobile v1.0 | [`fuentes/06-mobile.md`](fuentes/06-mobile.md) |
| 07 | Seguimiento, Centro de Control, Knowledge y Advisory v1.0 | [`fuentes/07-seguimiento-control-knowledge-advisory.md`](fuentes/07-seguimiento-control-knowledge-advisory.md) |
| 08 | Servicios Transversales v1.0 | [`fuentes/08-servicios-transversales.md`](fuentes/08-servicios-transversales.md) |
| 09C | Contratos e Integraciones MVP v1.0 | [`fuentes/09c-contratos-integraciones.md`](fuentes/09c-contratos-integraciones.md) |
| 09F | Funcionamiento Integral v1.0 | [`fuentes/09f-funcionamiento-integral.md`](fuentes/09f-funcionamiento-integral.md) |
| 10 | Modelo BD de referencia MVP v1.0 | [`fuentes/10-modelo-bd-referencia.md`](fuentes/10-modelo-bd-referencia.md) |
| CONST | Constitución Claude Code v1.0 | [`fuentes/11-constitucion-claude-code.md`](fuentes/11-constitucion-claude-code.md) |
| v3.1 | Alcance y Especificación Funcional Completa v3.1 | [`fuentes/alcance-funcional-v3.1.md`](fuentes/alcance-funcional-v3.1.md) |
| — | Visión, Arquitectura y Alcance Marco v1.0 | [`fuentes/vision-arquitectura-marco.md`](fuentes/vision-arquitectura-marco.md) |
| — | Pautas de desarrollo (borrador) | [`fuentes/pautas-desarrollo.md`](fuentes/pautas-desarrollo.md) |
| — | GANUS TRACK D00 · especificación | [`fuentes/track-d00-especificacion.md`](fuentes/track-d00-especificacion.md) |
| — | GANUS TRACK · anexos (Excel, presentación, flujo) | [`fuentes/track-anexos.md`](fuentes/track-anexos.md) |
| — | Infografías transcritas (arquitectura, modelo de información, ciclo de vida) | [`fuentes/diagramas-referencia.md`](fuentes/diagramas-referencia.md) |

## Skills

| # | Skill | Bloque | Se modifica desde este repo |
| --- | --- | --- | --- |
| 1 | [evaluar-decision-funcional](.claude/skills/evaluar-decision-funcional/SKILL.md) | Reglas | Solo con pedido explícito |
| 2 | [verificar-ownership-dominio](.claude/skills/verificar-ownership-dominio/SKILL.md) | Reglas | Solo con pedido explícito |
| 3 | [validar-arquitectura-hexagonal](.claude/skills/validar-arquitectura-hexagonal/SKILL.md) | Reglas | Solo con pedido explícito |
| 4 | [guardrail-ia-determinista](.claude/skills/guardrail-ia-determinista/SKILL.md) | Reglas | Solo con pedido explícito |
| 5 | [generar-pruebas-dod](.claude/skills/generar-pruebas-dod/SKILL.md) | Reglas | Solo con pedido explícito |
| 6 | [disenar-plantilla-formulario](.claude/skills/disenar-plantilla-formulario/SKILL.md) | Formularios | Sí |
| 7 | [versionar-formulario](.claude/skills/versionar-formulario/SKILL.md) | Formularios | Sí |
| 8 | [calcular-dataset-poblacion](.claude/skills/calcular-dataset-poblacion/SKILL.md) | Planificación | Sí |
| 9 | [validar-publicacion-tarea](.claude/skills/validar-publicacion-tarea/SKILL.md) | Planificación | Sí |
| 10 | [gestionar-estados-tarea](.claude/skills/gestionar-estados-tarea/SKILL.md) | Planificación | Sí |
| 11 | [procesar-alerta-campo](.claude/skills/procesar-alerta-campo/SKILL.md) | Planificación | Sí |
| 12 | [definir-envelope-staging](.claude/skills/definir-envelope-staging/SKILL.md) | STAGING | Solo con pedido explícito |
| 13 | [construir-paquete-trabajo](.claude/skills/construir-paquete-trabajo/SKILL.md) | Planificación → STAGING | Sí |
| 14 | [procesar-sincronizacion-resultados](.claude/skills/procesar-sincronizacion-resultados/SKILL.md) | Planificación ← STAGING | Sí |
