# GANUS — Contexto de Formularios dinámicos y Planificación

Este archivo se carga en cada conversación. Define para qué sirve el repositorio, quién lo usa, qué se puede
modificar y cómo se mantiene la memoria del trabajo.

## 1. Para qué sirve este repositorio

1. **Consultar dudas** sobre GANUS: cualquier módulo, regla, decisión o documento.
2. **Preparar prompts** para desarrollar GANUS en el repositorio de código (que es otro repositorio).
3. **Registrar el estado** del trabajo del usuario en sus módulos.

No contiene código de la aplicación. Todo está en Markdown.

## 2. Rol del usuario

El usuario es el **responsable de Formularios dinámicos y Planificación** de GANUS Operativo. Los dos módulos van de
la mano: Formularios define *qué* se captura y Planificación define *sobre quién, quién y cuándo*, lo entrega al móvil
por STAGING y controla el resultado. El equipo móvil implementa la representación en pantalla a partir de la
estructura que diseña el usuario (contrato del formulario y paquete de trabajo).

| Módulo | Responsabilidades del usuario |
| --- | --- |
| Formularios | Constructor de plantillas, campos, tipos, orden, obligatoriedad, condiciones, validaciones, catálogos, claves de búsqueda y de contexto, filtros disponibles, vista previa, renderer único (web y móvil), versionado y publicación |
| Planificación | Calendario, tareas unitarias y masivas, cálculo y congelamiento del dataset, asignación, programación y recurrencia, validación previa y "Confirmar y publicar", estados de la tarea y del resultado por activo, paquete de trabajo hacia STAGING, recepción de resultados, Centro de Control, alertas de campo y su conversión en tarea |

Dependencias: Organización (procesos, actividades, espacios, usuarios), Inventario (atributos aptos para búsqueda y
consulta de población), STAGING (sobre de mensajes, Inbox/Outbox), Mobile (contrato del paquete y de resultados) y
Producto (decisiones pendientes).

## 3. Alcance: qué se puede modificar

| Se puede modificar | Solo se consulta, nunca se modifica |
| --- | --- |
| `modelo-datos/formularios.md` y `modelo-datos/planificacion.md` | `fuentes/` (documentos oficiales del proyecto) |
| `modelo-datos/README.md` (convenciones y observaciones a otros módulos) | Todo lo que pertenece a otros módulos: Organización, Inventario, Ciclo de vida, Mobile, STAGING, Seguimiento, Knowledge, Advisory, Track, Potreros, ODOO |
| `contexto/` (estado, bitácora y decisiones pendientes) | |
| Skills de Formularios y Planificación: 6 a 11, 13 y 14 | |

Reglas de alcance:
- Se puede **preguntar sobre cualquier módulo** y se responde con fuentes. Pero no se modifican sus documentos ni su
  modelo, ni se proponen cambios en su código.
- Si algo de otro módulo afecta a Formularios o Planificación, se registra como observación en
  `modelo-datos/README.md` o como decisión pendiente, dirigida a su responsable.
- `fuentes/` solo cambia si el usuario lo pide explícitamente (por ejemplo, cuando llegue una versión nueva del 04 o del 05).
- Las skills 1 a 5 y 12 son transversales (reglas y STAGING) y afectan a todo el equipo: cambiarlas solo si el usuario
  lo pide explícitamente, y avisarle de ese impacto.
- Los prompts que se preparen para el repositorio de código deben limitar los cambios a los módulos de Formularios y
  Planificación. Los demás dominios se usan solo por contrato (API, DTO o eventos).

## 4. Memoria

- **No hay memoria entre conversaciones.** No se mencionan, suponen ni citan conversaciones anteriores.
- La única memoria persistente es **este repositorio**. Al empezar una conversación, leer `contexto/estado-actual.md`
  (rama, Jira y trabajo en curso). Si hace falta historia, leer `contexto/bitacora.md`.
- Dentro de una conversación vale lo dicho en ella.
- Si un dato no está en el repositorio ni en la conversación actual, se dice y se pregunta. No se completa de memoria.

## 5. Cuándo y cómo se actualiza

El repositorio **solo se actualiza cuando el usuario lo indica**: "ya se hizo X", "estoy en la rama Y", "el ticket
ahora es GAN-123", "se decidió la DP-05", "actualiza el modelo de datos". Nunca por iniciativa propia ni deduciendo
avances. Responder una consulta o preparar un prompt no actualiza nada.

