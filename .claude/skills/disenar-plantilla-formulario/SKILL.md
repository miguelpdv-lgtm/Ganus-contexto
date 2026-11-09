---
name: disenar-plantilla-formulario
description: Define la estructura de un formulario dinámico GANUS (claves de búsqueda, claves de contexto, filtros disponibles, secciones, campos, tipos, orden, obligatoriedad, condiciones, validaciones y catálogos) y el contrato JSON único que renderizan la vista previa, "Probar tarea", la web y el móvil. Usar al construir el constructor de formularios, el renderer, el modelo de datos de Formularios o al diseñar un formulario concreto.
---

# Diseñar plantilla de formulario

## Propósito
El formulario es una **parametrización guardada en base de datos**, no una pantalla (04 §1). Define qué se captura y
con qué reglas. Planificación lo usa para armar la tarea y Mobile lo ejecuta sin reinterpretarlo.

> FORMULARIOS = DEFINICIÓN · PLANIFICACIÓN = DATASET · MOBILE = EJECUCIÓN (04 §31)

## Fuentes
- 04 completo (en especial §2, §5, §7–§19, §22, §25, §27, §29, §30).
- 10 §9 (entidades de Formularios), §10 (tipos de dato), §33 (no guardar todo como JSON sin estructura).
- 06 §7, §12, §13 (lo que Mobile necesita para renderizar y validar).
- 09C §5, §10.3 (FormVersionReference), CT-FRM-01.
- CONST §7.3.

## Cuándo usarla
- Al construir el constructor de formularios (web).
- Al construir el renderer (web y móvil).
- Al crear las tablas del esquema `forms`.
- Al diseñar un formulario concreto con el negocio.

## Cuándo no usarla
- Publicar o versionar: `versionar-formulario`.
- Elegir la población: `calcular-dataset-poblacion`.
- Armar el paquete del móvil: `construir-paquete-trabajo`.

## Qué hace y qué no hace Formularios (04 §2, §5)

| Sí hace | No hace |
| --- | --- |
| Define claves de búsqueda y de contexto | Administrar activos (Inventario) |
| Define filtros disponibles | Decidir la población de una tarea (Planificación) |
| Define campos, tipo, orden y obligatoriedad | Programar, asignar ni manejar recurrencia (Planificación, D-03) |
| Define validaciones y catálogos | Crear maestros de procesos, actividades o espacios (Organización) |
| Previsualiza | Definir métodos de captura por campo (Mobile; ver DP-01, DP-14) |
| Versiona y publica (`versionar-formulario`) | Ejecutar reglas de Knowledge |
| Entrega la definición a Planificación y Mobile | Tener versiones distintas para web y móvil |

## Estructura (04 §7, §19)
```
FORMULARIO → VERSIÓN (tipo de activo) → CLAVES (búsqueda + contexto, con marca de filtro) + SECCIONES → CAMPOS → VALIDACIONES / CATÁLOGOS
```
Modelo de datos: `modelo-datos/formularios.md` (6 tablas, v1).

### 1. Claves de búsqueda (04 §6.2, §8)
- Son dimensiones del activo que sirven para **localizar o segmentar** la población: RFID, ID GANUS, especie, raza,
  sexo, edad, estado, categoría, peso…
- **Formularios no las crea.** Solo puede elegir atributos que Inventario publicó con "aptitud de búsqueda".
- Texto libre, notas, fotos, videos y documentos no son claves.
- Una clave de búsqueda **no es un campo de captura**.
- Cada versión declara **un tipo de activo** (`tipo_activo_id`), obligatorio para publicar: un formulario por tipo de
  activo (DP-10, acordado; por confirmar con Producto). Planificación lo usa para pedir la población.

### 2. Claves de contexto (04 §6.3, §9)
- Dimensiones que explican el resultado (04 §9). Referencian maestros de Organización; no se copian.
- **Acordado (DP-02, por confirmar con Producto):** proceso y tipo de actividad **no** se declaran como claves: toda
  tarea los tiene y su valor lo fija la tarea (compatible con D-04). El formulario declara solo los niveles de la
  jerarquía de espacios (por ejemplo, Potrero) y, cuando se definan, otros contextos autorizados.
