# Bitácora

Registro de los cambios hechos con este repositorio, del más reciente al más antiguo. Solo se agrega una entrada
cuando el usuario indica que algo se hizo o cambió (ver `CLAUDE.md` §5).

Formato de cada entrada:

```
## AAAA-MM-DD — Título corto
- Qué: <qué cambió>
- Archivos: <rutas>
- Rama / Jira: <si aplica>
```

---

## 2026-11-10 — Auditoría del modelo v1: correcciones y reducciones (v1.1)
- Qué: por indicación del usuario. Se audita el modelo v1 de `forms` y `planning` contra las fuentes. Se mantiene el
  diseño (no se divide ninguna tabla) y se aplican 8 correcciones y 4 de las 5 reducciones propuestas:
  1. El criterio de contexto de `tarea.criterios` usa el nivel de espacio como dimensión y el espacio como valor.
  2. `tarea.espacio_id` es del nivel más bajo que declare el formulario.
  3. `campo_formulario.catalogo_maestro_ref` (`dominio:catalogo`) reemplaza a `catalogo_maestro_id`.
  4. `version_formulario.obsoleto_por`.
  5. `captura.aplicacion` solo `APPLIED` y `CONFLICT`.
  6. Convención de identificadores: uuid en entidades principales, PK compuesta en las dependientes.
  7. Skill 6 alineada (código de la opción y contrato sin catálogos aparte).
  8. Observaciones a STAGING y Mobile ampliadas (inicio por activo, versión del paquete, ubicación y espacio de la
     alerta) y DP-27 con el 06 §7 y §15.
  Reducciones: se quitan `activo_tarea.incluido`, `opcion_campo.id`, `tarea.recurrencia`, `tarea.tarea_serie_id` y
  `alerta_campo.observacion`. No se aplica pasar los valores a un jsonb dentro de `captura`: `valor_captura` sigue.
  Resultado: `forms` 6 tablas y 39 columnas; `planning` 6 tablas y 68 columnas. Los dos modelos agrupan las columnas
  por bloques. DP-04 anota que no hay columnas de recurrencia.
- Archivos: `modelo-datos/formularios.md`, `modelo-datos/planificacion.md`, `modelo-datos/README.md`,
  `contexto/decisiones-pendientes.md`, `contexto/estado-actual.md`, `docs/index.html`, `README.md`, skills
  `disenar-plantilla-formulario`, `versionar-formulario`, `calcular-dataset-poblacion` y
  `procesar-sincronizacion-resultados`.
- Rama / Jira: `claude/funny-brahmagupta-8pwmhv`.

## 2026-11-09 — Mockup de 6 tablas publicado en GitHub Pages
- Qué: por indicación del usuario, la rama que publica GitHub Pages se adelanta hasta la rama de trabajo, así que
  https://miguelpdv-lgtm.github.io/Ganus-contexto/ muestra el mockup «Pesaje» con las 6 tablas del modelo v1.
- Archivos: `contexto/estado-actual.md`, `contexto/bitacora.md`.
- Rama / Jira: `claude/funny-brahmagupta-8pwmhv` y `claude/intelligent-ramanujan-1skub1`.

## 2026-11-09 — Modelo de datos v1 de `forms` y `planning`
- Qué: por indicación del usuario. Se listan los requisitos del esquema `planning` (05, 10, 09C), se propone un
  modelo mínimo y se audita con un ejercicio: una tarea masiva de pesaje con el formulario «Pesaje». El usuario
  acuerda 7 decisiones y los ajustes que resultan:
  1. Un formulario por tipo de activo (`version_formulario.tipo_activo_id`). DP-10 parcial.
  2. Sin tabla de filtros disponibles: marca `es_filtro` en las claves; los criterios de la tarea guardan la
     dimensión maestra.
  3. Proceso y actividad no son claves de contexto (DP-02 acordada, por confirmar con Producto).
  4. El contexto se fija en la tarea, no en cada captura.
  5. El espacio de la tarea es dónde se trabaja; la ubicación de la población es un criterio.
  6. Sin condiciones de la tarea hasta definirlas: se abre DP-27.
  7. Cada causa del catálogo indica si exige observación y evidencia (DP-20).
  Resultado: `forms` pasa a 6 tablas (`clave_formulario` reemplaza claves de búsqueda, de contexto y filtros; código
  estable en campos y opciones) y `planning` de 17 a 6 tablas (`tarea`, `activo_tarea`, `captura`,
  `valor_captura`, `alerta_campo`, `historial`). Se actualizan las skills 6 a 11, 13 y 14, el mockup «Pesaje»
  (6 tablas) y se registran dos observaciones a STAGING y Mobile sobre los contratos 09C §10.3 y §10.5.
