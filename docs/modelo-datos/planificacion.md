# Esquema `planning` — Planificación y ejecución

**Estado:** borrador v0 para revisión del equipo.
**Dueño:** Formularios y Planificación.
**Fuentes:** 05 v1.0, 06 §14–§23, 09C §9–§10, 10 §11–§13 y §20, skills `calcular-dataset-poblacion`,
`validar-publicacion-tarea`, `gestionar-estados-tarea`, `procesar-alerta-campo` y `procesar-sincronizacion-resultados`.

## Qué guarda y qué no
- **Guarda** la tarea, su dataset congelado, la asignación, el historial de estados, el resultado por activo, las
  capturas recibidas del campo (valores y evidencias), las excepciones y las alertas de campo.
- **No guarda** la identidad del activo (Inventario), la definición del formulario (Formularios), los paquetes ni
  los mensajes de sincronización (STAGING), ni los archivos (servicio transversal de Archivos).

## Diagrama

Imagen: [img/planificacion.png](img/planificacion.png) (GitHub también dibuja el bloque Mermaid de abajo).

```mermaid
erDiagram
    serie_recurrencia |o--o{ tarea : "genera ocurrencias (DP-04)"
    tarea ||--o{ asignacion_tarea : "asigna"
    tarea ||--o{ historial_estado_tarea : "registra estados"
    tarea ||--o| dataset_tarea : "define poblacion"
    dataset_tarea ||--o{ criterio_dataset_tarea : "filtra con"
    dataset_tarea ||--o{ objetivo_tarea : "incluye o excluye"
    tarea ||--o{ resultado_activo_tarea : "controla por activo"
    objetivo_tarea ||--o| resultado_activo_tarea : "tiene resultado"
    resultado_activo_tarea ||--o{ captura_ejecucion : "recibe capturas"
    captura_ejecucion ||--o{ valor_captura : "guarda valores"
    captura_ejecucion ||--o{ validacion_captura : "registra validaciones"
    captura_ejecucion ||--o{ evidencia_ejecucion : "adjunta"
    resultado_activo_tarea ||--o{ excepcion_activo_tarea : "explica no ejecucion"
    excepcion_activo_tarea ||--o{ evidencia_ejecucion : "adjunta"
    tarea |o--o{ alerta_campo : "reportada durante"
    alerta_campo ||--o{ evidencia_alerta : "adjunta"
    alerta_campo ||--o{ decision_alerta : "registra decisiones"
    alerta_campo ||--o{ alerta_tarea : "origina"
    tarea ||--o{ alerta_tarea : "atiende"

    tarea {
        uuid id PK
        uuid organizacion_id "ref logica a organization (tenant)"
        uuid finca_id "ref logica a organization.finca"
        varchar codigo_tarea "UK por finca, ej. TSK-000123"
        varchar nombre
        text descripcion
        varchar tipo_tarea "UNIT, MASS"
        uuid proceso_id "ref logica a organization"
        uuid tipo_actividad_id "ref logica a organization"
        uuid espacio_id "ref logica, opcional"
        uuid formulario_id "ref logica a forms.formulario"
        uuid version_formulario_id "ref logica, inmutable al publicar"
        varchar estado "10 estados D-06"
        varchar prioridad "CRITICAL, HIGH, MEDIUM, LOW"
        timestamptz inicio_programado
        timestamptz fin_programado
        int duracion_estimada_minutos "origen pendiente DP-16"
        varchar condicion_carga "NORMAL, HIGH_LOAD, OVERLOAD al publicar"
        int version_tarea "sube con cada paquete nuevo"
        int revision "concurrencia optimista"
        uuid serie_recurrencia_id FK "opcional, DP-04"
        varchar causa_cancelacion
        uuid creado_por
        timestamptz creado_en
        uuid publicado_por
        timestamptz publicado_en
        timestamptz actualizado_en
    }
    serie_recurrencia {
        uuid id PK
        uuid finca_id "ref logica"
        jsonb regla "frecuencia, dias, fin; formato DP-04"
        timestamptz vigente_desde
        timestamptz vigente_hasta
        uuid creado_por
        timestamptz creado_en
    }
    asignacion_tarea {
        uuid id PK
        uuid tarea_id FK
        uuid usuario_id "ref logica a organization.usuario"
        varchar rol_en_tarea "OPERATOR, SUPERVISOR"
        uuid asignado_por
        timestamptz asignado_en
        timestamptz vigente_hasta "NULL = asignacion vigente"
    }
    historial_estado_tarea {
        uuid id PK
        uuid tarea_id FK
        varchar estado_anterior
        varchar estado_nuevo
        varchar origen "WEB, MOBILE, SYSTEM"
        text razon
        uuid cambiado_por
        timestamptz cambiado_en
        uuid correlation_id
    }
    dataset_tarea {
        uuid id PK
        uuid tarea_id FK "UK, un dataset por tarea"
        uuid version_formulario_id "con la que se calculo"
        int cantidad_incluidos
        int cantidad_excluidos
        varchar referencia_inventario "instante o version consultada"
        uuid calculado_por
        timestamptz calculado_en
        timestamptz congelado_en "NULL hasta publicar"
    }
    criterio_dataset_tarea {
        uuid id PK
        uuid dataset_id FK
        uuid filtro_disponible_id "ref logica a forms.filtro_disponible_formulario"
        varchar operador "EQ, NEQ, GT, GTE, LT, LTE, IN... (DP-03)"
        jsonb valor
        int orden
    }
    objetivo_tarea {
        uuid id PK
        uuid dataset_id FK
        uuid activo_id "ref logica a inventory.activo, UK con dataset"
        varchar estado_inclusion "INCLUDED, EXCLUDED, INCONSISTENT"
        text motivo
    }
    resultado_activo_tarea {
        uuid id PK
        uuid tarea_id FK
        uuid objetivo_id FK "solo objetivos INCLUDED"
        uuid activo_id "ref logica, UK con tarea_id"
        varchar estado "PENDING, IN_PROGRESS, SUCCESS, INCONSISTENT, NOT_EXECUTED, CANCELLED"
        uuid ejecutado_por "ref logica a organization.usuario"
        timestamptz inicio_en
        timestamptz fin_en
        timestamptz actualizado_en
    }
    excepcion_activo_tarea {
        uuid id PK
        uuid resultado_activo_id FK
        varchar causa_codigo "catalogo pendiente DP-20"
        text observacion
        uuid registrado_por
        timestamptz registrado_en
    }
    captura_ejecucion {
        uuid id PK "captureId generado en el movil"
        uuid tarea_id FK
        int version_tarea "del paquete recibido"
        uuid resultado_activo_id FK
        uuid activo_id "ref logica"
        uuid version_formulario_id "ref logica, debe coincidir con la tarea"
        uuid capturado_por "ref logica a organization.usuario"
        uuid dispositivo_id
        timestamptz iniciado_en
        timestamptz capturado_en
        timestamptz recibido_en
        varchar origen "MOBILE_OFFLINE, MOBILE_ONLINE, WEB"
        varchar estado_aplicacion "APPLIED, CONFLICT, REJECTED"
        uuid correlation_id
    }
    valor_captura {
        uuid id PK
        uuid captura_id FK
        uuid campo_id "ref logica a forms.campo_formulario, UK con captura"
        varchar codigo_campo
        text valor_texto
        numeric valor_numero
        timestamptz valor_fecha
        boolean valor_booleano
        jsonb valor_opciones "ids de opcion seleccionados"
    }
    validacion_captura {
        uuid id PK
        uuid captura_id FK
        uuid campo_id "ref logica"
        varchar regla
        varchar resultado "OK, FAILED"
        text mensaje
    }
    evidencia_ejecucion {
        uuid id PK
        uuid captura_id FK "opcional"
        uuid excepcion_id FK "opcional"
        uuid campo_id "ref logica, opcional"
        uuid archivo_id "ref logica al servicio de Archivos"
        varchar tipo "PHOTO, VIDEO, DOCUMENT, VOICE_NOTE, SIGNATURE"
        timestamptz registrado_en
    }
    alerta_campo {
        uuid id PK "alertId generado en el movil"
        uuid organizacion_id "ref logica"
        uuid finca_id "ref logica"
        uuid tarea_origen_id FK "opcional"
        uuid activo_id "ref logica, opcional"
        uuid espacio_id "ref logica, opcional"
        varchar tipo_alerta "incluye OTHER para novedades (D-08)"
        varchar prioridad "CRITICAL, HIGH, MEDIUM, LOW"
        varchar canal "VOICE, VIDEO, PHOTO, TEXT"
        varchar estado "pendiente DP-06"
        text descripcion
        uuid reportado_por
        timestamptz ocurrido_en
        timestamptz recibido_en
        boolean ocurrio_offline
    }
    evidencia_alerta {
        uuid id PK
        uuid alerta_id FK
        uuid archivo_id "ref logica al servicio de Archivos"
        varchar tipo "PHOTO, VIDEO, VOICE_NOTE"
    }
    decision_alerta {
        uuid id PK
        uuid alerta_id FK
        varchar decision "ATTENDED, CONVERTED, RESOLVED, DISCARDED"
        text nota
        uuid decidido_por
        timestamptz decidido_en
    }
    alerta_tarea {
        uuid alerta_id PK, FK
        uuid tarea_id PK, FK
        uuid creado_por
        timestamptz creado_en
    }
```

