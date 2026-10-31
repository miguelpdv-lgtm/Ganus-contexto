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
