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

## 2026-10-23 — Reorganización del repositorio
- Qué: el repositorio pasa a ser solo Markdown y se dedica a consultas y prompts para Formularios y Planificación.
  Se agregan `CLAUDE.md`, `README.md` y la carpeta `contexto/`. Los documentos oficiales se convierten a Markdown en
  `fuentes/`. Las imágenes, los Excel y la presentación se transcriben en `fuentes/diagramas-referencia.md` y
  `fuentes/track-anexos.md`. El modelo de datos pasa a `modelo-datos/`.
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