- Archivos: `modelo-datos/formularios.md`, `modelo-datos/planificacion.md`, `modelo-datos/README.md`,
  `contexto/decisiones-pendientes.md`, `contexto/estado-actual.md`, `docs/index.html`, `README.md`, `CLAUDE.md`
  (mapa), skills `disenar-plantilla-formulario`, `versionar-formulario`, `calcular-dataset-poblacion`,
  `validar-publicacion-tarea`, `gestionar-estados-tarea`, `procesar-alerta-campo`, `construir-paquete-trabajo` y
  `procesar-sincronizacion-resultados`.
- Rama / Jira: `claude/funny-brahmagupta-8pwmhv`.

## 2026-10-31 — El mockup «Pesaje» se abre desde GitHub Pages
- Qué: por indicación del usuario. GitHub Pages publica la raíz de esta rama y mostraba el `README.md` en lugar del
  mockup, que vive en `docs/`. Se agrega `index.html` en la raíz, que redirige a `docs/`. El mockup sigue en un solo
  archivo y `vercel.json` no cambia.
- Archivos: `index.html`, `contexto/estado-actual.md`.
- Rama / Jira: `claude/intelligent-ramanujan-1skub1` (rama publicada en https://miguelpdv-lgtm.github.io/Ganus-contexto/).

## 2026-10-30 — Mockup de presentación del formulario «Pesaje»
- Qué: por indicación del usuario se agrega una página HTML de presentación que muestra, paso a paso, la creación y
  publicación del formulario «Pesaje» y los registros que se guardan en cada una de las 8 tablas del esquema `forms`
  (propuesta de modelo discutida en la conversación; `modelo-datos/formularios.md` no se modifica). Se publica en
  `docs/` para desplegarla en Vercel (el repositorio es privado; `vercel.json` publica solo `docs/`). Es el único
  contenido no Markdown del repositorio.
- Archivos: `docs/index.html`, `vercel.json`, `contexto/estado-actual.md`.
- Rama / Jira: `claude/intelligent-ramanujan-1skub1`.

## 2026-10-27 — Información recibida sobre el alcance de Formularios
- Qué: el usuario recibe y comparte cuatro definiciones. (1) La raza y otras características del activo se capturan
  con formularios dinámicos de levantamiento; Formularios no define la plantilla del activo. (2) Organización
  establece los procesos y Formularios los trae como contexto. (3) Organización debe agregar la relación
  finca–proceso. (4) Lo que no se pueda estandarizar se maneja con formularios dinámicos, también para definir
  estructuras. Se abre DP-26 porque (4) choca con el 04 §2. Se anotan (1) en DP-08 y (2) en DP-02, y (3) como
  observación a Organización. Se registran los documentos de alcance funcional de Formularios y Planificación, que
  toman el 04 v1.3 y el 05 v1.0 como base (DP-01 sigue abierta en este registro).
- Archivos: `contexto/decisiones-pendientes.md`, `modelo-datos/README.md`, `contexto/estado-actual.md`,
  `.claude/skills/disenar-plantilla-formulario/SKILL.md`.

## 2026-10-23 — Reorganización del repositorio
- Qué: el repositorio pasa a ser solo Markdown y se dedica a consultas y prompts para Formularios y Planificación.
  Se agregan `CLAUDE.md`, `README.md` y la carpeta `contexto/`. Los documentos oficiales se convierten a Markdown en
  `fuentes/`. Las imágenes, los Excel y la presentación se transcriben en `fuentes/diagramas-referencia.md` y
  `fuentes/track-anexos.md`. El modelo de datos pasa a `modelo-datos/`. Por indicación del usuario se eliminan los
  32 archivos originales que no eran Markdown (Word, Excel, PowerPoint, PNG, PDF, HTML y SVG); siguen disponibles en
  la rama `main` y en el historial de git. `Skills_Ganus.pdf` queda reemplazado por `.claude/skills/` y Organización
  v1.1 por la v1.2.
- Archivos: `CLAUDE.md`, `README.md`, `contexto/`, `fuentes/`, `modelo-datos/`.

## 2026-10-23 — Modelo de datos de `forms` y `planning` (borrador v0)
- Qué: diagramas entidad-relación de los dos esquemas, reglas que protege cada uno, cambios respecto al borrador
  recibido del equipo y observaciones a Inventario y Ciclo de vida. Se agregan DP-24 y DP-25.
- Archivos: `modelo-datos/formularios.md`, `modelo-datos/planificacion.md`, `modelo-datos/README.md`,
  `contexto/decisiones-pendientes.md`.

## 2026-10-21 — Skills del proyecto y registro de decisiones
- Qué: redacción de las 14 skills (reglas, Formularios, Planificación y STAGING) y del registro de decisiones
  pendientes DP-01 a DP-23.
- Archivos: `.claude/skills/`, `contexto/decisiones-pendientes.md`.
