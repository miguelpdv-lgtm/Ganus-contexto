# GANUS TRACK · Anexos (Excel, presentación y diagrama de flujo)

> **Fuente de solo lectura.** Transcripción a Markdown de `GANUS_TRACK_INPUT_D03_v1.xlsx`,
> `GANUS_TRACK_D00_Faltantes_por_Activo_y_Momento.xlsx`, `ejemplos Planificacion ganusOPE-TRACK.pptx` y
> `Flujo integral de GANUS Track.png`. TRACK no es un módulo del responsable de este repositorio: se consulta, no se modifica.
> Las hojas de datos masivos (miles de filas simuladas) no se transcriben; se describen sus columnas y un resumen.

---

## 1. Flujo integral de GANUS TRACK (diagrama)

D00 recibe, normaliza y guarda. D03 y siguientes interpretan y generan valor.

| Paso | Etapa | Qué ocurre |
| --- | --- | --- |
| 1 | Campo | Animales con dispositivos LoRaWAN. **GPS**: coordenadas y batería, 4 veces al día. **TAG**: temperatura, pasos y batería, 4 veces al día |
| 2 | Comunicación | Gateway LoRaWAN en la finca recibe la señal; el Network Server gestiona la red y entrega los mensajes a GANUS |
| 3 | D00 · Recepción y normalización | 1) Recibir los mensajes. 2) Decodificar la trama del fabricante a datos entendibles. 3) Estandarizar al formato GANUS. 4) Guardar la trama original y el dato normalizado |
| 4 | Almacenamiento D00 (base intermedia) | `track_telemetry_raw` (trama original), `track_telemetry` (dato estandarizado), `track_device` (información técnica del dispositivo) |
| 5 | D03 · TRACK | Asocia el dispositivo con el animal (desde Inventario), determina la ubicación en la finca (D01 · mapa digital) y evalúa condiciones y genera eventos (D02 · reglas) |
| 6 | Resultado en GANUS | Ubicación de animales, historial de movimiento, temperatura y pasos, estado de batería, eventos y alertas, reportes |

Dominios involucrados: Campo (TAG y GPS) → Comunicación (gateway + network server) → D00 (recepción y normalización) →
D03 TRACK (contexto, calidad y eventos) → D01 (mapa digital de la finca) → D02 (reglas y condiciones) → D04 (alertas y gestión).

Columnas de las tablas D00 según el diagrama:

| Tabla | Columnas |
| --- | --- |
| `track_telemetry_raw` | id, received_at, protocol, device_key, gateway_id, message_id, payload_raw, rssi, snr, sequence_no, status, decoder_version |
| `track_telemetry` | id, farm_id, device_key, device_type (TAG/GPS), observed_at, received_at, latitude, longitude, temperature_c, steps, battery_pct, gateway_id, quality_state, raw_id, decoder_version |
| `track_device` | device_key, device_id, device_type, manufacturer, model, protocol, decoder_id, decoder_version, status, farm_id |

Ejemplo del diagrama: un TAG reporta 37.3 °C, 4.236 pasos y 90 % de batería; un GPS reporta latitud 10.960614,
longitud -74.800382 y 88 % de batería. La relación animal–dispositivo (A001 ↔ TAG-001 / GPS-001) viene de Inventario.

## 2. Contrato D00 → TRACK (`GANUS_TRACK_INPUT_D03_v1.xlsx`)

#### Contrato D00 a TRACK

| Propiedad | Valor |
| --- | --- |
| Nombre de tabla | track_input |
| Propósito | Tabla de entrada de GANUS TRACK resultante de D00 NORMALIZED + cruces con registro de dispositivos e INVENTARIO. |
| Granularidad | Una fila por mensaje normalizado/medición decodificada. |
| Filas | 4025 |
| Identidades de activos sin resolver | 0 |
| Dispositivos sin resolver | 0 |
| Duplicados señalados | 67 |
| Errores de decodificación | 24; se mantienen fuera de track_input porque no generan medición normalizada. Consultar paquete D00 anterior. |
| Regla de relación | device_key (DevEUI) -> Registro de dispositivos -> asignación vigente -> asset_id/ActivoID -> ficha del animal. |
| Ubicación | latitude/longitude son observaciones GPS. No se asigna potrero en D00; TRACK debe cruzar coordenadas con polígonos GIS reales. |
| Datos sintéticos | Todos los registros de este paquete son simulados; asignaciones y decoder no validados con hardware real. |
| Contrato | D00_TO_TRACK_INPUT v1.0 — propuesta de dataset de prueba. |

#### Diccionario de campos

