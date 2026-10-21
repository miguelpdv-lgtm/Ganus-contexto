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
[`docs/decisiones-pendientes.md`](../../docs/decisiones-pendientes.md). Mientras una decisión esté abierta, la skill
indica qué hacer: implementar solo lo definido y no inventar el resto.

## Fuentes y abreviaturas

| Código | Documento | Ruta en el repositorio |
| --- | --- | --- |
| MC | Master Context Claude Code v1.0 | `Cerebro/Contexto/GANUS_MASTER_CONTEXT_CLAUDE_CODE_v1.0.md` |
| 00 | Cerebro GANUS MVP v1.1 | `Cerebro/Contexto/00_CEREBRO_GANUS_MVP_v1.1.docx` |
| v3.1 | Alcance y Especificación Funcional Completa v3.1 | `Cerebro/Contexto/GANUS_Alcance_Especificacion_Funcional_Completa_v3.1_Ciclo_de_Vida.docx` |
| ARQ | Arquitectura de Referencia v1.1 | `Alcance Funcional/GANUS_Arquitectura_Referencia_v1.1_Evaluacion_y_Base_Oficial.docx` |
| 02 | Funcional Organización v1.2 | `Alcance Funcional/02_FUNCIONAL_ORGANIZACION_GANUS_v1.2_COMPLETO_FINAL.docx` |
| 03 | Funcional Inventario y Ciclo de Vida v1.2 | `Alcance Funcional/03_FUNCIONAL_INVENTARIO_Y_CICLO_DE_VIDA_GANUS_v1.2.docx` |
| 04 | Funcional Formularios Dinámicos v1.3 | `Alcance Funcional/04_FUNCIONAL_FORMULARIOS_DINAMICOS_GANUS_v1.3.docx` |
| 05 | Funcional Planificación v1.0 | `Alcance Funcional/05_FUNCIONAL_PLANIFICACION_GANUS_v1.0.docx` |
| 06 | Funcional Mobile v1.0 | `Alcance Funcional/06_FUNCIONAL_MOBILE_GANUS_v1.0.docx` |
| 07 | Seguimiento, Centro de Control, Knowledge y Advisory v1.0 | `Alcance Funcional/07_FUNCIONAL_SEGUIMIENTO_CENTRO_CONTROL_KNOWLEDGE_ADVISORY_GANUS_v1.0.docx` |
| 08 | Servicios Transversales v1.0 | `Alcance Funcional/08_SERVICIOS_TRANSVERSALES_GANUS_OPERATIVO_v1.0.docx` |
| 09C | Contratos e Integraciones MVP v1.0 | `Alcance Funcional/09_CONTRATOS_INTEGRACIONES_GANUS_OPERATIVO_MVP_v1.0.docx` |
| 09F | Funcionamiento Integral v1.0 | `Alcance Funcional/09_FUNCIONAMIENTO_INTEGRAL_GANUS_OPERATIVO_v1.0.docx` |
| 10 | Modelo BD Referencia MVP v1.0 | `Alcance Funcional/10_MODELO_BD_REFERENCIA_MVP_GANUS_v1.0.docx` |
| CONST | Constitución Claude Code v1.0 | `11_CONSTITUCION_CLAUDE_CODE_GANUS_v1.0.docx` |

Los `.docx` no se leen de forma confiable desde Claude Code. Se recomienda convertirlos a Markdown en `docs/`
(por ejemplo con `pandoc`) y actualizar esta tabla.
