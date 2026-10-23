# 07 · Seguimiento, Centro de Control, Knowledge y Advisory v1.0

> **Fuente de solo lectura.** Convertido a Markdown desde `07_FUNCIONAL_SEGUIMIENTO_CENTRO_CONTROL_KNOWLEDGE_ADVISORY_GANUS_v1.0.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**GANUS**

**07 — SEGUIMIENTO · CENTRO DE CONTROL · KNOWLEDGE · ADVISORY**

**ESPECIFICACIÓN FUNCIONAL Y DE IMPLEMENTACIÓN — MVP GANUS OPERATIVO**

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>PROPÓSITO<br />
</strong>Documento de recepción para desarrollo. Define qué construir, qué no construir, responsabilidades, modelo de conocimiento, indicadores, situaciones, alertas, seguimiento, Centro de Control, Advisory, IA, documentos, referencias externas, trazabilidad, integración y criterios de aceptación.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

Versión 1.0 · 08 octubre 2026

*Principio rector: GANUS no le pide al ganadero que construya inteligencia. GANUS construye inteligencia a partir de lo que el ganadero sabe, lo que la finca hace y lo que el contexto muestra.*

# 0. CONTROL DEL DOCUMENTO

| **Elemento**       | **Definición**                                                                                                           |
|--------------------|--------------------------------------------------------------------------------------------------------------------------|
| Documento          | 07 — Seguimiento, Centro de Control, Knowledge & Advisory GANUS                                                          |
| Producto           | GANUS OPERATIVO                                                                                                          |
| Versión            | 1.0                                                                                                                      |
| Estado             | Especificación base para desarrollo                                                                                      |
| Dependencias       | 00 Cerebro; 01 Arquitectura; 02 Organización; 03 Inventario + Ciclo de Vida; 04 Formularios; 05 Planificación; 06 Mobile |
| Arquitectura       | Modular Monolith + Hexagonal/Clean + API-first + Event-driven integration                                                |
| Backend / Frontend | NestJS + TypeScript / Angular modular                                                                                    |
| Persistencia       | PostgreSQL con separación lógica por dominio                                                                             |
| Fuente de verdad   | Cada dominio conserva sus datos maestros. Knowledge interpreta; no reemplaza Inventario, Planificación, Forms ni Mobile. |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>DECISIÓN DE PRODUCTO<br />
</strong>El MVP NO expone constructores complejos de conocimiento, KPI, alertas, dashboards ni automatizaciones. La complejidad existe internamente. La experiencia visible se reduce a: Contexto → Mis reglas → Indicadores → Situaciones → Advisory → Acción.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 1. VISIÓN Y PROPÓSITO

Knowledge es el Motor de Comprensión Empresarial de GANUS. Su misión es transformar datos operativos, conocimiento del ganadero, conocimiento base de GANUS, documentos y referencias externas en conocimiento empresarial explicable.

> CONTEXTO  
> ↓  
> DATOS + CONOCIMIENTO + REGLAS + REFERENCIAS  
> ↓  
> MEDICIÓN → COMPARACIÓN → INTERPRETACIÓN  
> ↓  
> SITUACIÓN → HALLAZGO/RIESGO/OPORTUNIDAD  
> ↓  
> RECOMENDACIÓN → DECISIÓN → PLANIFICACIÓN  
> ↓  
> MOBILE → RESULTADO → NUEVA MEDICIÓN

Las reglas, indicadores, alertas y dashboards son manifestaciones del conocimiento; no deben convertirse en múltiples sistemas independientes.

# 2. OBJETIVOS

- Permitir que el ganadero explique sus reglas en lenguaje natural.

- Interpretar esas reglas contra el modelo real de GANUS.

- Calcular indicadores propios con datos disponibles.

- Recomendar indicadores relevantes según tipo de explotación, sector, industria y negocio.

- Medir el comportamiento de la finca como empresa.

- Incorporar referencias de mercado y sector para comparación.

- Detectar situaciones relevantes y convertirlas en alertas cuando corresponda.

- Explicar por qué existe una situación y qué datos la sustentan.

- Alimentar Advisory con conocimiento contextualizado.

- Convertir una recomendación aceptada en trabajo mediante Planificación.

- Evitar que el cliente tenga que configurar manualmente dashboards, widgets, fórmulas o alertas.

# 3. ALCANCE MVP

## 3.1 Incluido

- Contexto empresarial de finca y modelo productivo.

- Conocimiento base GANUS por tipo de explotación.

- Reglas propias expresadas en lenguaje natural, interpretadas y confirmadas.

- Indicadores propios, recomendados, de comportamiento y de referencia.

- Motor determinista de cálculo.

- Motor de situaciones.

- Alertas derivadas de situaciones.

- Hallazgos, riesgos, oportunidades y recomendaciones.

- Centro de Control estratégico web.

- Centro de Control táctico conectado a Planificación.

- Drill-down hasta datos, activos, tareas y evidencias cuando aplique.

- Advisory IA explicable y contextualizado.

- Inteligencia documental para PDF, Excel, Word e imágenes/OCR cuando aplique.

- Referencias sectoriales/mercado mediante fuentes autorizadas.

- Trazabilidad de regla, cálculo, fuente, periodo y evidencia.

- Propuestas de conocimiento generadas por IA, siempre separadas de conocimiento oficial.

- Paso de recomendación aceptada a Planificación.

## 3.2 No incluido en MVP

- Constructor visual avanzado de reglas para cliente final.

- Constructor manual de dashboards/widgets.

- Constructor independiente de alertas.

- Constructor independiente de automatizaciones.

- Agentes autónomos ejecutando procesos críticos.

- Predicción avanzada como requisito base.

- Optimización automática de programación.

- Workflow complejo de gobierno de conocimiento.

- Edición libre de SQL/fórmulas por usuario.

- Modificación directa de Inventario o Planificación por la IA.

# 4. PRINCIPIOS NO NEGOCIABLES

| **Principio**                             | **Regla**                                                  |
|-------------------------------------------|------------------------------------------------------------|
| Complejidad interna / simplicidad externa | El usuario no aprende el modelo técnico.                   |
| Knowledge interpreta                      | No administra identidad ni programación.                   |
| Cálculo determinista                      | La IA no inventa valores ni ejecuta fórmulas críticas.     |
| IA estructurada                           | El LLM devuelve objetos validados, no mutaciones directas. |
| Explicabilidad                            | Todo resultado debe poder mostrar fundamento.              |
| No inventar                               | Si faltan datos, se declara.                               |
| Situación antes que alerta                | Alerta = situación que requiere atención.                  |
| Dashboard como representación             | No es fuente de conocimiento ni constructor en MVP.        |
| Recomendación ≠ acción                    | La acción pasa por decisión/Planificación.                 |
| Fuente de verdad                          | Cada dominio mantiene su ownership.                        |
| Oficial ≠ inferido                        | La IA no publica silenciosamente conocimiento productivo.  |
| Versionamiento                            | Cambios no alteran silenciosamente históricos.             |

# 5. FRONTERAS CON LOS DEMÁS MÓDULOS

| **Dominio**  | **Es dueño de**                             | **Knowledge consume / produce** | **NO hace**                     |
|--------------|---------------------------------------------|---------------------------------|---------------------------------|
| Organization | Finca, usuarios, roles, procesos, espacios  | Contexto                        | No administra reglas/KPI        |
| Inventory    | Identidad, atributos, relaciones, lifecycle | Activos y hechos                | No crea identidad paralela      |
| Forms        | Definición y versionado de captura          | Resultados/evidencias           | No redefine formularios         |
| Planning     | Tareas, calendario, dataset, estados        | Cumplimiento, resultados        | No reprograma silenciosamente   |
| Mobile       | Ejecución/captura/offline                   | Resultados y alertas            | No cambia reglas                |
| STAGING      | Intercambio/sincronización                  | Eventos                         | No contiene lógica empresarial  |
| Analytics    | Presentación                                | Indicadores/situaciones         | No calcula conocimiento propio  |
| Advisory     | Interfaz conversacional                     | Knowledge contextualizado       | No responde sin fuentes         |
| Knowledge    | Interpretación                              | —                               | No es ERP ni gestor operacional |

# 6. MODELO CONCEPTUAL MÍNIMO

| **Objeto**    | **Definición**                                   | **Ejemplo**                                     |
|---------------|--------------------------------------------------|-------------------------------------------------|
| Contexto      | Qué tipo de negocio es y qué le importa          | Ganadería de carne; engorde; objetivo comercial |
| Regla         | Cómo el usuario quiere que GANUS interprete algo | Animal listo para venta 430–470 kg              |
| Indicador     | Medición reproducible                            | 32 animales en rango                            |
| Situación     | Estado detectado                                 | 7 animales llevan \>15 días en rango            |
| Recomendación | Qué debería considerar hacer                     | Evaluar comercialización                        |

Derivados: Alerta = situación priorizada; Dashboard = representación; Objetivo = meta asociada a indicador; Hallazgo/Riesgo/Oportunidad = tipos de interpretación.

# 7. CONTEXTO EMPRESARIAL

| **Dimensión**      | **Datos**                                   | **Origen**                       |
|--------------------|---------------------------------------------|----------------------------------|
| Organización       | Finca, ubicación, roles, espacios, procesos | Organization                     |
| Modelo productivo  | Especie, propósito, explotación             | Configuración GANUS/Organization |
| Objetivos          | Prioridades del negocio                     | GANUS + usuario                  |
| Activos            | Animales, infraestructura, equipos          | Inventory                        |
| Operación          | Tareas, resultados, tiempos, cumplimiento   | Planning/Mobile                  |
| Producción         | Pesos, leche, sanidad, reproducción         | Forms/Make/Odoo                  |
| Finanzas/comercial | Ventas, costos, compras, margen             | Odoo/documentos                  |
| Externo            | Sector, mercado, clima, benchmarks          | Fuentes autorizadas              |

El contexto debe construirse primero con datos existentes. Las preguntas al usuario solo completan vacíos relevantes.

# 8. FUENTES DE CONOCIMIENTO

| **Fuente**                | **Uso**                                                                                |
|---------------------------|----------------------------------------------------------------------------------------|
| Conocimiento base GANUS   | Buenas prácticas, modelos, KPIs, reglas generales, protocolos.                         |
| Conocimiento del ganadero | Reglas, prioridades, objetivos y criterios.                                            |
| Operación GANUS           | Inventory, Forms, Planning, Mobile, Make, eventos, cumplimiento, Track cuando aplique. |
| Información empresarial   | Odoo, estados financieros, ventas, compras, costos, activos, nómina.                   |
| Documentación técnica     | Protocolos, normativa ICA, manuales, procedimientos, certificaciones.                  |
| Sector/industria          | Benchmarks y KPIs de referencia.                                                       |
| Mercado                   | Precios de leche, carne, animales, insumos, etc.                                       |
| Clima                     | Variables ambientales cuando exista fuente autorizada.                                 |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>REGLA DE CONFIANZA<br />
</strong>Toda referencia externa debe conservar fuente, fecha, unidad, región/cobertura y vigencia. Sin esos metadatos no debe presentarse como referencia confiable.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 9. REGLAS DE NEGOCIO EN LENGUAJE NATURAL

El usuario expresa una intención de negocio; GANUS la transforma en estructura técnica.

> Usuario:  
> "Para mí un animal está listo para venta cuando pesa entre 430 y 470 kilos."  
>   
> Interpretación provisional:  
> Activo = bovino  
> Variable = peso actual  
> Condición = \>=430 kg AND \<=470 kg  
> Contexto = comercialización  
> Resultado = identificar animales en rango  
>   
> GANUS:  
> "Entendí tu regla así... ¿Es correcto?"  
> \[Confirmar\] \[Modificar\]

## 9.1 Flujo

1.  Capturar texto/voz.

2.  Identificar intención.

3.  Resolver entidades contra diccionario semántico GANUS.

4.  Resolver unidades, catálogos, fechas y contexto.

5.  Construir representación estructurada.

6.  Validar existencia y permisos.

7.  Identificar faltantes/ambigüedades.

8.  Mostrar interpretación humana.

9.  Confirmar/corregir.

10. Persistir versión activa.

## 9.2 Restricción técnica

El LLM no genera SQL de producción ni ejecuta mutaciones. Devuelve JSON estructurado validable. El motor determinista ejecuta las condiciones permitidas.

# 10. INDICADORES

| **Familia**    | **Definición**                    | **Ejemplos**                                              |
|----------------|-----------------------------------|-----------------------------------------------------------|
| Propio         | Calculado con datos de la finca   | Ganancia diaria, mortalidad, cumplimiento, costo/animal   |
| Recomendado    | GANUS propone que debería medirse | Peso al destete, intervalo entre partos, productividad/ha |
| Comportamiento | Mide cómo funciona la empresa     | Retrasos, reprocesos, productividad, cumplimiento         |
| Referencia     | Valor externo de comparación      | Precio kg carne, precio leche, precio animal              |

## 10.1 Contrato lógico

| **Campo**   | **Obligatorio** | **Contenido**                                   |
|-------------|-----------------|-------------------------------------------------|
| IndicatorID | Sí              | ID estable                                      |
| Name        | Sí              | Nombre                                          |
| Type        | Sí              | OWNED/RECOMMENDED/BEHAVIOR/REFERENCE            |
| Definition  | Sí              | Definición inequívoca                           |
| Formula     | Sí si calculado | Fórmula versionada                              |
| Unit        | Sí              | Unidad                                          |
| Dimensions  | Sí              | Tiempo, finca, especie, lote, etc.              |
| Source      | Sí              | Origen                                          |
| Period      | Cuando aplique  | Ventana                                         |
| Target      | Opcional        | Objetivo                                        |
| Benchmark   | Opcional        | Referencia                                      |
| Version     | Sí              | Versión                                         |
| DataQuality | Sí              | COMPLETE/PARTIAL/INSUFFICIENT/STALE/CONFLICTING |

# 11. MOTOR DE CÁLCULO

Los valores oficiales se calculan determinísticamente. La IA interpreta solicitudes y explica resultados, pero no sustituye el cálculo.

- No usar LLM para generar valores oficiales.

- Guardar fórmula y versión.

- Guardar periodo, filtros y cobertura.

- Guardar timestamp.

- Guardar calidad de datos.

- Si falta información, devolver estado INSUFFICIENT y explicar qué falta.

- Permitir reproducibilidad histórica.

> Ganancia diaria promedio =  
> (Peso final - Peso inicial) / días transcurridos  
>   
> Resultado:  
> valor + unidad + periodo + cobertura + calidad + versión de cálculo

# 12. MOTOR DE SITUACIONES

La situación es el centro funcional de Knowledge.

| **Tipo**          | **Descripción**                   | **Ejemplo**                    |
|-------------------|-----------------------------------|--------------------------------|
| Normal            | Dentro de comportamiento esperado | Mortalidad estable             |
| Desviación        | Fuera de objetivo/comportamiento  | Ganancia -8%                   |
| Incumplimiento    | Condición/tarea no cumplida       | 14 tareas vencidas             |
| Riesgo            | Impacto negativo posible          | Mortalidad creciente           |
| Oportunidad       | Potencial de beneficio            | 18 animales en rango comercial |
| Tendencia         | Cambio sostenido                  | Costo alimentación +12%        |
| Dato insuficiente | No puede evaluarse                | Falta superficie productiva    |

> Situation {  
> id, type, severity, status, context, detectedAt, period,  
> relatedIndicators\[\], relatedRules\[\], affectedAssets\[\],  
> evidence\[\], explanation, recommendations\[\], dataQuality,  
> knowledgeOrigin  
> }

# 13. ALERTAS

Una alerta es una situación que requiere atención. No se construye como módulo independiente.

| **Elemento** | **Regla**                                       |
|--------------|-------------------------------------------------|
| Origen       | Situation                                       |
| Prioridad    | Critical / High / Medium / Low                  |
| Destinatario | Según rol/contexto                              |
| Canales      | Centro de Control + notificaciones habilitadas  |
| Persistencia | Hasta resolver/descartar/expirar según política |
| Trazabilidad | Situación + fuente + evidencia                  |
| Acción       | Puede proponer tarea; no crea silenciosamente   |

> CRITICAL = riesgo inmediato / impacto alto / condición crítica  
> HIGH = atención prioritaria  
> MEDIUM = seguimiento  
> LOW = informativa/preventiva

# 14. SEGUIMIENTO

Seguimiento responde: ¿qué está pasando, cómo evoluciona y qué merece atención? No sustituye Planificación.

| **Vista**   | **Pregunta**                 | **Contenido**                                                      |
|-------------|------------------------------|--------------------------------------------------------------------|
| Estratégica | ¿Cómo está mi empresa?       | Estado por dimensiones, KPI, situaciones, tendencias, referencias  |
| Táctica     | ¿Cómo funciona la operación? | Cumplimiento, tareas, retrasos, resultados, alertas                |
| Detalle     | ¿Por qué ocurre?             | Indicador → situación → activos/tareas → evidencia → recomendación |

## 14.1 Dimensiones de referencia

Dirección empresarial; rentabilidad; liquidez; flujo de caja; crecimiento; inversión; producción; ganancia de peso; conversión; productividad; sanidad; mortalidad; enfermedades; tratamientos; bioseguridad; reproducción; preñez; natalidad; servicios; partos; abortos; inventario; operación; cumplimiento; productividad; tiempo; bienestar animal; condición corporal; administración; costos; compras; ventas; activos.

# 15. CENTRO DE CONTROL / DASHBOARD ESTRATÉGICO

El Dashboard es una representación automática del conocimiento. No se configura manualmente en el MVP.

> ESTADO DE MI NEGOCIO  
> Producción \| Sanidad \| Reproducción \| Operación \| Comercial \| Finanzas \| Bienestar  
>   
> GANUS RECOMIENDA REVISAR  
> 1. Situación prioritaria  
> 2. Situación prioritaria  
> 3. Oportunidad  
>   
> INDICADORES  
> valor + tendencia + comparación + calidad  
>   
> SITUACIONES  
> severidad + impacto + afectados  
>   
> REFERENCIAS  
> finca vs histórico vs sector vs mercado  
>   
> ADVISORY  
> preguntar / explicar / recomendar

- El estado general debe entenderse en menos de 30 segundos.

- Priorizar situaciones sobre volumen de gráficas.

- No mostrar gráficos sin significado empresarial.

- Toda cifra relevante debe permitir drill-down.

- Mostrar tendencia solo con histórico suficiente.

- Mostrar comparación solo con referencia válida.

- Mostrar calidad de datos.

# 16. CENTRO DE CONTROL TÁCTICO

Planificación responde qué trabajo debe hacerse; Control Táctico responde cómo está funcionando y qué requiere atención.

- Alertas operativas siempre visibles.

- Indicadores de cumplimiento, vencidas, hoy, ejecución, validación y bloqueos.

- Kanban operacional cuando corresponda.

- Navegación a tarea y resultado por activo.

- Recepción de alertas originadas en Mobile.

- Conversión de una situación/recomendación en tarea mediante Planificación.

# 17. ADVISORY IA

Advisory es la interfaz conversacional del conocimiento, no un chatbot generalista.

| **Tipo**      | **Ejemplo**                                                  |
|---------------|--------------------------------------------------------------|
| Estado        | ¿Cómo está mi finca?                                         |
| Explicación   | ¿Por qué bajó la ganancia de peso?                           |
| Comparación   | ¿Cómo estoy frente al mes anterior/sector/mercado?           |
| Diagnóstico   | ¿Qué situaciones son prioritarias?                           |
| Recomendación | ¿Qué debería revisar?                                        |
| Exploración   | ¿Qué indicadores debería controlar?                          |
| Dato faltante | ¿Qué necesito para medir productividad por hectárea?         |
| Acción        | Crear tarea con esta recomendación (confirmación requerida). |

## 17.1 Respuesta explicable

> Respuesta breve  
> ↓  
> Qué encontré  
> ↓  
> Por qué  
> ↓  
> Datos/indicadores usados  
> ↓  
> Comparaciones  
> ↓  
> Recomendación  
> ↓  
> \[Ver detalle\] \[Crear tarea\]

## 17.2 Seguridad

- No inventar datos, precios, KPI o eventos.

- No ocultar incertidumbre.

- No afirmar causalidad sin evidencia.

- No usar fuente externa sin procedencia.

- No ejecutar acción crítica sin confirmación.

- No modificar Inventario ni Planificación directamente.

- Toda recomendación debe apuntar a fundamentos.

# 18. IMPLEMENTACIÓN IA

| **Capacidad**      | **Entrada**                    | **Salida**                               | **Control**                   |
|--------------------|--------------------------------|------------------------------------------|-------------------------------|
| Interpretar regla  | Texto/voz + contexto           | JSON de intención/entidades/condiciones  | Schema + catálogos + permisos |
| Analizar documento | Archivo + contexto             | Conceptos/variables/hallazgos/propuestas | Fuente + confianza + revisión |
| Explicar situación | Situation + datos              | Explicación                              | Solo datos presentes          |
| Recomendar         | Situación + contexto           | Recomendación                            | Reglas + evidencia            |
| Advisory           | Pregunta + contexto recuperado | Respuesta + fundamentos                  | RAG/contexto + trazabilidad   |

> {  
> "intent":"DEFINE_BUSINESS_RULE",  
> "entities":\[\],  
> "conditions":\[\],  
> "requested_output":\[\],  
> "missing_information":\[\],  
> "confidence":0.0,  
> "explanation":"...",  
> "requires_confirmation":true  
> }

La aplicación rechaza respuestas IA fuera del schema. El LLM se integra detrás de un adaptador para no acoplar GANUS a un proveedor.

# 19. INTELIGENCIA DOCUMENTAL

- PDF, Excel, Word e imágenes/OCR cuando aplique.

- Registrar documento, origen, fecha, tipo e identificador.

- Extraer texto/estructura.

- Identificar entidades, fechas, valores, unidades y conceptos.

- Relacionar con contexto de finca.

- Proponer indicadores, reglas, situaciones o recomendaciones.

- No publicar automáticamente una regla productiva generada por IA.

- Conservar referencia al documento.

> DOCUMENTO → INGESTA → EXTRACCIÓN → COMPRENSIÓN IA  
> → CONTEXTO → PROPUESTA → VALIDACIÓN → CONOCIMIENTO

# 20. REFERENCIAS DE MERCADO / SECTOR / INDUSTRIA

| **Tipo**  | **Ejemplos**                         | **Metadatos**                          |
|-----------|--------------------------------------|----------------------------------------|
| Mercado   | precio leche, carne, animal, insumos | fuente, fecha, región, unidad, moneda  |
| Sector    | rangos productivos, KPIs             | fuente, modelo, periodo                |
| Industria | benchmarks empresariales             | fuente, población, periodo, definición |
| Normativa | requisitos/límites                   | fuente oficial, vigencia               |
| Clima     | lluvia, temperatura, eventos         | fuente, ubicación, fecha               |

En MVP el contrato debe quedar preparado aunque no todas las fuentes externas estén integradas.

# 21. GOBIERNO

| **Clasificación** | **Significado**                         | **Productivo**          |
|-------------------|-----------------------------------------|-------------------------|
| Oficial GANUS     | Conocimiento validado de plataforma     | Sí, según publicación   |
| Oficial finca     | Regla confirmada por usuario autorizado | Sí                      |
| Inferido IA       | Hallazgo/recomendación                  | No directamente         |
| Propuesto         | Nuevo KPI/regla pendiente               | No                      |
| Referencia        | Dato externo                            | No                      |
| Histórico         | Versión anterior                        | Solo consulta histórica |

MVP no requiere workflow complejo de aprobación, pero sí debe distinguir claramente oficial, inferido y propuesto.

# 22. CALIDAD DE DATOS

- COMPLETE: cobertura suficiente.

- PARTIAL: cobertura incompleta.

- INSUFFICIENT: no alcanza para evaluar.

- STALE: información fuera de vigencia esperada.

- CONFLICTING: fuentes incompatibles.

La calidad acompaña indicadores y recomendaciones; Advisory debe reconocerla.

# 23. DRILL-DOWN Y TRAZABILIDAD

> CENTRO DE CONTROL  
> ↓  
> INDICADOR  
> ↓  
> SITUACIÓN  
> ↓  
> REGLA / DEFINICIÓN  
> ↓  
> PERIODO + FILTROS  
> ↓  
> DATOS FUENTE  
> ↓  
> ACTIVOS / ACTIVIDADES  
> ↓  
> EVIDENCIAS  
> ↓  
> RECOMENDACIÓN  
> ↓  
> ACCIÓN / TAREA

| **Pregunta**              | **Debe responder**              |
|---------------------------|---------------------------------|
| ¿De dónde salió?          | Fuente + definición + periodo   |
| ¿Por qué está en rojo?    | Condición que produjo situación |
| ¿Qué activos afectan?     | ActivoID + contexto             |
| ¿Qué actividad/evidencia? | TaskID/resultado/evidencia      |
| ¿Qué recomienda?          | Recomendación + fundamento      |
| ¿Quién decidió?           | Usuario + fecha                 |
| ¿Qué pasó después?        | Resultado posterior             |

# 24. EVENTOS E INTEGRACIÓN LÓGICA

- ASSET_CREATED / ASSET_UPDATED / ASSET_STATUS_CHANGED

- FORM_RESULT_RECORDED

- TASK_PUBLISHED / TASK_STARTED / TASK_COMPLETED / TASK_CANCELLED

- TASK_ASSET_RESULT_UPDATED

- FIELD_ALERT_CREATED

- DOCUMENT_ADDED / DOCUMENT_ANALYZED

- SALE_RECORDED / PURCHASE_RECORDED cuando exista integración

- MARKET_REFERENCE_UPDATED

- KNOWLEDGE_RULE_CONFIRMED / KNOWLEDGE_RULE_CHANGED

- INDICATOR_RECALCULATION_REQUESTED

Inbox/Outbox, idempotencia, retries, DLQ y contratos técnicos se detallarán en Documento 09. Aquí se define el comportamiento funcional.

# 25. PANTALLAS MVP

| **Pantalla**       | **Objetivo**         | **Mínimo**                                          |
|--------------------|----------------------|-----------------------------------------------------|
| Centro de Control  | Estado del negocio   | Resumen, prioridades, KPI, situaciones, referencias |
| Situación          | Entender             | Explicación, afectados, evidencia, recomendación    |
| Mis reglas         | Reglas propias       | Natural language, confirmar, activar/desactivar     |
| Indicadores        | Medición             | Propios/recomendados/comportamiento/referencia      |
| Advisory           | Conversar            | Pregunta, respuesta, fundamentos, navegación        |
| Documentos         | Agregar conocimiento | Carga, análisis, propuestas                         |
| Detalle indicador  | Entender cálculo     | Fórmula, periodo, cobertura, fuente, comparación    |
| Detalle referencia | Entender mercado     | Valor, fuente, fecha, región, unidad                |

No construir en MVP: diseñador de dashboards, widgets, editor de SQL, editor avanzado de alertas, editor independiente de automatizaciones.

# 26. CASOS FUNCIONALES DE REFERENCIA

| **Caso**       | **Entrada**                                  | **Resultado**                                       |
|----------------|----------------------------------------------|-----------------------------------------------------|
| Peso comercial | “Animal listo entre 430 y 470 kg”            | Indicador + situación + oportunidad + recomendación |
| Mortalidad     | “Avisar si supera 2% mensual”                | KPI + comparación + alerta                          |
| Vacuna         | “No quiero vacunas vencidas”                 | Situación + alerta + posible tarea                  |
| Costos         | “Alimentación no debe crecer más que ventas” | Comparación de tendencias + situación               |
| Dato faltante  | “Quiero productividad/ha”                    | Detecta falta de superficie y la solicita           |
| Mercado        | “¿Estoy vendiendo bien?”                     | Cruza venta propia con referencia válida y explica  |

# 27. CRITERIOS DE ACEPTACIÓN

**AC-01** La finca recibe conocimiento base sin parametrización técnica inicial.

**AC-02** El usuario expresa una regla en lenguaje natural.

**AC-03** GANUS muestra cómo la entendió antes de activarla.

**AC-04** La regla queda versionada y trazable.

**AC-05** Indicadores propios se calculan determinísticamente.

**AC-06** Se distinguen las cuatro familias de indicadores.

**AC-07** Se detecta falta de datos.

**AC-08** Se detectan situaciones.

**AC-09** Situaciones pueden generar alertas.

**AC-10** Centro de Control funciona sin configuración manual de widgets.

**AC-11** Existe drill-down.

**AC-12** Advisory responde solo con conocimiento contextualizado.

**AC-13** Advisory explica fundamentos.

**AC-14** Advisory no inventa datos.

**AC-15** Una recomendación aceptada puede llegar a Planificación.

**AC-16** Alertas Mobile llegan por STAGING.

**AC-17** Documentos generan propuestas sin publicación automática.

**AC-18** Referencias externas conservan fuente/fecha/unidad/cobertura.

**AC-19** Históricos no cambian silenciosamente.

**AC-20** El MVP no depende de un constructor complejo.

# 28. BACKLOG TÉCNICO RECOMENDADO

| **ID** | **Componente**        | **Objetivo**                  |
|--------|-----------------------|-------------------------------|
| KN-001 | Context Model         | Contexto por finca            |
| KN-002 | Knowledge Catalog     | Tipos/origen/estado           |
| KN-003 | Semantic Dictionary   | Términos → entidades GANUS    |
| KN-004 | Rule Interpretation   | Lenguaje natural → estructura |
| KN-005 | Rule Validator        | Entidades/unidades/permisos   |
| KN-006 | Rule Runtime          | Evaluación determinista       |
| KN-007 | Indicator Registry    | Definiciones versionadas      |
| KN-008 | Indicator Calculator  | Cálculo reproducible          |
| KN-009 | Situation Engine      | Detección de situaciones      |
| KN-010 | Alert Resolver        | Severidad/prioridad/destino   |
| KN-011 | Reference Service     | Benchmarks/mercado            |
| KN-012 | Provenance            | Trazabilidad                  |
| KN-013 | Document Intelligence | Ingesta + propuestas          |
| KN-014 | Advisory Retrieval    | Contexto recuperable          |
| KN-015 | Advisory Response     | Respuesta explicable          |
| KN-016 | Control Center API    | Agregación                    |
| KN-017 | Drill-down            | Indicador → evidencia         |
| KN-018 | Planning Handoff      | Recomendación → tarea         |
| KN-019 | Knowledge Audit       | Cambios/decisiones            |
| KN-020 | Data Quality          | Cobertura/frescura/conflicto  |

# 29. GUÍA PARA DESARROLLO CON IA DE CODIFICACIÓN

- Implementar como dominio dentro del modular monolith; no microservicio obligatorio.

- Separar dominio, aplicación, infraestructura y presentación.

- No acceder directamente a repositorios de otros dominios.

- Consumir contratos/eventos o servicios autorizados.

- Versionar reglas e indicadores.

- Guardar provenance de resultados.

- Persistir estado; no depender de memoria del LLM.

- LLM detrás de adapter.

- Prompts no contienen la única definición de reglas de negocio.

- Catálogos y definiciones críticas deben persistir.

- LLM no genera SQL de producción.

- LLM no ejecuta infraestructura.

- Toda mutación pasa por caso de uso autorizado.

- Registrar versión de modelo/prompt cuando sea relevante.

- Tests de regresión deben asegurar que cambios de conocimiento no rompen históricos.

# 30. MODELO LÓGICO DE DATOS

> knowledge_context  
> knowledge_rule  
> knowledge_rule_version  
> knowledge_rule_source  
> knowledge_indicator  
> knowledge_indicator_version  
> knowledge_indicator_value  
> knowledge_indicator_calculation  
> knowledge_situation  
> knowledge_situation_evidence  
> knowledge_recommendation  
> knowledge_alert  
> knowledge_reference  
> knowledge_reference_value  
> knowledge_document_analysis  
> knowledge_finding  
> knowledge_provenance  
> knowledge_audit  
> knowledge_ai_interaction  
>   
> Context  
> ├─ rules  
> ├─ indicators  
> ├─ situations  
> ├─ references  
> └─ recommendations  
>   
> RuleVersion → Indicator / Situation / Alert / Recommendation  
> IndicatorValue → Situation  
> Situation → Assets / Tasks / Evidence  
> Recommendation → accepted → Planning Task

El modelo físico, claves, índices, constraints y ownership se definirán en Documento 10.

# 31. SEGURIDAD

- Respetar finca/contexto del usuario.

- No consultar datos fuera del ámbito autorizado.

- Registrar creador/modificador de reglas y decisiones.

- Aplicar permisos de documentos.

- Advisory no debe revelar datos de otra finca.

- Validar permisos antes de crear una tarea.

# 32. PRUEBAS OBLIGATORIAS

| **Tipo**        | **Qué validar**                                         |
|-----------------|---------------------------------------------------------|
| Unitarias       | Fórmulas, operadores, severidad, clasificación, calidad |
| Integración     | Eventos de Inventory/Planning/Mobile                    |
| Contract        | Schemas IA/eventos/respuestas                           |
| IA estructurada | Casos válidos, ambiguos e inválidos                     |
| Regression      | Versiones e históricos                                  |
| Security        | Aislamiento por finca/rol                               |
| E2E             | Regla → indicador → situación → recomendación → tarea   |
| Data Quality    | Faltantes, duplicados, fechas, unidades, conflictos     |
| Advisory        | No inventar + fundamentos + incertidumbre               |

# 33. ESCENARIO E2E OBLIGATORIO

> 1\. Finca de carne configurada.  
> 2. Bovinos con pesos y fechas en Inventory.  
> 3. Usuario dice: “Animal listo para venta entre 430 y 470 kg”.  
> 4. IA interpreta.  
> 5. Usuario confirma.  
> 6. Regla ACTIVE.  
> 7. Indicador “Animales en peso comercial” = 32.  
> 8. Knowledge detecta 7 con \>15 días en rango.  
> 9. Situation = OPORTUNIDAD/ATENCIÓN.  
> 10. Advisory explica.  
> 11. Usuario acepta recomendación.  
> 12. Planning recibe propuesta con afectados.  
> 13. Supervisor publica tarea.  
> 14. Mobile ejecuta y captura por animal.  
> 15. Resultado vuelve por STAGING.  
> 16. Knowledge recalcula.  
> 17. Centro de Control actualiza.  
> 18. Todo el recorrido queda trazable.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>PRUEBA DE DISEÑO<br />
</strong>Si este escenario exige que el administrador configure manualmente varios constructores, la implementación está mal diseñada. Debe sentirse como una conversación de negocio que se convierte en inteligencia y luego en acción.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 34. EVOLUCIÓN POST-MVP

- Knowledge Studio avanzado para usuarios expertos.

- Constructor visual de reglas.

- Simulación de reglas.

- Versionamiento/gobierno formal.

- What-if y escenarios.

- Modelos predictivos.

- Agentes especializados.

- Automatizaciones con aprobación.

- Benchmarking avanzado.

- Más fuentes externas.

- Aprendizaje continuo.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>REGLA<br />
</strong>La evolución puede exponer capacidades internas del motor. El MVP no debe anticipar esa complejidad en la interfaz.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 35. GLOSARIO

| **Término**       | **Definición**                              |
|-------------------|---------------------------------------------|
| Knowledge         | Motor de comprensión empresarial.           |
| Advisory          | Interfaz IA del conocimiento.               |
| Regla             | Criterio de negocio expresado y validado.   |
| Indicador         | Medición reproducible.                      |
| Situación         | Condición detectada.                        |
| Alerta            | Situación priorizada.                       |
| Hallazgo          | Interpretación relevante.                   |
| Referencia        | Valor externo de comparación.               |
| Centro de Control | Vista de estado/seguimiento.                |
| Drill-down        | Navegación a detalle/evidencia.             |
| Provenance        | Origen y trazabilidad.                      |
| Oficial           | Conocimiento publicado y validado.          |
| Inferido          | Resultado generado por IA.                  |
| Calidad           | Suficiencia/frescura/consistencia del dato. |

# 36. REGLAS FINALES PARA EL PROGRAMADOR

**R01** No agregar módulos o constructores no definidos aquí.

**R02** No duplicar responsabilidades de otros dominios.

**R03** No convertir Knowledge en ERP paralelo.

**R04** No convertir Advisory en chatbot sin fuentes.

**R05** No usar LLM para cálculos críticos.

**R06** No permitir datos inventados.

**R07** No permitir resultados sin trazabilidad.

**R08** No presentar referencias externas sin metadatos.

**R09** No crear alertas sin situación de origen.

**R10** No crear dashboard configurable en MVP.

**R11** No crear editor de widgets.

**R12** No ejecutar acciones críticas automáticamente.

**R13** Toda propuesta IA debe poder confirmarse/rechazarse.

**R14** Toda regla activa tiene versión.

**R15** Toda fórmula es reproducible.

**R16** Todo indicador declara calidad.

**R17** Todo resultado visible explica su origen.

**R18** Toda integración respeta arquitectura oficial.

**R19** Antes de crear una nueva entidad, comprobar si cabe en Contexto, Regla, Indicador, Situación o Recomendación.

**R20** Si una función obliga al usuario a aprender la estructura interna del motor, debe rediseñarse.

# 37. BASE DOCUMENTAL Y DECISIONES CONSOLIDADAS

La especificación conserva la visión original de Knowledge como centro de inteligencia empresarial: conocimiento base + operación + documentación + contexto externo; interpretación documental; indicadores y reglas como manifestaciones del conocimiento; dashboard generado automáticamente; Advisory contextualizado y explicable; y separación entre conocimiento oficial e inferido.

La decisión de simplificación es de producto y UX: estas capacidades no se exponen como múltiples constructores independientes durante el MVP. Se concentran en un motor y se manifiestan mediante Contexto, Reglas, Indicadores, Situaciones, Recomendaciones, Centro de Control y Advisory.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>CRITERIO DE CIERRE<br />
</strong>El módulo está correctamente diseñado cuando un ganadero puede explicar cómo funciona su negocio en términos naturales y GANUS puede demostrar, con datos y trazabilidad, qué está pasando, por qué importa y qué recomienda, sin convertir al usuario en administrador de un motor de reglas.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>