- El valor de cada contexto se resuelve **en la tarea**, nunca en la plantilla ni en cada captura (04 §22, 09C §10.4).
- La v3.1 lista otras claves (activo principal, responsable, ubicación, fecha, código): sigue el conflicto DP-01.

### 3. Filtros disponibles (04 §6.4, §10)
- El formulario declara **sobre qué dimensiones** se puede filtrar marcando `es_filtro` en sus claves de búsqueda o de
  contexto (04 §10: los filtros «no son un maestro independiente»). No hay una tabla aparte de filtros.
- Una clave puede servir para identificar y no para filtrar (RFID suele tener `es_filtro = false`).
- Planificación pone los operadores y los valores concretos, y guarda la dimensión maestra (`atributo_id` o nivel de
  espacio), no el id de la fila del formulario.
- **CLAVE ≠ FILTRO:** la clave es la dimensión; el filtro es la condición aplicada.

### 4. Secciones y orden (04 §14)
- El formulario se organiza en secciones; cada campo tiene una posición explícita.
- El mismo orden se respeta en vista previa, "Probar tarea", web y móvil.
- Pendiente: secciones repetibles (DP-11). No se implementan.

### 5. Campos (04 §11)

| Propiedad | Definición |
| --- | --- |
| `fieldId` | Identificador del campo en esta versión (UUID). Cada versión tiene ids nuevos |
| `key` | Código del campo (`campo_formulario.codigo`): único en la versión y **estable entre versiones** (`peso`, `presenta_anomalia`) |
| `label` | Nombre funcional que ve el usuario |
| `type` | Uno de los 14 tipos de la tabla siguiente |
| `order` | Posición dentro de la sección |
| `required` | `ALWAYS`, `OPTIONAL` o `CONDITIONAL` (04 §15). Se deriva de `obligatorio` y de `condiciones.obligatorio_si` |
| `requiredWhen` / `visibleWhen` | Condición, solo si aplica (DP-11) |
| `preload` | Origen del valor precargado: Inventario, tarea, contexto o configuración (04 §11) |
| `defaultValue` | Valor inicial |
| `validations` | Reglas según el tipo (04 §16) |
| `catalog` | Catálogo maestro referenciado o lista local (04 §17) |
| `help` | Texto de ayuda opcional |

### 6. Tipos de campo (04 §12) y comportamiento

| `type` | Uso | Valor que se guarda | Validaciones permitidas |
| --- | --- | --- | --- |
| `SHORT_TEXT` | Nombre, código | texto | `minLength`, `maxLength`, `pattern` |
| `LONG_TEXT` | Observación, descripción | texto | `minLength`, `maxLength` |
| `INTEGER` | Cantidad, conteo, edad | número entero | `min`, `max` |
| `DECIMAL` | Peso, temperatura | número decimal | `min`, `max`, `decimals` |
| `DATE` | Fecha de aplicación | `YYYY-MM-DD` | `min`, `max` |
| `DATETIME` | Momento de un evento | ISO 8601 en UTC | `min`, `max` |
| `SINGLE_SELECT` | Sexo, estado, condición | `optionId` | catálogo obligatorio |
| `MULTI_SELECT` | Síntomas | lista de `optionId` | catálogo, `minSelected`, `maxSelected` |
| `BOOLEAN` | Sí / No | `true` / `false` | — |
| `PHOTO` | Evidencia fotográfica | lista de `evidenceId` | `minCount`, `maxCount` (DP-12) |
| `DOCUMENT` | Certificado, soporte | lista de `evidenceId` | `minCount`, `maxCount` (DP-12) |
| `VIDEO` | Evidencia audiovisual | lista de `evidenceId` | `minCount`, `maxCount` (DP-12) |
| `VOICE_NOTE` | Observación hablada | lista de `evidenceId` | `minCount`, `maxCount` (DP-12) |
| `SIGNATURE` | Firma del responsable | `evidenceId` | — |

**No son tipos de campo** (04 §12, §27): activo relacionado, ubicación, RFID, QR, báscula, sensor IoT, cámara, voz,
API, usuario, separador, título. El activo y la ubicación se resuelven con claves y contexto. La tecnología de
captura pertenece a Mobile. (Esto queda condicionado a **DP-01**, porque la v3.1 sí los incluye.)

Unidad de medida para `INTEGER` y `DECIMAL`: no está definida en el 04 (**DP-12**). No se agrega hasta que se apruebe.