### Estados de la tarea (D-06) — códigos propuestos
| Estado oficial | Código |
| --- | --- |
| Borrador | `DRAFT` |
| Validada | `VALIDATED` |
| Programada | `SCHEDULED` |
| Disponible para campo | `AVAILABLE_FOR_FIELD` |
| En ejecución | `IN_PROGRESS` |
| Pausada | `PAUSED` |
| Finalizada | `FINISHED` |
| Por validar | `PENDING_VALIDATION` |
| Cerrada | `CLOSED` |
| Cancelada | `CANCELLED` |

No existen como estado: Aceptada, En desplazamiento, Vencida (Vencida es un indicador de cumplimiento).

## Reglas que el modelo debe proteger

| Regla | Cómo se protege | Fuente |
| --- | --- | --- |
| Solo estados oficiales | `CHECK estado IN (...)` con los 10 códigos | D-06 |
| Transiciones válidas | Máquina de estados en el dominio + fila en `historial_estado_tarea` por cada cambio | 05 §35 |
| Versión del formulario fija | El caso de uso rechaza cambiar `version_formulario_id` después de publicar | 10 §16 |
| Dataset congelado | `dataset_tarea.congelado_en` no nulo al publicar; luego no se editan `objetivo_tarea` | 05 regla 17 |
| Un resultado por activo y tarea | `UNIQUE (tarea_id, activo_id)` en `resultado_activo_tarea` | 05 regla 19 |
| Captura sin duplicados | `captura_ejecucion.id` = `captureId` del móvil (PK) | 09C §9, CI-009 |
| Un valor por campo y captura | `UNIQUE (captura_id, campo_id)` en `valor_captura` | 10 §11 |
| No ejecutado o cancelado con causa | Un caso de uso exige `excepcion_activo_tarea` para esos estados | 05 regla 22 |
| La alerta no se cierra al crear la tarea | La relación vive en `alerta_tarea`; el estado de la alerta cambia solo por `decision_alerta` | 05 §25 |
| Código de tarea único por finca | `UNIQUE (finca_id, codigo_tarea)` | — |