Cuando el usuario lo indica:
1. Actualizar `contexto/estado-actual.md` (rama, ticket, en curso, hecho, próximos pasos).
2. Agregar una entrada al inicio de `contexto/bitacora.md`: fecha, qué cambió, archivos y ticket o rama si aplica.
3. Si se cerró o abrió una decisión, actualizar `contexto/decisiones-pendientes.md` y las skills que la citan.
4. Hacer commit en este repositorio con un mensaje claro (con la clave de Jira si existe). Push solo si el usuario lo pide.

## 6. Reglas del proyecto (Constitución resumida, CONST §22)

1. Los documentos de `fuentes/` (00 a 11) son la fuente de verdad del MVP.
2. No inventar requisitos funcionales.
3. Respetar ownership y fronteras de dominio.
4. No acceder directamente a tablas o repositorios de otro dominio.
5. No duplicar maestros ni la identidad de los activos.
6. Organización = contexto; Inventario = identidad y ciclo de vida; Formularios = definición; Planificación =
   dataset y trabajo; Mobile = ejecución; Knowledge = comprensión; Advisory = interpretación y propuesta.
7. STAGING integra; no contiene lógica de negocio.
8. Mobile no segmenta ni modifica población, formulario ni tarea.
9. Advisory no escribe directamente en dominios transaccionales.
10. Usar APIs, DTOs y eventos; nunca joins entre dominios como integración.
11. Usar idempotencia, trazabilidad, Inbox/Outbox y reintentos donde corresponda.
12. Si falta una decisión técnica: proponer y documentar (ADR). Si falta una decisión funcional: no inventar, registrarla
    en `contexto/decisiones-pendientes.md`.
13. Todo cambio debe tener pruebas.
14. La solución correcta es la mínima que respeta producto, arquitectura y trazabilidad.

Conflicto conocido: para Formularios y Planificación, los documentos 04 v1.3 y 05 v1.0 contradicen en varios puntos a
la v3.1, al 00 y al Master Context (DP-01). No elegir en silencio: señalar el conflicto.

## 7. Cómo responder

- Citar la fuente: documento y sección (por ejemplo "04 §24"). Las abreviaturas están en `README.md`.
- Distinguir siempre: **definido**, **pendiente** (con su `DP-xx`) o **en conflicto** entre documentos.
- Si la pregunta es de otro módulo, responder y aclarar que es de otro responsable.
- Responder en español, claro y directo.

## 8. Plantilla para prompts de desarrollo

Cuando el usuario pida un prompt para el repositorio de código, usar esta estructura con los datos de
`contexto/estado-actual.md`:

```
Contexto: GANUS Operativo, módulo <Formularios | Planificación>.
Rama: <rama actual>. Ticket Jira: <clave> — <título>.

Objetivo: <qué debe quedar funcionando, en una o dos frases>.

Fuentes: <documento §sección>; modelo de datos: <tablas de modelo-datos/...>.
Skill a aplicar: <nombre de la skill>.

Alcance permitido: solo el módulo <forms | planning> y sus migraciones.
Otros dominios únicamente por contrato (API, DTO o eventos). No modificar código de otros módulos.

Decisiones pendientes que aplican: <DP-xx: qué NO implementar hasta que se decida>.

Criterios de aceptación:
- <criterio verificable>
Pruebas obligatorias:
- <prueba>

Forma de trabajo: primero propone un plan sin editar (plan mode). Después implementa, ejecuta las pruebas,
resume los archivos cambiados y completa el checklist de PR (CONST §21).
```

## 9. Mapa del repositorio

| Ruta | Contenido |
| --- | --- |
| `CLAUDE.md` | Este archivo |
| `README.md` | Guía para personas: uso, estructura, abreviaturas e índice de skills |
| `contexto/estado-actual.md` | Rama, Jira, trabajo en curso y entregables |
| `contexto/bitacora.md` | Registro de cambios hechos con este repositorio |
| `contexto/decisiones-pendientes.md` | Decisiones pendientes DP-01 a DP-27 |
| `modelo-datos/` | Modelo de datos de `forms` y `planning` (diagramas Mermaid) y convenciones |
| `fuentes/` | Documentos oficiales del proyecto en Markdown (solo lectura) |
| `.claude/skills/` | Las 14 skills del proyecto |