### 7. Obligatoriedad, visibilidad y validación (04 §15–§16)
- Son tres cosas distintas: un campo puede ser visible y opcional, visible y obligatorio, o mostrarse solo con una condición.
- Obligatorio significa "debe tener un valor válido"; el valor puede venir precargado.
- Un campo obligatorio sin valor válido **bloquea completar** el formulario.

### 8. Catálogos (04 §17)
- `MASTER`: se referencia un catálogo de otro dominio; no se copia.
- `LOCAL`: opciones propias del formulario.
- Las opciones se guardan con la versión, para que los resultados viejos sigan siendo interpretables. Como la versión
  publicada no cambia, «desactivar» una opción es no copiarla a la versión siguiente; la anterior la conserva.
- Cada campo y cada opción tienen un `codigo` estable entre versiones (`peso_kg`, `normal`). El resultado guarda el
  código de la opción.

## Lenguaje de condiciones — Propuesta (DP-11, pendiente de aprobación)
Gramática mínima, igual en web y móvil:
```json
{ "all": [ { "field": "presenta_anomalia", "op": "eq", "value": true } ] }
```
- Combinadores: `all` (Y) y `any` (O).
- Operadores: `eq`, `neq`, `gt`, `gte`, `lt`, `lte`, `in`, `notIn`, `isEmpty`, `isNotEmpty`.
- Una condición solo puede referirse a campos con orden anterior (evita ciclos).
- Se usa en `visibleWhen` y `requiredWhen`.
Mientras DP-11 esté abierta, implementar solo condiciones de un campo con `eq` (el caso del 04 §16).

## Contrato JSON del formulario — Propuesta v0 (DP-07, DP-13, pendiente de acordar con Mobile)
La definición que reciben la vista previa, "Probar tarea", la web y el móvil. Se genera desde las tablas de `forms`;
no reemplaza el modelo relacional (10 §33).

```json
{
  "schemaVersion": "1.0",
  "formId": "uuid",
  "formVersionId": "uuid",
  "versionNo": 3,
  "code": "EVAL-REPRO",
  "name": "Evaluación reproductiva",
  "status": "PUBLISHED",
  "assetTypeId": "uuid",
  "publishedAt": "2026-10-21T15:00:00Z",
  "searchKeys": [
    { "attributeRef": "inventory:attr:rfid", "label": "RFID", "isFilter": false },
    { "attributeRef": "inventory:attr:sexo", "label": "Sexo", "isFilter": true },
    { "attributeRef": "inventory:attr:edad_meses", "label": "Edad (meses)", "isFilter": true }
  ],
  "contextKeys": [
    { "spaceLevelRef": "organization:space-level:potrero", "label": "Potrero", "isFilter": true }
  ],
  "sections": [
    {
      "sectionId": "uuid", "order": 1, "title": "Evaluación",
      "fields": [
        { "fieldId": "uuid", "key": "condicion_corporal", "label": "Condición corporal", "type": "DECIMAL",
          "order": 1, "required": "ALWAYS", "validations": [{ "type": "min", "value": 1 }, { "type": "max", "value": 5 }] },
        { "fieldId": "uuid", "key": "peso", "label": "Peso", "type": "DECIMAL", "order": 2, "required": "ALWAYS",
          "preload": { "source": "INVENTORY", "ref": "inventory:attr:peso_actual" } }
      ]
    },
    {
      "sectionId": "uuid", "order": 2, "title": "Hallazgos",
      "fields": [
        { "fieldId": "uuid", "key": "presenta_anomalia", "label": "¿Presenta anomalía?", "type": "BOOLEAN",
          "order": 1, "required": "ALWAYS" },
        { "fieldId": "uuid", "key": "tipo_anomalia", "label": "Tipo de anomalía", "type": "SINGLE_SELECT", "order": 2,
          "required": "CONDITIONAL",
          "requiredWhen": { "all": [{ "field": "presenta_anomalia", "op": "eq", "value": true }] },
          "visibleWhen":  { "all": [{ "field": "presenta_anomalia", "op": "eq", "value": true }] },
          "catalog": { "catalogId": "uuid", "kind": "LOCAL" } },
        { "fieldId": "uuid", "key": "foto", "label": "Foto", "type": "PHOTO", "order": 3, "required": "CONDITIONAL",
          "requiredWhen": { "all": [{ "field": "presenta_anomalia", "op": "eq", "value": true }] } },
        { "fieldId": "uuid", "key": "observacion", "label": "Observación", "type": "LONG_TEXT", "order": 4,
          "required": "OPTIONAL" },
        { "fieldId": "uuid", "key": "nota_voz", "label": "Nota de voz", "type": "VOICE_NOTE", "order": 5,
          "required": "OPTIONAL" }
      ]
    }
  ],
  "catalogs": [
    { "catalogId": "uuid", "kind": "LOCAL", "options": [
      { "optionId": "uuid", "code": "QUISTE", "label": "Quiste" },
      { "optionId": "uuid", "code": "OTRO", "label": "Otro" } ] }
  ]
}
```
Los valores de rango y los catálogos del ejemplo son ilustrativos. El caso funcional es el del 04 §30.

