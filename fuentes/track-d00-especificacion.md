# GANUS TRACK · D00 Especificación de desarrollo v1.1

> **Fuente de solo lectura.** Convertido a Markdown desde `GANUS_TRACK_D00_ESPECIFICACION_DESARROLLO_v1_1_MODELO_DATA_INTERACCION (1).docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**GANUS TRACK**

**D00 · RECEPCIÓN, DECODIFICACIÓN, NORMALIZACIÓN Y ALMACENAMIENTO DE TELEMETRÍA**

Especificación funcional y técnica para desarrollo · Versión 1.0 · Octubre 2026

*Objetivo: que desarrollo implemente la entrada de datos de campo a GANUS TRACK sin mezclar responsabilidades de negocio.*

# 1. DECISIÓN DE ARQUITECTURA

Para el MVP se asume que la finca ya tiene infraestructura de comunicación, que el mapa territorial ya fue levantado y que los animales existen en GANUS OPERATIVO / INVENTORY. D00 resuelve únicamente la adaptación de la información que llega desde los dispositivos.

> DISPOSITIVO → LoRaWAN → GATEWAY → NETWORK SERVER → D00 → TELEMETRÍA NORMALIZADA → D03

| **D00 hace**               | **D00 NO hace**               |
|----------------------------|-------------------------------|
| Recibir mensajes           | No identifica animales        |
| Conservar RAW              | No modifica Inventory         |
| Decodificar payload        | No calcula potreros/geocercas |
| Normalizar valores         | No evalúa reglas              |
| Guardar RAW + NORMALIZED   | No genera alertas de negocio  |
| Publicar contrato estándar | No crea órdenes               |

# 2. EL CONCEPTO CLAVE: LA TRAMA

El dispositivo no entrega una fila de una tabla GANUS. Entrega una trama/payload. LoRaWAN transporta esa información; el fabricante define el contenido de aplicación.

> TAG → payload LoRaWAN → A0 1B 11 FF 00 01 08 C5 ...

El decoder transforma esa trama en datos entendibles:

> temperature_c = 37.3 \| steps = 4236 \| battery_pct = 90 \| observed_at = 06:00

Principio: el fabricante puede cambiar; el contrato normalizado que sale de D00 debe permanecer estable.

# 3. LO QUE YA EXISTE ANTES DE D00

| **Elemento**                  | **Responsable** | **D00**                    |
|-------------------------------|-----------------|----------------------------|
| Finca y tenant                | GANUS           | Solo recibe farm_id        |
| Mapa y espacios físicos       | D01             | No los administra          |
| Animal                        | INVENTORY       | No lo crea ni modifica     |
| Asociación animal-dispositivo | Inventory/D03   | No la inventa              |
| Reglas/geocercas              | D02             | No las evalúa              |
| Calidad/evaluabilidad         | D03             | Recibe el dato normalizado |
| Alertas                       | D04             | No las genera              |

# 4. FLUJO COMPLETO

1.  1\. CAMPO: TAG y GPS están instalados y registrados técnicamente.

2.  2\. TRANSMISIÓN: el dispositivo construye su payload y transmite por LoRaWAN.

3.  3\. GATEWAY: la infraestructura de la finca recibe la señal.

4.  4\. NETWORK SERVER: entrega el mensaje a la integración.

5.  5\. D00 RECIBE: registra mensaje, device_key, gateway y metadatos.

6.  6\. D00 DECODIFICA: aplica el decoder correspondiente a fabricante/modelo.

7.  7\. D00 NORMALIZA: convierte a unidades y nombres estándar GANUS.

8.  8\. D00 GUARDA: conserva RAW y crea la observación normalizada.

9.  9\. D03: valida calidad, asociación, frescura y evaluabilidad.

10. 10\. INVENTORY: D03 resuelve a qué activo corresponde el device_key.

11. 11\. D01: contextualiza coordenadas en el mapa digital.

12. 12\. D02: aporta la regla/geocerca aplicable.

13. 13\. D03: determina CUMPLE, INCUMPLE, NO EVALUABLE o SIN REGLA.

14. 14\. D04: gestiona una alerta cuando corresponda.

# 5. QUÉ DEBE ENTREGAR EL FABRICANTE

- Identificador único: DevEUI, Device ID o equivalente.

- Protocolo utilizado.

- Formato exacto del payload.

- Descripción byte a byte/campo a campo.

- Unidad, rango, escala y precisión de cada variable.

- Signed/unsigned, endianess, offset y multiplicadores cuando apliquen.

- Ejemplos reales de payload y resultado esperado.

- Campos opcionales y comportamiento cuando faltan.

- Periodicidad de transmisión.

- Formato de batería, actividad, temperatura y GPS.

- Sequence/contador si existe.

- Metadata de gateway/radio disponible.

- Comportamiento después de pérdida de cobertura.

- Firmware y versión del payload.

- Decoder/documentación oficial.

Sin esta información, la integración del dispositivo no está técnicamente cerrada.

# 6. EJEMPLO TAG

| **Dato**    | **Ejemplo**         |
|-------------|---------------------|
| device_key  | 70B3D57EDTAG01      |
| device_type | TAG                 |
| temperatura | 37.3 °C             |
| pasos       | 4.236               |
| batería     | 90 %                |
| observed_at | 2026-09-21 06:00:00 |

> RAW: A0 1B 11 FF 00 01 08 C5 ...
>
> NORMALIZED: {"device_key":"70B3D57EDTAG01","device_type":"TAG","temperature_c":37.3,"steps":4236,"battery_pct":90,"observed_at":"2026-09-21T06:00:00"}

# 7. EJEMPLO GPS

| **Dato**    | **Ejemplo**         |
|-------------|---------------------|
| device_key  | 70B3D57EDGPS01      |
| device_type | GPS                 |
| latitud     | 10.960614           |
| longitud    | -74.800382          |
| batería     | 88 %                |
| observed_at | 2026-09-21 06:00:00 |

> NORMALIZED: {"device_key":"70B3D57EDGPS01","device_type":"GPS","latitude":10.960614,"longitude":-74.800382,"battery_pct":88,"observed_at":"2026-09-21T06:00:00"}

D00 no decide si la coordenada está dentro de P17. Eso es responsabilidad posterior de TRACK.

# 8. TABLAS MVP

## 8.1 track_telemetry_raw

| **Campo**       | **Oblig.** | **Propósito**                    |
|-----------------|------------|----------------------------------|
| id              | Sí         | ID interno RAW                   |
| received_at     | Sí         | Momento de recepción             |
| protocol        | Sí         | Ej. LoRaWAN                      |
| device_key      | Sí         | ID técnico                       |
| gateway_id      | No         | Gateway                          |
| message_id      | No         | ID de transporte                 |
| payload_raw     | Sí         | Trama original                   |
| payload_hash    | Sí         | Deduplicación                    |
| rssi / snr      | No         | Radio, si existe                 |
| sequence_no     | No         | Contador, si existe              |
| status          | Sí         | RECEIVED/DECODED/ERROR/DUPLICATE |
| decoder_version | No         | Versión del decoder              |

## 8.2 track_telemetry

| **Campo**          | **Propósito**               |
|--------------------|-----------------------------|
| id                 | ID de observación           |
| farm_id            | Finca/tenant                |
| device_key         | Dispositivo                 |
| device_type        | TAG/GPS/otro                |
| observed_at        | Momento del dato            |
| received_at        | Momento de recepción        |
| latitude/longitude | Coordenadas si aplica       |
| temperature_c      | Temperatura si aplica       |
| steps              | Pasos si aplica             |
| battery_pct        | Batería                     |
| gateway_id         | Gateway                     |
| quality_state      | Estado técnico inicial      |
| raw_id             | Referencia al RAW           |
| decoder_version    | Versión que produjo el dato |

## 8.3 track_device

| **Campo**       | **Propósito**                       |
|-----------------|-------------------------------------|
| device_key      | Identificador único                 |
| device_id       | ID interno GANUS                    |
| device_type     | TAG/GPS                             |
| manufacturer    | Fabricante                          |
| model           | Modelo                              |
| protocol        | Protocolo                           |
| decoder_id      | Decoder                             |
| decoder_version | Versión                             |
| farm_id         | Finca                               |
| status          | ACTIVE/INACTIVE/MAINTENANCE/RETIRED |

track_device es catálogo técnico. No reemplaza Inventory.

# 9. ASOCIACIÓN DISPOSITIVO ↔ ANIMAL

> device_key → dispositivo técnico → asociación vigente → Inventory → animal

D00 no debe asumir asociaciones. Si llega un device_key técnicamente válido pero no tiene asociación funcional vigente, D00 normaliza y D03 deberá marcarlo como NO ASOCIADO.

- No crear automáticamente un animal.

- No modificar Inventory.

- No corregir asociaciones.

- No atribuir una coordenada a un animal si la asociación no está validada.

# 10. FECHAS OBLIGATORIAS

| **Campo**     | **Significado**                               |
|---------------|-----------------------------------------------|
| observed_at   | Cuándo el dispositivo generó/observó el dato. |
| received_at   | Cuándo GANUS recibió el mensaje.              |
| decoded_at    | Cuándo terminó la decodificación.             |
| normalized_at | Cuándo se creó el registro estándar.          |

Ejemplo: si el dispositivo genera 06:00 y GANUS recibe 06:07, observed_at sigue siendo 06:00. No sustituirlo por received_at.

# 11. DEDUPLICACIÓN Y TRAZABILIDAD

D00 debe permitir responder: qué llegó, cuándo llegó, de qué dispositivo, por qué gateway, cuál era la trama, qué decoder se usó y qué dato normalizado produjo.

- Usar message_id/sequence_no cuando existan.

- Usar payload_hash como apoyo para deduplicación.

- Un duplicado no debe crear una segunda observación funcional.

- Nunca borrar el RAW para ocultar un error.

# 12. CONTRATO D00 → D03

> {  
> "farm_id":"FINCA-001",  
> "device_key":"70B3D57EDGPS01",  
> "device_type":"GPS",  
> "observed_at":"2026-09-21T06:00:00",  
> "received_at":"2026-09-21T06:07:00",  
> "latitude":10.960614,  
> "longitude":-74.800382,  
> "battery_pct":88,  
> "gateway_id":"GW-001",  
> "sequence_no":1842,  
> "quality_state":"VALID",  
> "raw_id":"RAW-000002",  
> "decoder_version":"HKT-GPS-v1.0"  
> }
>
> {  
> "farm_id":"FINCA-001",  
> "device_key":"70B3D57EDTAG01",  
> "device_type":"TAG",  
> "observed_at":"2026-09-21T06:00:00",  
> "received_at":"2026-09-21T06:06:00",  
> "temperature_c":37.3,  
> "steps":4236,  
> "battery_pct":90,  
> "gateway_id":"GW-001",  
> "quality_state":"VALID",  
> "raw_id":"RAW-000001",  
> "decoder_version":"HKT-TAG-v1.0"  
> }

Campos que no aplican quedan NULL. No inventar valores.

# 13. DECODER Y VERSIONAMIENTO

La dependencia del fabricante debe quedar encapsulada en el adapter/decoder.

> device/model → decoder → decoder_version → campos normalizados

- Un cambio de payload requiere nueva versión del decoder.

- No reinterpretar silenciosamente datos históricos.

- D03 nunca debe conocer el formato propietario del fabricante.

- Agregar otro fabricante no debe cambiar el contrato D00 → D03.

# 14. ERRORES

| **Situación**          | **D00**                                  |
|------------------------|------------------------------------------|
| Payload válido         | RAW + NORMALIZED                         |
| Duplicado              | RAW + DUPLICATE; no duplicar observación |
| Payload corrupto       | RAW + ERROR                              |
| Decoder inexistente    | RAW + UNDECODED/ERROR                    |
| Campo inválido         | RAW + INVALID                            |
| Device desconocido     | Conservar mensaje; UNKNOWN_DEVICE        |
| Campo opcional ausente | NULL si el mensaje sigue siendo válido   |

Un error de D00 no puede convertirse en incumplimiento operativo.

# 15. LO QUE D00 NO DEBE HACER

- Identificar o crear animales.

- Modificar Inventory.

- Determinar potreros o geocercas.

- Evaluar entrada/salida/permanencia.

- Decidir cumplimiento/incumplimiento.

- Generar alertas de negocio.

- Crear órdenes.

- Interpretar comportamiento animal.

- Interpretar falta de comunicación como salida física.

- Contener lógica de negocio específica del fabricante.

# 16. RESPONSABILIDAD POR DOMINIO

| **Dominio** | **Pregunta**                                                       |
|-------------|--------------------------------------------------------------------|
| D00         | ¿Qué llegó y cómo lo traduzco al formato GANUS?                    |
| D03         | ¿El dato es válido, oportuno, completo y a qué activo corresponde? |
| D01         | ¿Dónde está dentro del mapa?                                       |
| D02         | ¿Qué debía ocurrir según la regla?                                 |
| D03         | ¿Lo ocurrido cumple o se desvía?                                   |
| D04         | ¿Qué desviación debe gestionarse?                                  |

# 17. ESCENARIO DE PRUEBA MVP

| **Parámetro**                 | **Valor** |
|-------------------------------|-----------|
| Animales                      | 10        |
| TAG por animal                | 1         |
| GPS por animal                | 1         |
| Dispositivos                  | 20        |
| Transmisiones/día/dispositivo | 4         |
| Días                          | 7         |
| Mensajes esperados            | 560       |
| TAG                           | 280       |
| GPS                           | 280       |

La frecuencia 4/día es escenario de prueba, no una constante de código. Debe existir un perfil de transmisión configurable.

# 18. CASOS DE PRUEBA OBLIGATORIOS

| **ID** | **Caso**                 | **Resultado esperado**              |
|--------|--------------------------|-------------------------------------|
| T01    | TAG válido               | RAW + NORMALIZED                    |
| T02    | GPS válido               | RAW + NORMALIZED con lat/lon        |
| T03    | Duplicado                | No duplicar observación             |
| T04    | Decoder inexistente      | RAW + ERROR                         |
| T05    | Campo inválido           | RAW + INVALID                       |
| T06    | Dato tardío              | Conservar observed_at y received_at |
| T07    | Device desconocido       | No crear animal                     |
| T08    | Campo opcional ausente   | NULL                                |
| T09    | Nueva versión decoder    | Historial intacto                   |
| T10    | Silencio de transmisión  | No inventar dato                    |
| T11    | Recuperación             | Procesar nuevo mensaje normalmente  |
| T12    | Sin asociación Inventory | D00 normaliza; D03 = NO ASOCIADO    |

# 19. CRITERIOS DE ACEPTACIÓN

- Recibir mensajes reales o simulados.

- Conservar RAW sin alteración.

- Relacionar RAW y NORMALIZED.

- Identificar device_key.

- Aplicar decoder correcto.

- Normalizar unidades y nombres.

- Separar observed_at de received_at.

- Controlar duplicados.

- Registrar errores sin perder RAW.

- No crear animales desde D00.

- No modificar Inventory.

- No calcular geocercas.

- No generar alertas de negocio.

- Permitir que D03 consuma el contrato sin conocer HKT, MQTT, Gateway o payload propietario.

- Permitir incorporar otro fabricante sin cambiar el contrato normalizado.

# 20. DEFINITION OF DONE

> TAG/GPS REAL → LoRaWAN → Gateway → Network Server → D00 → RAW → Decoder → NORMALIZED → D03

- RAW consultable.

- Telemetría normalizada consultable.

- Decoder/version identificables.

- device_key trazable.

- raw_id presente en cada observación normalizada.

- Contrato D00 → D03 documentado y probado.

- Pruebas de éxito, duplicado, error, tardanza y device desconocido.

- Sin lógica de negocio de TRACK dentro de D00.

# 21. ENTREGABLES DE DESARROLLO

- Servicio/adaptador de recepción.

- Persistencia RAW.

- Catálogo técnico de dispositivos.

- Framework de decoders versionados.

- Decoder de cada TAG/GPS aprobado para MVP.

- Persistencia de telemetría normalizada.

- Deduplicación.

- Estados de procesamiento y errores.

- Contrato JSON D00 → D03 versionado.

- Publicación interna de telemetría normalizada.

- Pruebas unitarias de decoders.

- Pruebas de integración end-to-end.

- Datos simulados para pruebas sin hardware.

- Documento payload → campos GANUS.

# 22. PENDIENTES CON EL PROVEEDOR DE HARDWARE

- Payload definitivo TAG.

- Payload definitivo GPS.

- Identificador estable.

- Mecanismo de entrega al Network Server.

- QoS/reintentos.

- Timestamp y precisión.

- Formato de batería, actividad, temperatura y GPS.

- Sequence/FCnt.

- Metadata de gateway.

- Buffer y retransmisión después de pérdida de cobertura.

- Firmware/protocolo.

- Decoder oficial.

- Payloads reales de ejemplo.

# 23. REGLA FINAL DE ARQUITECTURA

**D00 NO DEBE ENTENDER LA FINCA. D00 DEBE ENTENDER AL DISPOSITIVO.**

D00 traduce tecnología de campo al lenguaje estándar de GANUS. A partir de esa traducción, D03, D01, D02 y D04 agregan identidad, territorio, calidad, reglas, eventos y gestión.

**Principio rector:** “El fabricante puede cambiar; el contrato normalizado de GANUS debe permanecer estable.”

# ANEXO A. MODELO DE BASE DE DATOS E INTERACCIÓN CON LA DATA

Este anexo hace explícita la arquitectura de datos que debe implementar desarrollo. La regla es separar origen, normalización, identidad del animal, territorio, evaluación y gestión.

## A.1. Modelo conceptual

La cadena de información es:

**DISPOSITIVO → LoRaWAN → GATEWAY → NETWORK SERVER → D00 → BASE DE DATOS → D03 → D01/D02 → D04**

D00 solamente convierte la información del dispositivo al estándar GANUS. D03 utiliza ese dato para darle contexto operacional.

## A.2. Tablas y responsabilidad

| Tabla                   | Qué representa                  | Responsable           | Regla                                       |
|-------------------------|---------------------------------|-----------------------|---------------------------------------------|
| track_device            | Registro técnico del TAG/GPS    | D00/TRACK             | No representa al animal.                    |
| track_telemetry_raw     | Trama exactamente recibida      | D00                   | Nunca se reemplaza por el dato normalizado. |
| track_telemetry         | Dato estándar GANUS             | D00 → D03             | Es el contrato que consume TRACK.           |
| inventory_animal        | Animal/activo maestro           | OPERATIVO / INVENTORY | Es la fuente de identidad.                  |
| inventory_animal_device | Asociación animal ↔ dispositivo | INVENTORY / D03       | Debe manejar vigencia histórica.            |

## A.3. Relaciones

track_device.device_key → track_telemetry_raw.device_key

track_telemetry_raw.id → track_telemetry.raw_id

track_telemetry.device_key → inventory_animal_device.device_key

inventory_animal_device.animal_id → inventory_animal.id

La relación del dispositivo con el animal NO se graba como una suposición dentro de D00. Se resuelve mediante la asociación vigente en Inventory.

## A.4. Ejemplo completo de interacción

| Etapa                | Qué ocurre                                                                                                       |
|----------------------|------------------------------------------------------------------------------------------------------------------|
| 1\. CAMPO            | TAG-001 transmite temperatura=37.3 °C, pasos=4236 y batería=90 %. GPS-001 transmite latitud, longitud y batería. |
| 2\. D00 RAW          | D00 guarda cada payload original en track_telemetry_raw.                                                         |
| 3\. D00 NORMALIZA    | El decoder transforma los payloads a track_telemetry.                                                            |
| 4\. D03 IDENTIFICA   | D03 toma device_key y consulta inventory_animal_device.                                                          |
| 5\. INVENTORY        | La asociación indica TAG-001/GPS-001 → Animal A001.                                                              |
| 6\. D01 UBICA        | La coordenada GPS se cruza con el mapa digital y se determina el espacio territorial.                            |
| 7\. D02 APORTA REGLA | D02 indica qué debía ocurrir: por ejemplo, A001 debía permanecer en P17.                                         |
| 8\. D03 EVALÚA       | D03 compara observado vs esperado y determina CUMPLE, INCUMPLE, NO EVALUABLE o SIN REGLA.                        |
| 9\. D04 GESTIONA     | Si existe una desviación gestionable, D04 crea/gestiona la alerta.                                               |

## A.5. Ejemplo visual de relaciones

TAG-001  
↓  
track_device  
↓  
track_telemetry_raw  
↓ raw_id  
track_telemetry  
↓ device_key  
inventory_animal_device  
↓ animal_id  
Animal A001  
↓  
D01 = ubicación  
D02 = regla  
D03 = evaluación  
D04 = gestión

## A.6. Regla crítica: D00 no conoce al animal

Si llega TAG-001 y el dispositivo está registrado técnicamente pero no existe una asociación vigente con Inventory, D00 DEBE normalizar el dato. D03 posteriormente lo marcará como NO ASOCIADO. D00 no debe crear un animal ni inventar la relación.

## A.7. Regla crítica: D00 no conoce el territorio

Si llega una coordenada 10.960614 / -74.800382, D00 solamente guarda la coordenada. No debe escribir P17, sector, corral, geocerca, entrada, salida ni permanencia. La contextualización geográfica corresponde a D01/D03.

## A.8. Regla crítica: RAW y NORMALIZED deben coexistir

RAW permite reconstruir exactamente qué recibió GANUS. NORMALIZED permite que el resto de la plataforma trabaje con un formato estable. Ambos son necesarios y deben estar vinculados mediante raw_id.

## A.9. Regla crítica: el fabricante no puede contaminar el modelo GANUS

HKT, otro fabricante, LoRaWAN, MQTT, Gateway y el formato propietario del payload son detalles de integración. D03 y los dominios superiores no deben depender de ellos.

**Principio: EL FABRICANTE PUEDE CAMBIAR; EL CONTRATO NORMALIZADO DE GANUS DEBE PERMANECER ESTABLE.**

## A.10. Qué pregunta responde cada capa

| Capa                | Pregunta                                      |
|---------------------|-----------------------------------------------|
| track_telemetry_raw | ¿Qué recibimos exactamente?                   |
| track_telemetry     | ¿Qué significa ese dato en el estándar GANUS? |
| Inventory           | ¿A qué animal/activo pertenece?               |
| D01                 | ¿Dónde está?                                  |
| D02                 | ¿Qué debería ocurrir?                         |
| D03                 | ¿Qué ocurrió y es evaluable?                  |
| D04                 | ¿Qué debe gestionarse?                        |

Este anexo complementa la especificación principal y debe utilizarse como referencia de arquitectura para la implementación del modelo de datos y sus integraciones.
