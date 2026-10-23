# 00 · Cerebro GANUS MVP v1.1

> **Fuente de solo lectura.** Convertido a Markdown desde `00_CEREBRO_GANUS_MVP_v1.1.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**GANUS  
CEREBRO MVP**

*Contexto funcional común para Producto, UX, Arquitectura, Desarrollo y Claude Code*

**Versión 1.1 · Documento 00 del paquete de desarrollo**

# Control del documento

| Campo                      | Definición                                                                                 |
|----------------------------|--------------------------------------------------------------------------------------------|
| Propósito                  | Establecer el contexto común que debe respetar cualquier desarrollo del MVP.               |
| Nivel                      | Funcional transversal. No sustituye arquitectura, contratos ni especificaciones de módulo. |
| Fuente funcional principal | GANUS · Alcance y Especificación Funcional Completa v3.1.                                  |
| Fuente de visión           | GANUS · Visión, Alcance y Arquitectura Unificada v2.0.                                     |
| Criterio de prioridad      | MVP cerrado por los hallazgos y decisiones consolidadas.                                   |
| Uso                        | Debe leerse antes de desarrollar cualquier módulo.                                         |

# 1. Propósito

Este documento establece el lenguaje común, los límites funcionales, los principios y las reglas transversales que deben utilizar todos los documentos y desarrollos del MVP GANUS. Su función es evitar que cada módulo sea diseñado como una aplicación independiente o que un desarrollador tenga que interpretar nuevamente la visión.

No define la arquitectura técnica detallada, el modelo físico de base de datos ni los contratos técnicos. Esos temas pertenecen a los documentos correspondientes.

# 2. Qué es GANUS

GANUS es una plataforma B2B de gestión empresarial y operacional para fincas ganaderas. Su propósito es transformar la finca en una empresa organizada, controlada, trazable y capaz de tomar decisiones basadas en datos, conectando personas, activos, territorio, actividades, automatización, conocimiento, analítica, inteligencia y procesos empresariales.

En el ecosistema completo, el activo es la identidad operacional transversal; los formularios definen cómo se captura; Planificación convierte necesidades en trabajo; el móvil ejecuta; Seguimiento controla; Knowledge convierte datos en resultados gobernados; Analytics presenta; Advisory interpreta; ODOO aporta la dimensión empresarial; Track territorializa; y la Plataforma de Datos integra el histórico.

# 3. Diferencia entre ecosistema y MVP

La visión completa de GANUS define hacia dónde evoluciona la plataforma. El MVP define qué capacidades se implementan primero. Una capacidad puede pertenecer al ecosistema y, simultáneamente, estar fuera del primer release.

| Capa                      | MVP                                                                                              | Evolución posterior                             |
|---------------------------|--------------------------------------------------------------------------------------------------|-------------------------------------------------|
| Organización              | Grupo opcional, fincas, usuarios, roles, permisos, contexto                                      | Gobierno avanzado                               |
| Inventario                | Activos, identificadores, atributos, relaciones, documentos, historial, migración, ciclo de vida | Capacidades avanzadas de valoración/explotación |
| Formularios               | Captura dinámica, catálogos, validaciones, métodos, evidencias, versiones                        | Knowledge Studio avanzado                       |
| Planificación             | Tareas unitarias/masivas, población, programación, asignación, paquete de trabajo                | Balanceador y optimización avanzada             |
| Móvil                     | Ejecución, captura, evidencias, voz, offline y sincronización                                    | Asistencia contextual avanzada                  |
| Seguimiento               | Control operacional, estados, cumplimiento y trazabilidad                                        | Analítica avanzada                              |
| Dashboard                 | Indicadores iniciales del onboarding y vista táctica                                             | Canvas Builder y biblioteca libre de widgets    |
| Knowledge / Advisory      | Reglas y resultados iniciales, interpretación básica                                             | Agente avanzado, modelos predictivos            |
| Track                     | Contratos y modelo preparados                                                                    | Gemelo Digital, geocercas, tracking avanzado    |
| Potreros / Automatización | Modelo de eventos preparado                                                                      | Hardware e integración física avanzada          |
| ODOO                      | Frontera definida                                                                                | Integración profunda                            |

# 4. Cadena funcional oficial del MVP

**ORGANIZAR → IDENTIFICAR → CONFIGURAR CAPTURA → PLANIFICAR → ENTREGAR AL CAMPO → EJECUTAR → SINCRONIZAR → CONTROLAR → ENTENDER → DECIDIR**

Esta cadena no significa que todos los módulos ejecuten todas las etapas. Cada dominio tiene una responsabilidad definida y entrega información al siguiente.

# 5. Responsabilidad de los dominios

| Dominio                   | Es responsable de                                                                                                     | No debe hacer                                                           |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| Organización              | Contexto empresarial, grupos, fincas, usuarios, roles, permisos y asignación de ámbito.                               | Administrar activos, actividades o indicadores.                         |
| Inventario                | Identidad única de activos, identificadores, atributos, relaciones, ubicación, documentos, historial y ciclo de vida. | Planificar trabajo, crear tareas operativas o ser dueño de indicadores. |
| Formularios dinámicos     | Definir la captura: campos, métodos, catálogos, validaciones, evidencias y versiones.                                 | Programar, asignar, segmentar población o ejecutar.                     |
| Planificación             | Convertir necesidades en tareas; segmentar, calcular población, programar, asignar y publicar trabajo.                | Crear identidad de activos o administrar el diseño de formularios.      |
| Móvil                     | Recibir el paquete de trabajo, ejecutar, capturar, adjuntar evidencia y sincronizar.                                  | Planificar, segmentar población o administrar configuración.            |
| Seguimiento               | Consultar/controlar el ciclo de trabajo, estados, vencimientos, cumplimiento y trazabilidad.                          | Redefinir una tarea o modificar su población.                           |
| Dashboard / Analytics     | Presentar resultados e indicadores publicados según nivel de decisión.                                                | Crear reglas paralelas o convertirse en fuente maestra.                 |
| Knowledge                 | Definir/evaluar reglas e indicadores gobernados y producir resultados explicables.                                    | Inventar datos o modificar maestros sin autorización.                   |
| Advisory                  | Interpretar datos/resultados y formular recomendaciones explicables.                                                  | Ser fuente maestra o ejecutar cambios por sí solo.                      |
| TRACK                     | Representar territorio, posiciones y eventos espaciales.                                                              | Administrar la identidad maestra del activo.                            |
| Potreros / Automatización | Detectar eventos físicos y normalizarlos.                                                                             | Decidir por sí mismo el significado empresarial.                        |
| ODOO / CORE               | Gestionar la dimensión ERP empresarial.                                                                               | Duplicar la operación especializada de campo.                           |

# 6. El activo como identidad transversal

El activo es el centro de identidad de GANUS. La solución no debe modelar la identidad operacional como entidades aisladas de animales, equipos, infraestructura o suministros cuando puedan ser representados como activos.

Modelo conceptual:

**TIPO DE ACTIVO → PLANTILLA → VERSIÓN → ACTIVO → IDENTIFICADORES → UBICACIÓN → RELACIONES → ATRIBUTOS → DOCUMENTOS → HISTORIAL**

Un activo puede tener múltiples identificadores —por ejemplo RFID, código interno, chapeta, código oficial, QR o código legado— pero todos deben resolver a un único ActivoID.

# 7. Ciclo de vida del activo

El Ciclo de Vida es una capacidad transversal del Inventario. Al crear un activo se puede asignar un ciclo; el ciclo define etapas, tareas referenciales, condiciones e indicadores. La etapa actual es un resultado del ciclo y de los datos disponibles, no un campo aislado que el usuario pueda modificar sin control.

**Relación oficial:** Inventario asigna y mantiene el ciclo → Planificación puede utilizar tareas referenciales → Móvil registra ejecución → Knowledge evalúa condiciones y desviaciones → Analytics presenta → Advisory interpreta.

Los cambios de etapa deben conservar fecha, fuente/evento y trazabilidad. Las versiones del ciclo no reescriben el histórico.

# 8. Configuración de captura: Formularios dinámicos

Los Formularios Dinámicos son el mecanismo configurable de captura. Una plantilla define campos, métodos de captura, catálogos, validaciones, dependencias, evidencias y versión publicada.

- Una versión publicada es inmutable.

- Una modificación produce una nueva versión.

- El formulario no administra agenda, segmentación ni asignación.

- El tipo de activo puede ser criterio de elegibilidad/segmentación, pero no constituye contexto operativo de la plantilla.

- La taxonomía maestra es consumida por Inventario y Formularios.

- El formulario debe poder utilizar métodos como captura manual, selección, voz, RFID, cámara, dispositivo o integración cuando corresponda.

# 9. Planificación: convertir necesidad en trabajo

Planificación es el dueño del trabajo operativo. Determina sobre quién se trabaja, qué se hace, cuándo y quién lo ejecuta. La población objetivo debe estar calculada y visible antes de publicar.

Estados oficiales de tarea:

**Borrador → Validada → Programada → Disponible para campo → En ejecución → Pausada → Finalizada → Por validar → Cerrada**

Cancelada es una salida alterna. Los estados “Aceptada” y “En desplazamiento” no pertenecen al MVP.

# 10. Principio del móvil

El operador no administra GANUS. GANUS le entrega el trabajo, le facilita capturarlo y le permite informar lo extraordinario.

- El móvil recibe un paquete de trabajo definido por Planificación.

- El paquete contiene la tarea, formulario y versión, clave de búsqueda, recurso, fechas, prioridad, contexto y población.

- El móvil no cambia la población objetivo.

- La captura confirmada localmente no debe perderse por falta de conectividad.

- Cada ejecución debe conservar tarea, activo, usuario, formulario/version y tiempos.

- La navegación MVP se concentra en Tareas, Alertas e Historial; “Novedades” se absorbe en Alertas como tipo “Otro”.

- Voz forma parte de la ejecución/captura; no es una aplicación separada.

# 11. Seguimiento y trazabilidad

**DEFINICIÓN → SEGMENTACIÓN → VALIDACIÓN → PROGRAMACIÓN → ENTREGA → EJECUCIÓN → RESULTADO → SEGUIMIENTO**

GANUS debe permitir reconstruir el ciclo completo de una tarea y relacionarlo con activo, usuario, formulario/version y evidencias.

El cumplimiento básico se expresa como ejecutado / total. El seguimiento consulta y controla; no redefine la planificación.

# 12. Knowledge y Advisory

Knowledge convierte datos en conocimiento gobernado mediante reglas. Analytics presenta los resultados publicados. Advisory interpreta y recomienda.

| Concepto         | Responsable                   | Regla                                     |
|------------------|-------------------------------|-------------------------------------------|
| Dato             | Fuente operacional            | No se altera para satisfacer una regla.   |
| Cálculo          | Knowledge / lógica autorizada | Debe ser reproducible.                    |
| Regla            | Knowledge                     | Debe estar versionada y gobernada.        |
| Indicador/alerta | Resultado de regla            | Debe poder explicar su origen.            |
| Recomendación    | Advisory                      | Debe indicar datos/regla que la soportan. |

La IA es asistida: no es fuente maestra ni publica reglas productivas sin validación.

# 13. Eventos e integraciones

El patrón general de interoperabilidad es: FUENTE → EVENTO/API → NORMALIZACIÓN → OBJETO GANUS → REGLA → RESULTADO → DESTINO.

- Inventory publica cambios de activos.

- Planning publica actividades programadas y sus cambios de estado.

- Mobile publica ejecución y resultados.

- Track publica eventos espaciales.

- Automatización publica eventos físicos.

- Knowledge publica resultados gobernados.

- Los identificadores transversales deben mantenerse estables y compartidos.

# 14. Reglas transversales no negociables

- Una fuente maestra por concepto.

- No duplicar identidad entre módulos.

- No permitir que la UI determine ownership de datos.

- No convertir JSON libre en sustituto de relaciones críticas.

- No crear procesos administrativos que correspondan a ODOO.

- No implementar funciones fuera del MVP solo porque sean técnicamente fáciles.

- No crear una integración directa consultando tablas privadas de otro dominio.

- Todo cambio relevante debe ser trazable.

- Todo resultado de Knowledge/Advisory debe ser explicable.

- El offline debe preservar capturas confirmadas.

- Las versiones publicadas son inmutables.

- Los registros relevantes se desactivan/conservan; no se eliminan físicamente sin una regla explícita.

- Los desarrolladores no deben resolver contradicciones funcionales inventando comportamiento: la especificación consolidada v3.1 es el criterio de cierre.

# 15. Convenciones de lenguaje

| Término oficial     | Uso                                                                      |
|---------------------|--------------------------------------------------------------------------|
| Activo              | Entidad universal de identidad operacional.                              |
| Actividad / Tarea   | Unidad de trabajo gestionada por Planificación.                          |
| Formulario dinámico | Mecanismo de definición de captura.                                      |
| Población           | Conjunto calculado de activos objetivo de una tarea.                     |
| Paquete de trabajo  | Información que recibe el móvil para ejecutar exactamente una tarea.     |
| Ciclo de vida       | Definición versionada de etapas y comportamiento esperado de un activo.  |
| Etapa               | Estado contextual calculado/derivado dentro del ciclo.                   |
| Tarea referencial   | Guía de trabajo asociada a una etapa; no implica ejecución automática.   |
| Regla               | Conocimiento gobernado que transforma datos/condiciones en un resultado. |
| Advisory            | Capa de interpretación y recomendación.                                  |
| Track               | Dominio territorial y de ubicación.                                      |
| STAGING             | Frontera controlada de intercambio entre dominios/soluciones.            |

# 16. Qué debe consultar un desarrollador

Orden obligatorio de lectura:

1.  00 · CEREBRO GANUS MVP — este documento.

2.  01 · ARQUITECTURA GANUS — cómo construir.

3.  Documento funcional del módulo que va a desarrollar — qué debe hacer.

4.  09 · MODELO BD DE REFERENCIA — dónde vive la información.

5.  08 · CONTRATOS E INTEGRACIONES — cómo cruza fronteras.

6.  10 · CONSTITUCIÓN DE DESARROLLO — reglas de implementación y uso de Claude Code.

# 17. Criterio de cierre del documento

Este documento queda correcto cuando un desarrollador puede responder, antes de abrir el código: qué es GANUS, qué pertenece al MVP, qué módulo es dueño de cada concepto, qué módulo no puede hacer determinada acción, cuál es la cadena operacional y qué documentos debe consultar para resolver el detalle.

# 18. Base documental utilizada

Este documento se deriva de la Visión, Alcance y Arquitectura Unificada v2.0 y del Alcance y Especificación Funcional Completa v3.1, que consolida los documentos funcionales de Organización, Inventario, Formularios y Planificación y cierra las decisiones del MVP.

Nota: este documento no sustituye las especificaciones funcionales detalladas; las organiza y fija sus reglas transversales.
