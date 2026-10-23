# Esquema `forms` — Formularios dinámicos

**Estado:** borrador v0 para revisión del equipo.
**Dueño:** Formularios y Planificación.
**Fuentes:** 04 v1.3 (§7–§19, §24), 10 §9–§10, skills `disenar-plantilla-formulario` y `versionar-formulario`.

## Qué guarda y qué no
- **Guarda** la definición del formulario: versiones, secciones, campos, validaciones, condiciones, catálogos
  propios, claves y filtros disponibles.
- **No guarda** tareas, población ni resultados de captura. Los resultados pasaron al esquema `planning`
  (ver [planificacion.md](planificacion.md)), porque el 10 §4 dice que Formularios no es dueño del resultado de ejecución.

## Diagrama

Imagen: [img/formularios.png](img/formularios.png) (GitHub también dibuja el bloque Mermaid de abajo).

```mermaid
erDiagram
    formulario ||--o{ version_formulario : "versiona"
    version_formulario ||--o{ evento_version_formulario : "registra"
    version_formulario ||--o{ seccion_formulario : "organiza"
    seccion_formulario ||--o{ campo_formulario : "agrupa"
    version_formulario ||--o{ campo_formulario : "contiene"
    campo_formulario ||--o{ validacion_campo_formulario : "valida con"
    campo_formulario ||--o{ condicion_campo_formulario : "se condiciona con"
    version_formulario ||--o{ catalogo_formulario : "posee"
    catalogo_formulario ||--o{ opcion_catalogo_formulario : "contiene"
    catalogo_formulario |o--o{ campo_formulario : "provee opciones"
    version_formulario ||--o{ clave_formulario : "define claves"
    version_formulario ||--o{ filtro_disponible_formulario : "habilita filtros"

    formulario {
        uuid id PK
        uuid organizacion_id "ref logica a organization (tenant)"
        uuid finca_id "ref logica, NULL = toda la organizacion (DP-24)"
        varchar codigo UK "unico por organizacion, ej. FRM-EVAL-REPRO"
        varchar nombre
        text descripcion
        uuid creado_por
        timestamptz creado_en
        timestamptz actualizado_en
    }
    version_formulario {
        uuid id PK
        uuid formulario_id FK
        int numero_version "UK con formulario_id"
        varchar estado "DRAFT, PUBLISHED, OBSOLETE"
        jsonb definicion_publicada "contrato JSON generado al publicar, solo lectura"
        varchar version_contrato "schemaVersion del JSON, ej. 1.0"
        int revision "concurrencia optimista"
        uuid creado_por
        timestamptz creado_en
        timestamptz publicado_en
        timestamptz obsoleto_en
    }
    evento_version_formulario {
        uuid id PK
        uuid version_formulario_id FK
        varchar accion "CREATED, PUBLISHED, OBSOLETED"
        uuid usuario_id "ref logica a organization.usuario"
        timestamptz ocurrido_en
        text resumen_cambios
        uuid correlation_id
    }
    seccion_formulario {
        uuid id PK
        uuid version_formulario_id FK
        varchar nombre
        text descripcion
        int orden "UK con version_formulario_id"
    }
    campo_formulario {
        uuid id PK
        uuid version_formulario_id FK
        uuid seccion_id FK
        varchar codigo_campo "estable entre versiones, UK con version"
        varchar nombre "etiqueta visible"
        varchar tipo_campo "14 tipos: SHORT_TEXT, DECIMAL, PHOTO..."
        int orden "UK con seccion_id"
        varchar requerimiento "ALWAYS, OPTIONAL, CONDITIONAL"
        text valor_defecto
        text texto_ayuda
        uuid catalogo_id FK "lista local, opcional"
        varchar catalogo_maestro_ref "catalogo de otro dominio, opcional"
        varchar precarga_origen "INVENTORY, TASK, CONTEXT, CONFIGURATION"
        varchar precarga_ref "atributo o dato de origen"
    }
    validacion_campo_formulario {
        uuid id PK
        uuid campo_id FK
        varchar tipo_regla "MIN, MAX, MIN_LENGTH, MAX_LENGTH, PATTERN, DECIMALS..."
        text valor_regla
        text mensaje_error
    }
    condicion_campo_formulario {
        uuid id PK
        uuid campo_id FK
        varchar tipo "VISIBLE_WHEN, REQUIRED_WHEN"
        jsonb expresion "gramatica DP-11"
    }
    catalogo_formulario {
        uuid id PK
        uuid version_formulario_id FK "siempre local a la version"
        varchar codigo
        varchar nombre
    }
    opcion_catalogo_formulario {
        uuid id PK
        uuid catalogo_id FK
        varchar codigo_opcion "UK con catalogo_id"
        varchar etiqueta
        int orden
        boolean activa "se desactiva, nunca se borra"
    }
    clave_formulario {
        uuid id PK
        uuid version_formulario_id FK
        varchar tipo_clave "SEARCH, CONTEXT"
        varchar origen "INVENTORY_ATTRIBUTE, ORGANIZATION_DIMENSION"
        uuid referencia_id "ref logica al atributo o dimension"
        varchar codigo_referencia
        varchar etiqueta
    }
    filtro_disponible_formulario {
        uuid id PK
        uuid version_formulario_id FK
        varchar origen "INVENTORY_ATTRIBUTE, ORGANIZATION_SPACE_HIERARCHY"
        uuid referencia_id "ref logica al atributo o tipo de espacio"
        varchar codigo_referencia
        varchar etiqueta
    }
```

