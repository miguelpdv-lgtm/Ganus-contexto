# Diagramas de referencia (transcritos)

> **Fuente de solo lectura.** Transcripción a Markdown de tres infografías del proyecto:
> `Arquitectura GANUS_ Flujo de Datos Empresarial.png`, `Modelo de Información GANUS_ Datos Ganaderos Conectados.png`
> y `ciclovida.png`. Son material de apoyo visual: si difieren de los documentos funcionales (00–11), mandan los documentos.

---

## 1. Arquitectura conceptual TO-BE: flujo de datos y principales interacciones

Lema: "Del dato del campo a la decisión".

**Actores:** Dueño/ganadero (visión y decisiones), Administrador (configura y controla), Supervisor/capataz (planifica y
asigna), Operador de campo (ejecuta y captura), Asesor/veterinario (analiza y recomienda).

**GANUS Operativo (MVP), gestión del trabajo en la finca:**

| Módulo | Contenido |
| --- | --- |
| Organización | Fincas, usuarios, permisos, contexto |
| Inventario | Activos, identificadores, clasificación, ciclo de vida |
| Formularios | Plantillas, campos, validaciones, versiones |
| Planificación | Actividades, poblaciones, work packages, agenda |
| App móvil | Ejecución, captura de datos, evidencias, offline/sync |
| Seguimiento | Estado, avance, trazabilidad, control |

Sistemas alrededor: ODOO (ERP de la empresa), TRACK (territorio), POTREROS (eventos físicos), INDICA (inteligencia de
datos) y ADVISORY (IA y recomendaciones). Todo pasa por **STAGING / intercambio de datos** (recepción, validación,
transformación, enrutamiento, distribución) sobre una **base PostgreSQL común con esquemas separados por dominio**.

Esquemas que muestra la infografía: `organization`, `inventory`, `forms`, `planning`, `staging`, `track`, `potreros`,
`analytics`. (La Arquitectura de referencia §8 lista `organization`, `inventory`, `forms`, `planning`, `staging`, `audit`,
`reporting`; ver DP-22 en `contexto/decisiones-pendientes.md`.)

| # | Origen | Destino | Tipo de dato | Contenido principal | Propósito |
| --- | --- | --- | --- | --- | --- |
| 1 | ODOO | STAGING → Organización | Datos maestros | Empresa, fincas, usuarios, catálogos | Crear o actualizar el contexto del negocio |
| 2 | GANUS Operativo | STAGING → ODOO (opcional) | Resultados operativos | Producción, movimientos, consumos (según alcance) | Integración empresarial |
| 3 | Planificación | STAGING → App móvil | Trabajo planificado (work package) | Actividad, formulario, población/activos, fecha, responsable, instrucciones | Envío de trabajo al campo |
| 4 | App móvil | STAGING → Seguimiento / Inventario | Resultados de ejecución | Datos capturados, evidencias, estado, fecha/hora, usuario, ubicación | Actualizar avance y registrar eventos en el activo |
| 5 | TRACK | STAGING → Seguimiento / Analytics | Eventos territoriales | Posición, movimiento, geocerca, estado del dispositivo | Monitoreo territorial y análisis |
| 6 | GANUS Operativo | STAGING → TRACK (opcional) | Configuraciones | Dispositivos, parámetros, geocercas | Administrar dispositivos y reglas |
| 7 | POTREROS | STAGING → Inventario / Seguimiento | Eventos físicos | Lecturas RFID, pesaje, entradas/salidas, punto de paso | Registrar movimientos y eventos del activo |
| 8 | GANUS Operativo (consolidado) | INDICA | Datos para análisis | Eventos, estados, producción, movimientos, cumplimiento | Generar indicadores y tableros |
| 9 | INDICA | ADVISORY | Información analítica | Indicadores, tendencias, alertas | Generar análisis y recomendaciones |
| 10 | GANUS Operativo | STAGING → POTREROS (opcional) | Configuraciones | Parámetros, dispositivos, reglas de lecturas | Administrar dispositivos y operación |

## 2. Modelo de información MVP (infografía)

"Una sola aplicación, todos los datos conectados". Resume dominios y entidades. **Es anterior al modelo de datos de
`modelo-datos/`**: para Formularios y Planificación manda `modelo-datos/formularios.md` y `modelo-datos/planificacion.md`.

