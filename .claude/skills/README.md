# Skills GANUS

Cada carpeta contiene un `SKILL.md` que Claude Code carga cuando la tarea coincide con su `description`.
Las reglas que deben cumplirse **siempre** (ownership, arquitectura, guardrail de IA) también deben resumirse en
`CLAUDE.md`, porque una skill solo se carga cuando se activa.

## Índice

| # | Skill | Bloque | Para qué |
| --- | --- | --- | --- |
| 1 | [evaluar-decision-funcional](evaluar-decision-funcional/SKILL.md) | Reglas | Decidir si algo está definido, prohibido o pendiente antes de implementarlo |
| 2 | [verificar-ownership-dominio](verificar-ownership-dominio/SKILL.md) | Reglas | Que ningún dominio toque datos o código de otro |
| 3 | [validar-arquitectura-hexagonal](validar-arquitectura-hexagonal/SKILL.md) | Reglas | Capas Domain / Application / Adapters / Infrastructure en NestJS y Angular |
| 4 | [guardrail-ia-determinista](guardrail-ia-determinista/SKILL.md) | Reglas | Límites del LLM: interpreta, no calcula ni escribe |
| 5 | [generar-pruebas-dod](generar-pruebas-dod/SKILL.md) | Reglas | Pruebas obligatorias y Definition of Done |
| 6 | [disenar-plantilla-formulario](disenar-plantilla-formulario/SKILL.md) | Formularios | Estructura del formulario y contrato que renderizan web y móvil |
| 7 | [versionar-formulario](versionar-formulario/SKILL.md) | Formularios | Borrador, publicación, nueva versión y obsolescencia |
| 8 | [calcular-dataset-poblacion](calcular-dataset-poblacion/SKILL.md) | Planificación | Filtros, cálculo y congelamiento de la población |
| 9 | [validar-publicacion-tarea](validar-publicacion-tarea/SKILL.md) | Planificación | Validación previa, resumen y "Confirmar y publicar" |
| 10 | [gestionar-estados-tarea](gestionar-estados-tarea/SKILL.md) | Planificación | Máquina de estados de la tarea y del resultado por activo |
| 11 | [procesar-alerta-campo](procesar-alerta-campo/SKILL.md) | Planificación | Alertas de campo y su conversión en tarea |
| 12 | [definir-envelope-staging](definir-envelope-staging/SKILL.md) | STAGING | Sobre común, Inbox/Outbox, idempotencia, reintentos y DLQ |
| 13 | [construir-paquete-trabajo](construir-paquete-trabajo/SKILL.md) | STAGING | Paquete de trabajo que recibe el móvil |
| 14 | [procesar-sincronizacion-resultados](procesar-sincronizacion-resultados/SKILL.md) | STAGING | Aplicar resultados del campo sin duplicar ni perder datos |

## Decisiones pendientes

Las skills citan decisiones abiertas por código (`DP-xx`). El registro está en
[`contexto/decisiones-pendientes.md`](../../contexto/decisiones-pendientes.md). Mientras una decisión esté abierta, la skill
indica qué hacer: implementar solo lo definido y no inventar el resto.

## Fuentes y abreviaturas

| Código | Documento | Ruta en el repositorio |
| --- | --- | --- |
| MC | Master Context Claude Code v1.0 | `fuentes/master-context.md` |
| 00 | Cerebro GANUS MVP v1.1 | `fuentes/00-cerebro-mvp.md` |
| v3.1 | Alcance y Especificación Funcional Completa v3.1 | `fuentes/alcance-funcional-v3.1.md` |
| ARQ | Arquitectura de Referencia v1.1 | `fuentes/01-arquitectura-referencia.md` |
| 02 | Funcional Organización v1.2 | `fuentes/02-organizacion.md` |
| 03 | Funcional Inventario y Ciclo de Vida v1.2 | `fuentes/03-inventario-ciclo-vida.md` |
| 04 | Funcional Formularios Dinámicos v1.3 | `fuentes/04-formularios-dinamicos.md` |
| 05 | Funcional Planificación v1.0 | `fuentes/05-planificacion.md` |
| 06 | Funcional Mobile v1.0 | `fuentes/06-mobile.md` |
| 07 | Seguimiento, Centro de Control, Knowledge y Advisory v1.0 | `fuentes/07-seguimiento-control-knowledge-advisory.md` |
| 08 | Servicios Transversales v1.0 | `fuentes/08-servicios-transversales.md` |
| 09C | Contratos e Integraciones MVP v1.0 | `fuentes/09c-contratos-integraciones.md` |
| 09F | Funcionamiento Integral v1.0 | `fuentes/09f-funcionamiento-integral.md` |
| 10 | Modelo BD Referencia MVP v1.0 | `fuentes/10-modelo-bd-referencia.md` |
| CONST | Constitución Claude Code v1.0 | `fuentes/11-constitucion-claude-code.md` |

