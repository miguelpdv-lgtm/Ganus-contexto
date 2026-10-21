---
name: verificar-ownership-dominio
description: Verifica que un cambio de GANUS respete el dueño de cada dato, escriba solo en el esquema de su dominio y se comunique con otros dominios por API, DTO o eventos, sin joins ni repositorios cruzados. Usar al crear tablas, migraciones, módulos NestJS, consultas o integraciones, y al revisar un PR que toque más de un dominio.
---

# Verificar ownership de dominio

## Propósito
GANUS usa una base PostgreSQL común, pero cada dominio es dueño de sus datos y de su lógica. Esta skill evita que un
módulo lea o escriba las tablas de otro, duplique maestros o cree una segunda identidad del activo.

## Fuentes
- CONST §3, §6 (mapa de ownership), §9 (contratos), §10 (base de datos).
- ARQ §7 (fronteras), §8 (esquemas), ADR-006.
- 10 §4 (propiedad por dominio), §25 (relaciones críticas), §33 (lo que no se hace en la BD).
- 09C §1, §5 (matriz de contratos), §18 (quién puede hacer qué).

## Cuándo usarla
- Al diseñar tablas o migraciones.
- Al crear o modificar un módulo, un repositorio o una consulta.
- Al integrar dos dominios.
- Al revisar un PR que toca más de un dominio.

## Mapa de ownership (CONST §6, 10 §4)

| Dominio | Es dueño de | No es dueño de |
| --- | --- | --- |
| Organización | Grupo, finca, usuarios, roles, permisos, espacios, procesos, tipos de actividad, clasificación Industria → Sector → Negocio | Activos, tareas, resultados, reglas, indicadores |
| Inventario | Activo (ActivoID), identificadores, atributos, relaciones, ubicación, documentos, historial, ciclo de vida | Planificación, captura, reglas |
| Formularios | form, form_version, secciones, campos, claves, validaciones, catálogos propios, publicación | Dataset, tarea, ejecución, resultado |
| Planificación | Tarea, dataset (task_target), asignación, programación, historial de estados, resultado por activo, excepciones | Identidad del activo, definición del formulario |
| Mobile | Ejecución, captura, evidencia, alertas y sincronización local | Población, definición de tarea o formulario, maestros |
| Seguimiento/Control | Modelos de lectura y control operacional | Fuente maestra de tareas o activos |
| Knowledge | Reglas, indicadores gobernados, situaciones, recomendaciones | Escritura transaccional |
| Advisory | Interpretación y propuestas de acción | Escritura en Task o Asset |
| STAGING | Inbox, Outbox, intentos, checkpoints, DLQ, paquetes | Datos maestros y reglas de negocio |
| Transversal | Sesión, auditoría, archivos, notificaciones, importación/exportación | Reglas de negocio de los dominios |

## Esquemas PostgreSQL (ARQ §8)
`organization`, `inventory`, `forms`, `planning`, `staging`, `audit`, `reporting`.
`reporting` solo contiene modelos de lectura derivados; nunca es fuente maestra.
Las entidades de Mobile (servidor), Control, Knowledge y Advisory no tienen esquema asignado: **DP-22** (ADR).

## Reglas
1. Un dominio escribe **solo** en su esquema (CONST §10).
2. Las referencias a otro dominio se guardan como ID estable (UUID), nunca por nombre visible (10 §3).
3. **Claves foráneas entre esquemas:** la regla por defecto es no crearlas. Solo se crean si el modelo de referencia
   lo decide explícitamente o con un ADR que las justifique (CONST §10, ARQ §8: "dependencias físicas mínimas y
   justificadas"). Las referencias de contexto (`farm_id`) se validan en la capa de aplicación.
4. **No hay joins entre dominios como mecanismo de integración.** Si un dominio necesita datos de otro: API del
   dueño, evento consumido o proyección de lectura en `reporting` (CONST §10, 09C §1).
5. Un consumidor que recibe una referencia no gana permiso de escritura (09C §10.2).
6. **ActivoID es único.** Ningún dominio crea una identidad paralela del activo ni tablas "vaca", "bovino" o "animal"
   (MC §21, 10 §7, §33).
7. No duplicar catálogos de negocio en una tabla compartida sin dueño (10 §23).
8. Lo que cruza a Mobile o a sistemas externos pasa por STAGING (09C §8).

## Reglas en el código (NestJS)
- Cada dominio es un módulo NestJS. Exporta solo servicios de aplicación o fachadas con DTOs; nunca repositorios,
  entidades de persistencia ni el cliente del ORM.
- Un módulo no importa archivos internos de otro módulo (`domain/`, `infrastructure/`). Solo su API pública.
- Las entidades de persistencia de un dominio no aparecen en otro dominio.
- Recomendado (decisión técnica): una regla automática de dependencias (por ejemplo `eslint-plugin-boundaries` o
  `dependency-cruiser`) que haga fallar el CI ante una importación cruzada.

## Casos frecuentes en Formularios y Planificación

| Necesidad | Correcto | Incorrecto |
| --- | --- | --- |
| Formularios necesita procesos y espacios | Contratos ProcessReference, ActivityTypeReference y SpaceReference de Organización (09C §5) | Consultar `organization.process` |
| Formularios necesita las claves de búsqueda | Lista de atributos aptos publicada por Inventario | Leer las columnas de `inventory.asset_attribute_value` |
| Planificación calcula el dataset | API o contrato de consulta de Inventario (DP-03) | `JOIN inventory.asset` desde `planning` |
| Planificación usa el formulario | FormVersionReference.v1 y la definición publicada | Copiar y editar el formulario en `planning` |
| Un resultado actualiza el peso del activo | Comando o contrato de Inventario, solo con mapeo autorizado (DP-08) | `UPDATE inventory.asset_attribute_value` desde Planificación |
| Mobile envía resultados | TaskExecutionResult.v1 por STAGING | Escribir directo en `planning.task_asset_result` |

## Procedimiento de revisión
1. Listar las tablas que el cambio lee y escribe, con su esquema.
2. Confirmar que cada escritura cae en el esquema del dominio que cambia.
3. Buscar importaciones cruzadas entre módulos.
4. Buscar SQL o consultas del ORM que mencionen otro esquema.
5. Confirmar que cada dato externo llega por API, evento o proyección.
6. Si se agregó una FK entre esquemas, exigir el ADR.

## Prohibido
- `JOIN` entre esquemas de dominios distintos para resolver lógica de negocio.
- Escribir en tablas de otro dominio, aunque esté en la misma base.
- Exportar repositorios o entidades ORM entre módulos.
- Copias editables de maestros de otro dominio.
- Lógica de negocio en STAGING, n8n, controladores o frontend (CONST §3).

## Verificación
- Prueba de arquitectura: ningún módulo importa internos de otro.
- Revisión de migraciones: cada tabla nueva está en el esquema de su dueño.
- CI-001 (09C §19): ningún dominio consulta tablas internas de otro.