| # | Dominio | Entidades que muestra |
| --- | --- | --- |
| 1 | Organización, usuarios y contexto | grupo, organización, finca, espacio (tipo finca/potrero/corral/instalación, padre_id, geometría opcional), usuario, rol, permiso, usuario_rol, rol_permiso |
| 2 | Inventario y activos | asset_type, asset (finca, tipo, identificador principal, nombre, estado, fecha_nacimiento, sexo, espacio), asset_identifier, asset_attribute, asset_relation |
| 3 | Ciclo de vida | asset_lifecycle_stage, asset_lifecycle_history, lifecycle_condition. Etapas de ejemplo: nacimiento, crianza, levante, producción, reproducción, salida |
| 4 | Formularios | form, form_version, form_field, form_catalog |
| 5 | Planificación | schedule (recurrencia), task, task_dataset (población, filtros JSON), task_assignment |
| 6 | Ejecución móvil | mobile_task, mobile_result, mobile_evidence, mobile_alert |
| 7 | Staging e intercambio | staging_inbox, staging_outbox, staging_log, staging_dead_letter (Mobile offline ↔ STAGING ↔ GANUS centro) |
| 8 | Control y seguimiento | task_progress, result_validation, indicator, indicator_value |
| 9 | Servicios transversales | auditoría, notificación, archivo, catálogo, búsqueda global, configuración, parámetros, observabilidad |
| 10 | Knowledge y Advisory | knowledge_rule, situation, recommendation, recommendation_task |
| 11 | Alertas y notificaciones | alert, notification |

Flujos clave: configuración (organización → fincas → usuarios → catálogos), planificación (formularios → tareas →
dataset → mobile), ejecución (resultados → validación → control), conocimiento (reglas → indicadores → situaciones →
recomendaciones) y comunicación (alertas → notificaciones → acción).

Capas de datos: maestros (organización, catálogos, activos, formularios), transaccionales (tareas, resultados,
evidencias), de control (indicadores, alertas, auditoría) y de conocimiento (reglas, situaciones, recomendaciones).

## 3. Ciclo de vida en GANUS (ejemplo: vaca lechera)

El ciclo de vida define las etapas que atraviesa un activo en el tiempo. Cada etapa tiene tareas referenciales y
condiciones que sirven de guía para la gestión y el análisis. Ejemplo: activo VACA-1258, nacida el 15/01/2024,
18 meses al 26/08/2026, etapa actual "Desarrollo".

| Etapa | Edad | Tareas referenciales (guía) | Condiciones a evaluar | Indicadores clave | Ejemplo de alerta |
| --- | --- | --- | --- | --- | --- |
| 1. Cría | 0–2 meses | Registro de nacimiento, calostrado, pesaje inicial, revisión sanitaria | Bajo peso al nacer, debilidad, no consumió calostro | Peso al nacimiento, supervivencia | No se registró calostrado en las primeras 12 horas |
| 2. Predestete | 2–6 meses | Pesajes periódicos, vacunas, desparasitación, destete | Bajo crecimiento, diarreas, problemas respiratorios | Peso al destete, ganancia diaria | Ganancia diaria de peso por debajo del objetivo |
| 3. Levante | 6–12 meses | Pesajes, evaluación corporal, manejo sanitario, nutrición | Ganancia baja, parasitismo, condición corporal baja | Peso actual, ganancia diaria, condición corporal | Parasitismo no controlado según programa |
| 4. Desarrollo | 12–15 meses | Pesajes, evaluación corporal, evaluación reproductiva, sanidad | Peso por debajo del objetivo, condición corporal baja, desarrollo reproductivo tardío | Peso actual, condición corporal, edad | No se realizó evaluación reproductiva en los últimos 60 días |
| 5. Pre-servicio | 15–24 meses | Evaluación reproductiva, pesaje, condición corporal, vacunas | No alcanza peso objetivo, condición corporal inadecuada, reproductivo no apto | Peso, condición corporal, edad al primer servicio | Animal no alcanza peso mínimo para primer servicio |
| 6. Gestación | 24–33 meses | Diagnóstico de gestación, control corporal, sanidad, nutrición | Pérdida de gestación, condición corporal baja, problemas sanitarios | Días de gestación, condición corporal, incidencias | Condición corporal baja en gestación avanzada |
| 7. Lactancia | 33–48 meses | Control de producción, reproducción, sanidad de ubre, nutrición | Producción baja, mastitis, no queda preñada | Litros/día, litros acumulados, días en leche | Producción por debajo del promedio del hato |
| 8. Descarte | 48+ meses | Evaluación final, motivo de salida, registro de salida | Baja productividad, problemas crónicos, edad avanzada | Productividad total, días productivos, motivo de salida | Bajo desempeño continuo por más de 2 ciclos |

Cómo funciona: 1) se define el ciclo (etapas, tareas referenciales y condiciones por tipo de activo); 2) se asigna al
activo; 3) GANUS ubica al activo en su etapa según la edad; 4) compara lo registrado con lo esperado; 5) genera alertas,
recomendaciones y reportes.

Relación con tu módulo: las **tareas referenciales** guían a Planificación pero **no crean tareas solas**
(Constitución §7.2, documento 10 §8).