| Campo | Definición |
| --- | --- |
| telemetry_id | Identificador único de la fila normalizada. |
| raw_id | Referencia al mensaje RAW de origen. |
| device_key / device_id | Identidad técnica del dispositivo (DevEUI y DeviceID interno). |
| asset_id / GANUS_ID / RFID_ID | Identidad del animal resuelta desde el registro de INVENTARIO. |
| assignment_id / assignment_valid_from / assignment_valid_to | Trazabilidad de la asignación del dispositivo al activo. |
| received_at / observed_at / time_basis | Distingue recepción de medición; observed_at queda nulo si la fuente no transmite hora de medición. |
| latitude / longitude | Coordenadas de GPS, solo si el mensaje procede de GPS. No representan por sí solas un potrero. |
| temperature_c / steps_count / battery_pct | Mediciones disponibles según el tipo de dispositivo. |
| quality_status / quality_errors | Estado de calidad para que TRACK pueda excluir, advertir o tratar registros según política. |
| territory_status | D00 no resuelve potrero/geocerca; queda explícitamente pendiente para la etapa espacial de TRACK. |
| simulation_flag | TRUE indica que el dataset es de prueba. |

#### Reglas del control

| Regla | Definición |
| --- | --- |
| Objetivo | Identificar exactamente qué activo/dispositivo no reportó y en qué momento se esperaba el mensaje. |
| Periodo | 2026-10-05 00:00 UTC a 2026-10-12 00:00 UTC (fin exclusivo). |
| Frecuencia de referencia | Un mensaje cada 4 horas por dispositivo: 00:00, 04:00, 08:00, 12:00, 16:00 y 20:00 UTC. |
| Unidad de evaluación | Activo + dispositivo (TAG/GPS) + momento esperado. |
| Estado FALTANTE | No existe recepción RAW asociada al slot esperado en la simulación. |
| Estado RECIBIDO | Existe al menos una recepción no marcada como entrega duplicada para ese slot. |
| Retrasados | Se muestran como recibidos si hay una recepción asociada al slot; la demora se informa por separado. |
| Malformed | Cuenta como recepción de transporte, pero se marca aparte porque no aporta medición utilizable. |
| Importante | El calendario de slots se deriva de la simulación. En producción debe salir de la configuración real por dispositivo y considerar tolerancia de retraso, ventanas horarias y cambios de configuración. |
| Datos | Simulación GANUS_TEST_V1; no representa telemetría productiva validada con fabricante. |

#### Hojas de datos (no transcritas)

| Hoja | Filas | Contenido |
| --- | --- | --- |
| `track_input` | 4.025 | Una fila por mensaje normalizado. Columnas: telemetry_id, raw_id, received_at, observed_at, time_basis, device_key, device_id, device_type, registry_device_type, asset_id, GANUS_ID, RFID_ID, Clave_antigua, asset_code, Especie, asset_status, assignment_id, assignment_valid_from, assignment_valid_to, assignment_is_current, device_assignment_status, latitude, longitude, temperature_c, steps_count, steps_semantics, battery_pct, f_port, f_cnt, payload_version, decoder_version, schema_version, quality_status, quality_errors, source_payload_base64, source_payload_hex, simulation_flag, asset_resolution_status, track_processing_status, territory_status, interface_note |
| `Referencia asignaciones` | 100 | Asignación dispositivo → activo: AsignacionID, ActivoID, DeviceID, DevEUI, Tipo_dispositivo, Fecha_inicio_vigencia, Fecha_fin_vigencia, Es_vigente, Motivo_fin, Regla_integridad, Origen_dato. 50 animales simulados con un TAG y un GPS cada uno |
| `Resumen por activo` | 50 | Mensajes esperados, recibidos y faltantes por activo. 84 esperados por activo; 218 faltantes en total; completitud entre 88,1 % y 100 % (promedio 94,8 %) |
| `Faltantes y momento` | 218 | Cada momento esperado sin recepción, por activo y dispositivo |
| `Auditoria slots esperados` | 4.200 | Cada slot esperado con recepciones, mensajes malformados o retrasados y estado RECIBIDO o FALTANTE |

Todos los datos son **simulados** (`GANUS_TEST_V1`), no telemetría real.

## 3. Ejemplo de planificación GANUS Operativo ↔ TRACK (presentación)

La presentación muestra el flujo Organización → Inventario → Formularios → Planificación → Staging Area → App móvil,
con el formulario y el dataset viajando juntos.

Ejemplo "Formulario de vacunación / recolección de peso":
- **Formulario:** código, nombre, campo de búsqueda, campo de contexto y campos: Lote vacuna (decimal), Foto (1), Documento (1) y Firma.
- **Planificación:** "Formulario vacuna, asígnelo a CPerez, 20 activos".
- **Dataset:** información del Inventario (20 animales).
- **App móvil:** recibe los datos de la tarea (fecha inicial, fecha fin, cantidad de activos, operador), el formulario y el dataset.
