# 06 · Funcional Mobile v1.0

> **Fuente de solo lectura.** Convertido a Markdown desde `06_FUNCIONAL_MOBILE_GANUS_v1.0.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

Ejecución de campo · Voz · Captura · Offline · Sincronización · Documento funcional MVP v1.0

**Complementa 00_CEREBRO_GANUS_MVP, la Arquitectura Oficial y los documentos funcionales de Organización, Inventario, Formularios y Planificación.**

# 1. Propósito

GANUS Mobile es la aplicación Android nativa para ejecutar en campo las tareas preparadas por Planificación. Opera offline-first, mantiene una base de datos local en el dispositivo y sincroniza bidireccionalmente con STAGING.

La ejecución está determinada por los datos recibidos: tarea, formulario y versión, dataset, campos, obligatoriedad, validaciones, contexto, tiempos, responsable y demás condiciones de ejecución. Mobile no planifica ni redefine el trabajo.

La voz es el mecanismo principal de captura de información de formularios, especialmente por las condiciones agrestes del campo. La aplicación debe convertir voz en datos estructurados con controles de interpretación, normalización, validación y confirmación. La captura convencional mediante selección o escritura siempre está disponible.

Mobile incorpora además un botón de ALERTA siempre visible para registrar rápidamente video, nota de voz, foto y/o texto y enviarlos al Centro de Alertas mediante STAGING, con prioridad operativa.

# 2. Principios rectores

- Mobile recibe trabajo; no lo define.

- El paquete de tarea contiene la información necesaria para ejecutar offline.

- El formulario define el dato; Mobile define la experiencia de captura.

- La voz es el mecanismo principal de captura, no un tipo de campo del formulario.

- La captura convencional siempre debe estar disponible.

- Cada activo del dataset conserva su resultado individual.

- Una excepción sobre un activo no obliga a cancelar toda la tarea.

- La base local es una base operativa de ejecución, no una simple caché.

- STAGING es la frontera de intercambio entre Planificación/Mobile y controla sincronización.

- Las alertas son un canal transversal de comunicación del campo al Centro de Control.

- La conectividad determina cuándo se intercambia información, no si el operador puede trabajar.

# 3. Lugar dentro de GANUS

| **Componente**    | **Responsabilidad**                                                         |
|-------------------|-----------------------------------------------------------------------------|
| Planificación     | Define tarea, dataset, formulario, responsable, programación y condiciones. |
| STAGING           | Transporta paquetes hacia Mobile y recibe resultados/alertas.               |
| Mobile            | Ejecuta tareas, captura información, registra excepciones y genera alertas. |
| Inventario        | Es maestro de identidad de activos.                                         |
| Formularios       | Define campos, claves, obligatoriedad, validaciones y versión publicada.    |
| Track             | Puede aportar contexto territorial/ubicación cuando corresponda.            |
| Centro de Control | Recibe resultados consolidados y alertas para control operativo.            |

# 4. Alcance MVP

- Aplicación Android nativa.

- Base de datos local operativa.

- Operación offline-first.

- Sincronización bidireccional con STAGING.

- Recepción de paquetes de trabajo.

- Visualización de tareas y contexto.

- Ejecución por activo/unidad.

- Captura principal por voz.

- Captura convencional por selección/escritura y controles definidos.

- Reconocimiento, interpretación, normalización y validación de voz.

- Confirmación del dato cuando corresponda.

- Captura de evidencias.

- Resultado por activo.

- Cancelación/no ejecución individual con causa.

- Estados de tarea y de resultado.

- Alertas de campo siempre accesibles.

- Alerta rápida mediante video, nota de voz, foto y texto.

- Cola local de sincronización.

- Reintentos y control de estados de sincronización.

- Auditoría básica de ejecución y sincronización.

# 5. Fuera de alcance MVP

- Creación o edición de tareas desde Mobile.

- Reasignación autónoma de tareas.

- Edición de formularios publicados desde Mobile.

- Modificación del dataset objetivo.

- Planificación de jornadas.

- Optimización automática de rutas.

- Agentes autónomos que decidan acciones.

- Analítica estratégica dentro de Mobile.

- Dependencia obligatoria de conectividad para ejecutar una tarea previamente descargada.

# 6. Arquitectura funcional de Mobile

El flujo principal es:

PLANIFICACIÓN → STAGING → PAQUETE DE TRABAJO → BASE LOCAL MOBILE → EJECUCIÓN → RESULTADOS/EVIDENCIAS → COLA DE SINCRONIZACIÓN → STAGING → PLANIFICACIÓN

Flujo excepcional:

MOBILE → ALERTA → BASE LOCAL → STAGING PRIORITARIO → CENTRO DE ALERTAS

# 7. Paquete de trabajo

Una tarea disponible para Mobile debe llegar como un paquete operativo suficientemente completo para ejecutarse sin conectividad.

| **Contenido**            | **Descripción**                                                |
|--------------------------|----------------------------------------------------------------|
| TaskID                   | Identidad única de la tarea.                                   |
| FormularioID             | Identidad del formulario.                                      |
| Versión                  | Versión publicada que debe ejecutarse.                         |
| Dataset                  | Población objetivo.                                            |
| Contexto                 | Finca, proceso, actividad, espacio y demás contexto aplicable. |
| Responsable              | Usuario/crew que ejecutará.                                    |
| Programación             | Fecha, ventana y prioridad.                                    |
| Campos                   | Definición necesaria para presentación/captura.                |
| Obligatoriedad           | Condiciones de campos obligatorios.                            |
| Validaciones             | Rangos, catálogos, formatos y dependencias.                    |
| Precarga                 | Datos conocidos que llegan al dispositivo.                     |
| Condiciones de ejecución | Reglas necesarias para ejecutar/cerrar.                        |
| Metadatos                | Trazabilidad y control de versión/sincronización.              |

La tarea debe conservar la versión exacta del formulario que Planificación confirmó.

# 8. Base de datos local

La base local mantiene el estado operativo del dispositivo.

- Tareas recibidas.

- Formularios y versiones requeridas.

- Dataset.

- Contexto y datos precargados.

- Resultados por activo.

- Estados de ejecución.

- Evidencias y referencias a archivos.

- Alertas.

- Cola de sincronización.

- Estados de envío y confirmación.

- Metadatos de auditoría.

La base local debe permitir continuar una tarea después de perder conectividad, apagar/reiniciar el dispositivo o salir temporalmente de la aplicación, preservando el estado local confirmado.

# 9. Ejecución de una tarea

1.  Mobile identifica tareas disponibles para el usuario.

2.  El operador abre una tarea.

3.  Mobile presenta resumen, objetivo, prioridad, tiempo y contexto.

4.  Se muestra la población/dataset según la tarea.

5.  El operador selecciona un activo/unidad.

6.  Mobile carga la definición del formulario y datos precargados.

7.  El operador captura principalmente mediante voz o, si lo prefiere/necesita, mediante controles convencionales.

8.  Mobile interpreta y valida la captura.

9.  El operador confirma/corrige cuando corresponda.

10. Mobile guarda el resultado localmente.

11. El proceso continúa con el siguiente activo.

12. Al completar la jornada o recuperar conectividad, Mobile sincroniza con STAGING.

# 10. Voz como mecanismo principal de captura

La voz es la interacción primaria para diligenciar campos en condiciones de campo. No es un campo ni una propiedad del formulario.

La lógica es:

VOZ → RECONOCIMIENTO → INTERPRETACIÓN CONTEXTUAL → NORMALIZACIÓN → VALIDACIÓN → CONFIRMACIÓN → VALOR ESTRUCTURADO

El reconocimiento debe utilizar el contexto del formulario y del campo para interpretar expresiones naturales.

| **Situación hablada**               | **Resultado esperado**                         |
|-------------------------------------|------------------------------------------------|
| “Cuatrocientos treinta y dos kilos” | 432 en campo decimal de peso.                  |
| “Hembra”                            | Valor correspondiente del catálogo de sexo.    |
| “Tres punto cinco”                  | 3.5 en campo numérico.                         |
| “Preñada”                           | Valor correspondiente del catálogo.            |
| Observación libre                   | Texto estructurado en el campo de observación. |

La implementación debe contemplar terminología ganadera, números, unidades, nombres y expresiones propias de la operación.

# 11. Calidad de reconocimiento de voz

La calidad de interpretación es una capacidad funcional crítica del producto. GANUS no debe convertir automáticamente una interpretación dudosa en un dato confirmado.

Flujo:

VOZ → RECONOCER → INTERPRETAR → VALIDAR → ¿CONFIABLE? → CONFIRMAR / REPETIR / CORREGIR

- Si el valor es claramente interpretable y válido, puede proponerse para confirmación.

- Si no cumple una validación, debe solicitar corrección/repetición.

- Si la interpretación es ambigua, debe mostrar lo entendido y permitir repetir.

- El operador debe poder corregir mediante voz o captura convencional.

- La validación final debe utilizar las condiciones recibidas con la tarea/formulario.

- El sistema debe conservar trazabilidad suficiente para diagnosticar fallas de captura cuando sea necesario.

# 12. Captura convencional

La voz es principal, pero nunca exclusiva.

- Selección única.

- Selección múltiple.

- Escritura de texto.

- Entrada numérica.

- Fecha/fecha-hora.

- Controles de firma.

- Captura de foto/video.

- Otros controles definidos por la versión del formulario.

La UI puede adaptarse al tipo de campo, pero no puede alterar la definición funcional recibida.

# 13. Validación y obligatoriedad

Mobile aplica las condiciones recibidas en el paquete de tarea.

- Campos obligatorios.

- Validaciones de rango.

- Formatos.

- Catálogos.

- Dependencias.

- Condiciones de evidencia.

- Condiciones de cierre.

Un campo obligatorio puede llegar precargado desde Inventario, Planificación o contexto; obligatoriedad significa que debe existir un valor válido, no necesariamente que el operador tenga que escribirlo.

# 14. Resultado por activo

La unidad mínima de control de calidad es el resultado asociado al activo/unidad del dataset.

| **Estado**                   | **Significado**                           |
|------------------------------|-------------------------------------------|
| Pendiente                    | No iniciado.                              |
| En ejecución                 | Activo actualmente en proceso.            |
| Ejecutado exitosamente       | Captura válida y completa.                |
| Ejecutado con inconsistencia | Existe resultado, pero requiere revisión. |
| No ejecutado                 | No se logró realizar/capturar.            |
| Cancelado                    | Se canceló la ejecución sobre ese activo. |

Una tarea masiva puede finalizar con resultados diferentes por activo.

# 15. Cancelación individual y excepciones

Mobile debe permitir que una tarea continúe aunque uno o varios activos no puedan ser atendidos.

- Activo no encontrado.

- Animal/activo no apto.

- Animal enfermo o lesionado.

- Activo fuera del área.

- Equipo/insumo no disponible.

- Riesgo operativo.

- Otra causa.

Según la condición recibida, puede solicitarse observación o evidencia. La causa debe quedar asociada al resultado individual.

# 16. Evidencias

Mobile debe permitir capturar las evidencias definidas por el formulario o requeridas por la situación.

- Foto.

- Video.

- Documento cuando aplique.

- Nota de voz.

- Firma.

- Observación escrita.

Las evidencias deben quedar vinculadas a tarea, formulario/versión y activo cuando corresponda.

# 17. Botón de ALERTA permanente

Debe existir un botón de ALERTA visible y accesible durante toda la operación, independientemente de la pantalla o tarea en la que se encuentre el operador.

Su propósito es reportar una situación que requiere atención del Centro de Control y que no necesariamente corresponde a la tarea actual.

La alerta debe ser rápida: el operador no debe diligenciar un formulario operacional largo para poder reportar una situación urgente.

# 18. Captura de una alerta

El flujo mínimo debe permitir:

| **Mecanismo** | **Uso**                                                                 |
|---------------|-------------------------------------------------------------------------|
| Video         | Mostrar directamente la situación.                                      |
| Nota de voz   | Explicar rápidamente qué está ocurriendo.                               |
| Foto          | Documentar visualmente.                                                 |
| Texto         | Agregar información cuando sea necesario.                               |
| Prioridad     | Indicar criticidad.                                                     |
| Contexto      | Identificar tarea, activo/espacio o ubicación cuando estén disponibles. |

El operador puede combinar mecanismos. La alerta debe poder enviarse con el mínimo de pasos razonable.

# 19. Alerta inmediata y conectividad

Cuando existe conectividad, una alerta debe tener prioridad de transmisión hacia STAGING para llegar rápidamente al Centro de Alertas.

Cuando no existe conectividad, la alerta se guarda localmente y se coloca en una cola prioritaria. Al recuperar conectividad, debe transmitirse antes o con prioridad respecto de datos operativos no críticos.

Estados mínimos: Pendiente de envío → Enviada → Recibida/confirmada → Atendida/gestionada en Centro de Control.

# 20. Sincronización bidireccional

| **Dirección**    | **Contenido**                                                                                           |
|------------------|---------------------------------------------------------------------------------------------------------|
| STAGING → Mobile | Tareas, formularios/versiones, dataset, contexto, condiciones, datos precargados y cambios autorizados. |
| Mobile → STAGING | Resultados, estados, capturas, evidencias, cancelaciones, excepciones y alertas.                        |

La sincronización debe soportar reintentos, idempotencia y recuperación ante interrupciones conforme al contrato de STAGING.

# 21. Estados de sincronización

- Pendiente local.

- En cola.

- Enviando.

- Enviado.

- Confirmado.

- Error temporal.

- Error que requiere atención.

El operador debe poder conocer si la información quedó guardada localmente y si fue sincronizada, sin exponer complejidad técnica innecesaria.

# 22. Pérdida de conectividad

La pérdida de conectividad no debe interrumpir una tarea descargada.

- Continuar ejecutando.

- Guardar capturas localmente.

- Guardar evidencias localmente.

- Guardar alertas localmente.

- Registrar tiempos localmente.

- Reintentar sincronización posteriormente.

- Evitar duplicación de resultados.

# 23. Tiempo y control de ejecución

Los tiempos forman parte de la información de la tarea y de la ejecución.

- Fecha/hora de inicio de tarea.

- Fecha/hora de inicio/fin por activo cuando aplique.

- Tiempo acumulado.

- Fecha/hora de pausas y reanudaciones cuando estén contempladas.

- Fecha/hora de sincronización.

- Identificación de retrasos respecto a la programación.

Mobile registra el comportamiento real; Planificación lo interpreta y consolida para indicadores.

# 24. Cambios posteriores a la descarga

La tarea ejecutable debe conservar la versión recibida. Un cambio posterior en Inventario o Formularios no debe alterar silenciosamente una tarea ya descargada.

Los cambios controlados deben seguir el mecanismo definido por Planificación/STAGING y conservar trazabilidad de versión y momento de aplicación.

Mobile no debe alterar unilateralmente la definición de la tarea.

# 25. Seguridad

- El usuario solo accede a trabajo autorizado para su contexto.

- El paquete de trabajo debe estar asociado al usuario/responsable.

- Los datos locales deben protegerse conforme a las capacidades de la plataforma Android.

- Las operaciones deben registrar usuario, fecha/hora y origen.

- Las evidencias deben mantener asociación y control de acceso.

- La pérdida del dispositivo no debe implicar acceso irrestricto a la información local.

# 26. Contribución a la inteligencia del negocio y temas transversales

| **Tema**     | **Contribución**                                                         |
|--------------|--------------------------------------------------------------------------|
| Datos        | Convierte interacción de campo en datos estructurados.                   |
| Calidad      | Valida captura y diferencia éxito, inconsistencia y no ejecución.        |
| Operación    | Registra tiempos, cantidades y comportamiento real.                      |
| Conocimiento | Entrega evidencia contextual para reglas y análisis.                     |
| Advisory     | Puede recibir señales de excepciones y situaciones.                      |
| Control      | Devuelve información para Centro de Control.                             |
| Trazabilidad | Relaciona tarea, formulario, activo, usuario, tiempo y resultado.        |
| Respuesta    | Las alertas convierten observaciones de campo en información accionable. |

El valor diferencial de Mobile está en convertir condiciones difíciles de campo en una experiencia de captura rápida, natural y controlada.

# 27. Casos de uso

| **Caso**                     | **Resultado**                                          |
|------------------------------|--------------------------------------------------------|
| Ejecutar tarea offline       | Trabajo completo sin conectividad.                     |
| Capturar por voz             | Valor estructurado validado desde lenguaje natural.    |
| Corregir voz                 | Operador repite o usa captura convencional.            |
| Capturar selección/escritura | Alternativa siempre disponible.                        |
| Completar activo             | Resultado exitoso individual.                          |
| Cancelar activo              | Causa/evidencia sin cancelar la tarea completa.        |
| Adjuntar evidencia           | Evidencia vinculada al resultado.                      |
| Reportar alerta              | Video/voz/foto/texto enviado o en cola prioritaria.    |
| Sincronizar                  | Resultados y alertas llegan a STAGING sin duplicación. |
| Reanudar                     | El operador continúa después de interrupción.          |

# 28. Criterios de aceptación MVP

13. La solución funciona como aplicación Android nativa.

14. Una tarea previamente sincronizada puede ejecutarse sin conectividad.

15. La base local conserva tareas, formularios, dataset, resultados y cola de sincronización.

16. La tarea recibida contiene la información necesaria para determinar su ejecución.

17. El operador puede capturar mediante voz como mecanismo principal.

18. El operador puede usar selección y escritura.

19. La interpretación de voz utiliza el contexto del campo/formulario.

20. Un dato ambiguo o inválido no se consolida silenciosamente.

21. El operador puede corregir una interpretación.

22. Cada activo tiene resultado individual.

23. Mobile permite cancelación/no ejecución individual con causa.

24. Las evidencias quedan asociadas al resultado.

25. El botón de ALERTA está siempre accesible.

26. Puede registrarse alerta con video y/o nota de voz y otros soportes.

27. Las alertas tienen prioridad de sincronización cuando existe conectividad.

28. Sin conectividad, la alerta queda localmente en cola.

29. Resultados y alertas regresan a STAGING.

30. Los datos no se duplican ante reintentos.

31. Se registran tiempos y estados de ejecución.

32. Mobile no puede cambiar unilateralmente el dataset o formulario de la tarea.

# 29. Consideraciones UX/UI

- La pantalla debe estar diseñada para condiciones de campo: pocos elementos, controles grandes y acciones claras.

- La voz debe estar disponible de manera natural durante la captura.

- El operador debe saber qué entendió GANUS antes de consolidar datos cuando exista riesgo de error.

- La captura convencional debe ser accesible sin abandonar el contexto.

- El avance de tarea debe ser visible.

- El estado de sincronización debe ser entendible.

- El botón de ALERTA debe ser siempre identificable y accesible.

- La alerta debe poder crearse en segundos.

- Las evidencias no deben requerir navegación compleja.

- La aplicación debe minimizar escritura manual en condiciones agrestes.

# 30. Trazabilidad

La ejecución debe poder reconstruirse mediante:

Tarea → Formulario/Versión → Dataset → Activo → Captura → Resultado → Evidencia → Usuario → Fecha/Hora → Sincronización

Para voz, cuando sea necesario para diagnóstico: interacción de voz → interpretación → valor propuesto → validación/confirmación → valor consolidado.

Para alertas: alerta → usuario → contexto → evidencia → sincronización → Centro de Alertas → tarea derivada, si aplica.

# 31. Consideraciones para desarrollo

- Android nativo es un requisito funcional/tecnológico de Mobile.

- La base local debe ser diseñada como almacenamiento operativo offline-first.

- El paquete recibido debe ser versionado y validable.

- Los contratos con STAGING deben ser idempotentes.

- La sincronización debe poder reanudarse después de fallos.

- La cola de alertas debe soportar prioridad.

- El motor de voz debe separar reconocimiento, interpretación, normalización y validación.

- La lógica de captura debe consumir la parametrización recibida y no duplicar reglas del dominio innecesariamente.

- El modelo debe soportar resultados parciales.

- Los archivos/evidencias requieren almacenamiento local temporal y sincronización controlada.

- Debe existir manejo de permisos Android para micrófono, cámara, almacenamiento y ubicación cuando aplique.

- El comportamiento de voz debe contemplar operación con y sin conectividad según las capacidades de la implementación elegida; no debe asumirse disponibilidad de nube durante una jornada offline.

- La telemetría técnica no debe convertirse en dependencia para ejecutar la tarea.

# 32. Principio de frontera funcional

| **Elemento**                | **Dueño**                                |
|-----------------------------|------------------------------------------|
| Definición de captura       | Formularios                              |
| Definición de tarea/dataset | Planificación                            |
| Paquete ejecutable          | STAGING / contrato                       |
| Interacción y ejecución     | Mobile                                   |
| Identidad del activo        | Inventario                               |
| Resultado de campo          | Ejecución Mobile asociada a tarea/activo |
| Transporte/sincronización   | STAGING                                  |
| Control de operación        | Planificación                            |
| Reglas de negocio           | Knowledge                                |
| Alertas y atención          | Mobile origina; Planificación controla   |

Regla transversal: el documento funcional define comportamiento; el modelo BD de referencia define dónde vive la información; el contrato define cómo cruza la frontera.

# 33. Resumen para desarrollo

GANUS Mobile no debe construirse como un formulario web dentro de un teléfono. Es una aplicación Android nativa de ejecución de campo, offline-first, con base local y sincronización con STAGING.

Su diferencial de interacción es la captura por voz: el operador habla y GANUS convierte esa interacción en datos estructurados, utilizando el contexto del formulario y controles de calidad para evitar errores. La captura convencional permanece disponible.

Su segundo diferencial es el canal permanente de ALERTA: desde cualquier momento del trabajo, el operador puede enviar rápidamente video, nota de voz, foto o texto al Centro de Alertas, incluso sin conectividad, quedando la alerta priorizada para sincronización.

Principio final: “El campo habla, GANUS estructura; el campo detecta, GANUS alerta; el centro controla y decide.”
