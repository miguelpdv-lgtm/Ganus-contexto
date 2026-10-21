---
name: guardrail-ia-determinista
description: Limita el uso de modelos de lenguaje en GANUS a interpretar lenguaje natural, estructurar propuestas y explicar, dejando cálculos, indicadores, validaciones y escrituras a código determinista con confirmación humana. Usar al implementar captura por voz, Advisory, interpretación de reglas de Knowledge o cualquier función que llame a un LLM.
---

# Guardrail de IA determinista

## Propósito
La IA interpreta; no es fuente maestra ni ejecuta cambios por sí sola. Todo lo que un LLM produce es una
**propuesta** que el código valida y que una persona confirma cuando cambia datos.

## Fuentes
- CONST §3, §7.5 (voz), §7.6 (Knowledge y Advisory), §8 (regla crítica de inteligencia), §19.
- MC §16, §21 (reglas 19 y 20).
- 06 §10–§11 (voz y calidad de reconocimiento).
- 07 (Knowledge: "GANUS muestra cómo la entendió antes de activarla", AC-03).
- 09C §12.4 (recomendación → ActionProposal → Planificación), §3 (`actorType: AI`).

## Dónde hay IA en el MVP

| Uso | Dominio | La IA hace | El código determinista hace |
| --- | --- | --- | --- |
| Captura por voz | Mobile | Reconocer e interpretar lo dicho según el campo | Normalizar, validar con las reglas del formulario, pedir confirmación |
| Advisory | Advisory | Explicar datos y proponer acciones | Calcular indicadores, crear la tarea si la persona acepta |
| Reglas en lenguaje natural | Knowledge | Traducir lo que dice el ganadero a una regla estructurada | Evaluar la regla, mostrar cómo se entendió y activarla solo con aprobación |

## La IA puede
- Interpretar lenguaje natural y devolver una estructura (JSON) validada contra un esquema.
- Proponer valores de campos a partir de la voz.
- Explicar resultados, alertas e indicadores ya calculados, citando de dónde salen.
- Proponer una acción (ActionProposal).

## La IA no puede
- Calcular indicadores oficiales, totales, promedios o cumplimientos: eso es código determinista (CONST §7.6).
- Decidir algo que ya tiene una regla estructurada (CONST §19).
- Escribir en la base de datos ni generar SQL contra producción (CONST §19, 10 §33).
- Crear o modificar una tarea o un activo directamente (09C §18, CI-011).
- Publicar reglas o conocimiento sin validación humana (MC §16).
- Inventar datos para llenar un vacío.
- Guardar en silencio un valor ambiguo (CONST §7.5).

## Patrón obligatorio
```
Entrada del usuario (voz / texto)
  → LLM interpreta
  → salida validada contra un esquema (si no cumple, se rechaza)
  → validación determinista (tipos, rangos, catálogos, reglas del formulario)
  → ¿confiable? → mostrar a la persona → confirmar / repetir / corregir
  → caso de uso determinista (el único que escribe)
  → evento con actorType = "AI" cuando la propuesta vino de la IA, y el actor humano que confirmó
```

## Voz en Mobile (06 §10–§11)
`VOZ → RECONOCIMIENTO → INTERPRETACIÓN CONTEXTUAL → NORMALIZACIÓN → VALIDACIÓN → CONFIRMACIÓN → VALOR ESTRUCTURADO`
- La interpretación usa el contexto del formulario y del campo: tipo, catálogo y, si existe, la unidad (DP-12).
- Valor válido y claro: se propone para confirmación.
- Valor que no cumple una validación: se pide repetir o corregir.
- Interpretación ambigua: se muestra lo entendido y se permite repetir.
- La captura convencional siempre está disponible.
- Se conserva trazabilidad para diagnosticar fallas de reconocimiento.

## Advisory → Planificación (09C §12.4)
1. Knowledge produce la situación y la recomendación.
2. Advisory la explica.
3. Si la persona acepta, se crea un ActionProposal.
4. Planificación recibe el comando y crea la tarea con sus propias reglas y estados, en Borrador.
5. Advisory nunca escribe en `planning`.

## Verificación
- Una salida del LLM que no cumple el esquema se rechaza y no llega al caso de uso.
- Un valor ambiguo pide confirmación; no se guarda.
- No existe ruta de código donde la respuesta del LLM ejecute SQL o escriba en un repositorio.
- Las recomendaciones aceptadas crean ActionProposal, no tareas directas.
- Los eventos originados por IA llevan `actorType: AI` y el actor humano que confirmó.