## Reglas que el modelo debe proteger

| Regla | Cómo se protege | Fuente |
| --- | --- | --- |
| Una versión publicada no cambia | El caso de uso rechaza cambios; opcionalmente un trigger que impida `UPDATE` en hijos de versiones `PUBLICADO` | 04 §24, 10 §26 |
| Una sola numeración por formulario | `UNIQUE (formulario_id, numero_version)` | 10 §27 |
| Orden explícito y sin repetir | `UNIQUE (seccion_id, orden)`, `UNIQUE (version_formulario_id, orden)` en secciones | 04 §14 |
| Código de campo único por versión | `UNIQUE (version_formulario_id, codigo_campo)` | 04 §11 |
| Campo de selección con catálogo | `CHECK`: `SINGLE_SELECT` y `MULTI_SELECT` requieren `catalogo_id` o `catalogo_maestro_ref` | 04 §17 |
| Opciones históricas se conservan | `activa = false`, nunca `DELETE` | 04 §17 |
| Solo tipos permitidos | `CHECK tipo_campo IN (...)` con los 14 tipos | 04 §12 |
| El JSON publicado coincide con las tablas | Se genera al publicar desde las tablas; nunca se edita a mano | 04 §18, 10 §33 |

Tipos de campo (04 §12): `SHORT_TEXT`, `LONG_TEXT`, `INTEGER`, `DECIMAL`, `DATE`, `DATETIME`, `SINGLE_SELECT`,
`MULTI_SELECT`, `BOOLEAN`, `PHOTO`, `DOCUMENT`, `VIDEO`, `VOICE_NOTE`, `SIGNATURE`.

**Códigos de valores:** los estados, tipos y orígenes se guardan con los mismos códigos del contrato JSON
(skill `disenar-plantilla-formulario`) y del documento 09 (`PUBLISHED`), para que base, API y móvil hablen igual.
La etiqueta en español es solo de presentación. Los nombres de tablas y columnas siguen en español, como el resto del
equipo, hasta que se decida DP-25.

Índices: `version_formulario (formulario_id, numero_version, estado)`, `campo_formulario (version_formulario_id, orden)` (10 §27).

## Cambios respecto al borrador recibido

| # | Borrador recibido | Esta versión | Motivo |
| --- | --- | --- | --- |
| 1 | `formulario` tenía `proceso_id` y `tipo_actividad_id` | Se quitan; el proceso y la actividad viven en la tarea y como claves de contexto | D-04: el formulario no asigna procesos ni actividades (ver DP-02) |
| 2 | `formulario` tenía `estado` | El estado solo está en `version_formulario` | 04 §24: el estado es de cada versión |
| 3 | `formulario` no tenía organización ni finca | Se agregan `organizacion_id` y `finca_id` | Aislamiento por cliente (09C §14); alcance pendiente (DP-24) |
| 4 | No existían los filtros disponibles | Nueva tabla `filtro_disponible_formulario` | 04 §7 y §10: componente obligatorio |
| 5 | Condiciones mezcladas en validaciones (`REQUIRED_IF`) y sin "visible si" | Nueva tabla `condicion_campo_formulario` | 04 §15: visibilidad, obligatoriedad y validación son conceptos distintos |
| 6 | `clave_formulario.codigo_clave` texto y `esta_habilitado` | `origen` + `referencia_id` estable; se quita `esta_habilitado` | Referencias por ID estable (10 §3); una versión publicada no se habilita ni deshabilita |
| 7 | `catalogo_formulario.version_formulario_id` nulo para catálogos maestros | Solo catálogos locales; los maestros se referencian en el campo (`catalogo_maestro_ref`) | 04 §17: el maestro se referencia, no se copia |
| 8 | `origen_precarga`: `INVENTORY_ATTRIBUTE`, `PREVIOUS_VALUE` | `precarga_origen` + `precarga_ref`, con los orígenes del 04 | 04 §11: Inventario, tarea, contexto o configuración. "Valor anterior" no está definido |
| 9 | Faltaba valor por defecto | `valor_defecto` | 04 §11 |
| 10 | `tipo_campo` con valores genéricos (`TEXTO`, `NUMERO`, `SELECCION`) | Los 14 tipos exactos, con los códigos del contrato JSON | 04 §12 |
| 11 | `publicacion_formulario` solo registraba publicaciones | `evento_version_formulario` con creación, publicación y obsolescencia | 04 §24, trazabilidad |
| 12 | `resultado_ejecucion_formulario`, `valor_…` y `evidencia_…` dentro de Formularios | Se mueven a `planning` | 10 §4: Formularios no es dueño del resultado |
| 13 | `definicion_esquema` JSON sin regla | `definicion_publicada` generado al publicar y de solo lectura | Evita dos fuentes de verdad (10 §33) |

**Lo que el borrador hizo bien:** no incluyó métodos de captura, separó versión y formulario, guardó catálogos y
opciones por versión, y usó UUID.

## Pendientes que afectan este esquema
DP-02 (claves de contexto), DP-08 (mapeo campo → atributo maestro: hoy no hay columna), DP-09 (versiones
simultáneas), DP-10 (formulario para varios tipos de activo), DP-11 (gramática de `expresion`), DP-12 (unidad de
medida y límites de evidencia: hoy no hay columna), DP-24 (alcance del formulario), DP-25 (idioma de nombres).
