---
name: construir-paquete-trabajo
description: Construye el paquete de trabajo (WorkPackage) que Planificación publica por STAGING para que el móvil ejecute una tarea sin conexión, con tarea, versión exacta del formulario, dataset congelado, contexto, responsable, precargas y condiciones de ejecución. Usar al publicar una tarea, al diseñar el contrato con el equipo móvil o al cambiar qué información recibe el operador.
---

# Construir paquete de trabajo

## Propósito
Entregar al móvil **todo lo necesario para ejecutar exactamente una tarea**, offline, sin que tenga que consultar
nada más ni tomar decisiones de planificación (00 §15, 06 §7).

## Fuentes
- 05 §16 (contenido mínimo del paquete), §27 regla 24.
- 06 §7 (paquete), §8 (base local), §24 (cambios posteriores a la descarga).
- 09C §5 (Planificación → Mobile por STAGING; Inventario → Mobile: AssetSnapshot), §9, §10.4 (WorkPackage),
  §12.2 (publicación al campo), §17 (CT-PLN-01).
- 04 §22 (contrato funcional hacia Mobile).
- 10 §14 (`sync_package`, `sync_package_item`, `device`, `sync_checkpoint`).

## Cuándo usarla
- Al implementar la publicación de una tarea.
- Al acordar el contrato con el equipo móvil.
- Al agregar o quitar datos que recibe el operador.

## Contenido obligatorio (05 §16, 06 §7, 04 §22, 09C §10.4)
- Identidad de la tarea (`taskId`) y su versión (`taskVersion`).
- Formulario y versión publicada, con la **definición completa** (campos en orden, obligatoriedad, condiciones,
  validaciones, catálogos).
- Dataset congelado con lo necesario para identificar cada activo y precargar valores.
- Contexto: finca, proceso, tipo de actividad, espacio.
- Responsable.
- Fechas, ventana y prioridad.
- Valores precargados autorizados y evidencias requeridas.
- Condiciones de ejecución y cierre.
- Metadatos de trazabilidad y versión.

## Estructura — Propuesta v0 (DP-07, pendiente de acordar con Mobile y STAGING)
El sobre es el de `definir-envelope-staging` con `eventType: "WorkPackage.v1"`. Este es el `payload`:
```json
{
  "packageId": "uuid",
  "taskId": "uuid",
  "taskVersion": 1,
  "farmId": "uuid",
  "issuedAt": "2026-10-21T15:00:00Z",
  "task": {
    "code": "T-000245",
    "name": "Evaluación reproductiva hembras adultas Norte",
    "description": "…",
    "type": "MASS",
    "priority": "HIGH",
    "schedule": { "start": "2026-10-22T06:00:00-05:00", "end": "2026-10-22T12:00:00-05:00" }
  },
  "assignment": { "userId": "uuid", "displayName": "Carlos Ramírez" },
  "context": {
    "processId": "uuid", "processName": "Manejo animal",
    "activityTypeId": "uuid", "activityTypeName": "Evaluación reproductiva",
    "spaceId": "uuid", "spaceName": "Potrero P-03"
  },
  "form": { "...": "definición completa de la versión, según disenar-plantilla-formulario" },
  "population": [
    {
      "assetId": "uuid",
      "displayName": "Vaca 803",
      "assetTypeId": "uuid",
      "identifiers": [ { "type": "RFID", "value": "982000123456789" }, { "type": "GANUS_ID", "value": "GAN-0001" } ],
      "searchKeyValues": { "sexo": "Hembra", "edad_meses": 38 },
      "preload": { "peso": 412.0 }
    }
  ],
  "executionConditions": {
    "cancellationCauses": [ { "code": "NOT_FOUND", "label": "Activo no encontrado", "requiresNote": false, "requiresEvidence": false } ]
  }
}
```
Cumple el mínimo del 09C §10.4 (`packageId`, `taskId`, `taskVersion`, `farmId`, `form`, `population`,
`assignment`, `context`, `issuedAt`) y lo amplía con lo que piden el 05 §16 y el 06 §7.

Notas:
- `population[]` es una **instantánea de solo lectura** (AssetSnapshot de Inventario, 09C §5). Solo lleva los datos que
  la tarea necesita; no la ficha completa del activo.
- `form` lleva la definición completa porque el móvil trabaja offline.
- Las causas de cancelación vienen del catálogo que se defina en DP-20.

## Cuándo se genera (09C §12.2)
1. Planificación valida tarea, formulario y población (`validar-publicacion-tarea`).
2. En la transacción de publicación se arma el paquete, se guarda y se escribe en el Outbox con
   `idempotencyKey = planning:task:publish:<taskId>:<taskVersion>`.
3. Se publica TaskPublished.v1.
4. STAGING lo entrega al dispositivo autorizado del responsable.
5. El móvil confirma recepción sin modificar la población.

## Reglas
1. Planificación no entrega el trabajo directo al móvil; siempre por STAGING (05 §16).
2. La versión del formulario del paquete es la exacta que la tarea guardó (06 §7).
3. La población la calcula Planificación; el móvil no la recalcula ni la amplía (09C §10.4).
4. Un paquete descargado no cambia en silencio. Un cambio autorizado genera un paquete nuevo con `taskVersion + 1`
   y queda trazado (06 §24, DP-17).
5. Solo datos de la finca y del responsable autorizados; sin secretos ni rutas internas de archivos (09C §14).
6. El paquete no contiene lógica de negocio ejecutable: solo datos y la definición declarativa del formulario.

## Prohibido
- Enviar una tarea que no esté Disponible para campo (v3.1 §9.4).
- Incluir activos fuera del dataset congelado.
- Permitir que el móvil edite la definición del formulario o la tarea.
- Copiar al paquete datos de Inventario que la tarea no necesita.

## Decisiones pendientes que afectan esta skill
- **DP-07:** estructura final, datos del activo que viajan y catálogos dentro o aparte.
- **DP-13:** si la web también consume este paquete para ejecutar.
- **DP-14:** cómo decide el móvil el mecanismo de captura (báscula, RFID, voz) sin que el formulario lo diga.
- **DP-17:** reprogramar o reasignar después de publicar.
- **DP-20:** catálogo de causas de cancelación.

## Verificación
- CI-007: el paquete trae la versión exacta del formulario y la población calculada.
- Publicar dos veces la misma `taskVersion` no genera dos paquetes.
- El paquete se valida contra su JSON Schema (prueba de contrato).
- Un operador de otra finca no recibe el paquete.
- Con el paquete y sin conexión, el móvil puede renderizar el formulario y ejecutar todos los activos.