Las etiquetas (`label`) de claves se toman de Inventario u Organización al generar el contrato; `forms` no las copia.

Reglas del contrato:
- El renderer **no** agrega, quita ni reordena campos.
- Un `type` desconocido para el renderer bloquea la tarea y pide actualizar la app; no se ignora (propuesta).
- `schemaVersion` cambia cuando el contrato cambia de forma incompatible (09C §16).

## Persistencia (10 §9)

| Concepto (10 §9) | Tabla en `forms` (`modelo-datos/formularios.md`) |
| --- | --- |
| Formulario (identidad estable) | `formulario` |
| Versión, tipo de activo y registro de publicación | `version_formulario` |
| Sección | `seccion_formulario` |
| Campo, validaciones (`reglas`), condiciones y precarga | `campo_formulario` |
| Clave de búsqueda o de contexto, con marca de filtro | `clave_formulario` |
| Opciones locales (los catálogos maestros se referencian en el campo) | `opcion_campo` |
Índices sugeridos: `version_formulario (formulario_id, numero_version, estado)`, `campo_formulario (seccion_id, orden)` (10 §27).

## Procedimiento (04 §20, pasos 1 a 8)
1. Un usuario autorizado (Administrador según 09F §3; matriz exacta en DP-23) crea la plantilla en Borrador.
2. Indica el tipo de activo y elige las claves de búsqueda entre los atributos que Inventario publicó como aptos.
3. Elige los niveles de espacio que serán contexto, desde Organización (DP-02).
4. Marca qué claves sirven como filtro (`es_filtro`).
5. Construye secciones y campos uno a uno.
6. Define tipo, orden, obligatoriedad, precarga, condiciones y validaciones.
7. Configura catálogos.
8. Previsualiza con datos de ejemplo usando el mismo renderer que Mobile.
9. Publica con `versionar-formulario`.

## Prohibido
- Codificar formularios individuales en el código (04 §29).
- Crear maestros duplicados dentro de Formularios (procesos, espacios, razas…).
- Agregar métodos de captura como propiedad del campo (sujeto a DP-01).
- Guardar la población dentro del formulario.
- Crear un formulario lógico para web y otro para móvil (04 §18).
- Poner lógica de negocio en componentes visuales.

## Decisiones pendientes que afectan esta skill
DP-01 (documento que manda), DP-02 (claves de contexto: acordada, por confirmar con Producto), DP-08 (mapeo campo →
atributo), DP-10 (tipo de activo acordado; potrero y tareas sin activo abiertos), DP-11 (condiciones), DP-12 (unidad y límites de evidencia), DP-13 (canal web), DP-14 (mecanismo de captura),
DP-19 (formulario de alerta), DP-23 (permisos), DP-26 (definir estructuras no estandarizables con Formularios).

## Verificación (04 §27)
- El formulario se configura sin crear maestros paralelos.
- Las claves de búsqueda vienen de Inventario; las de contexto, de Organización.
- Los 14 tipos de campo funcionan; no existen métodos de captura ni "activo relacionado" o "ubicación" como tipo.
- Cada campo tiene orden explícito; el orden es el mismo en todos los canales.
- Un campo obligatorio sin valor bloquea completar.
- Las condiciones activan visibilidad y obligatoriedad según la definición.
- Vista previa y "Probar tarea" producen el mismo JSON.