Índices (10 §27): `tarea (finca_id, estado, inicio_programado)`, `asignacion_tarea (usuario_id, vigente_hasta)`,
`objetivo_tarea (dataset_id, activo_id)`, `resultado_activo_tarea (tarea_id, estado)`,
`captura_ejecucion (tarea_id)`, `captura_ejecucion (activo_id, capturado_en)`, `alerta_campo (finca_id, estado, prioridad)`.

## Cambios respecto al borrador recibido

| # | Borrador recibido | Esta versión | Motivo |
| --- | --- | --- | --- |
| 1 | `objetivo_tarea.captura_criterios` (JSON repetido en cada activo) | Criterios a nivel de tarea en `dataset_tarea` + `criterio_dataset_tarea` | Los criterios son del dataset, no de cada activo (05 §12); se pueden consultar |
| 2 | No se registraba cuándo se calculó ni cuándo se congeló el dataset | `dataset_tarea` con cantidades, fechas y referencia a Inventario | Trazabilidad del dataset (05 regla 17, 10 §12) |
| 3 | `estado` "BORRADOR, PROGRAMADA, EN_EJECUCION, etc." | Los 10 estados exactos | D-06 |
| 4 | Sin `version_tarea` ni `revision` | Agregadas | Paquetes nuevos y concurrencia (09C §10.4, 10 §16) |
| 5 | Sin condición de carga ni datos de publicación | `condicion_carga`, `publicado_por`, `publicado_en` | 05 §14: registrar la condición de carga |
| 6 | `motivo_cancelacion` como texto en el resultado | Tabla `excepcion_activo_tarea` con causa, observación y evidencias | 05 §18: causa y, según el caso, observación o evidencia |
| 7 | Estados del resultado incompletos ("EJECUTADO_EXITO, CANCELADO, etc.") | 6 estados exactos | 10 §13, 06 §14 |
| 8 | Sin tiempos por activo | `inicio_en` y `fin_en` | 06 §23: tiempos por activo |
| 9 | Valores, evidencias y resultados de captura estaban en Formularios | `captura_ejecucion`, `valor_captura`, `validacion_captura`, `evidencia_ejecucion` en `planning` | 10 §4: el resultado no es de Formularios |
| 10 | `valor_tipado` como texto | Columnas tipadas (texto, número, fecha, sí/no, opciones) | Permite filtrar y calcular indicadores sin convertir texto |
| 11 | Captura sin identificador del móvil | `captura_ejecucion.id` = `captureId` | Idempotencia en la sincronización (09C §9) |
| 12 | `alerta_campo.tarea_convertida_id` (una sola tarea) | Tabla `alerta_tarea` (una alerta puede originar varias tareas) | 10 §20: `alert_task_link` |
| 13 | Estado de alerta "ABIERTA, ATENDIDA, RESUELTA, DESCARTADA" fijo | Historial en `decision_alerta`; el estado queda pendiente | DP-06 |
| 14 | Alerta sin evidencias, canal ni hora de recepción | `evidencia_alerta`, `canal`, `recibido_en`, `ocurrio_offline` | 06 §18–§19, 09C §10.6 |
| 15 | `regla_recurrencia` JSON en la tarea | Tabla `serie_recurrencia`; cada ocurrencia es una tarea | 05 §26: trazabilidad de cada instancia (forma final según DP-04) |
| 16 | Asignación sin historial | `asignado_por` y `vigente_hasta` | Reasignaciones trazables (DP-17) |
| 17 | FK físicas hacia Organización, Inventario y Formularios | Referencias lógicas sin FK | CONST §10 |

**Lo que el borrador hizo bien:** tarea con versión de formulario, separación unitaria y masiva, historial de estados,
inclusión y exclusión en el objetivo, resultado por activo y vínculo alerta → tarea.

## Pendientes que afectan este esquema
DP-03 (operadores de `criterio_dataset_tarea`), DP-04 (`serie_recurrencia`), DP-05 (transiciones), DP-06 (estado de
la alerta y si `alerta_campo` vive en este esquema), DP-16 (duración y carga), DP-17 (reasignación), DP-18 (recapturas:
hoy se permiten varias capturas por resultado), DP-20 (catálogo de causas), DP-22 (si las capturas van en `planning` o
en un esquema de ejecución), DP-25 (idioma de nombres).
