# 05 · Funcional Planificación v1.0

> **Fuente de solo lectura.** Convertido a Markdown desde `05_FUNCIONAL_PLANIFICACION_GANUS_v1.0.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

Centro de Control de la Operación de Campo · Documento funcional MVP v1.0

**Documento preparado para diseño funcional, UX/UI y desarrollo. Complementa 00_CEREBRO_GANUS_MVP, la Arquitectura Oficial y los documentos funcionales de Organización, Inventario y Formularios.**

# 1. Propósito

Planificación es el Centro de Control de la Operación de Campo de GANUS. Organiza el calendario, transforma necesidades operativas en tareas ejecutables, valida su viabilidad antes de confirmarlas, despacha el trabajo mediante STAGING, recibe los resultados del campo, controla avance, tiempos, retrasos, capacidad y calidad de captura, y gestiona las alertas detectadas durante la ejecución.

La unidad operacional es la TAREA. Una tarea puede ser unitaria o masiva y se ejecuta sobre un dataset definido mediante criterios, utilizando un formulario publicado y asignándose a un operador o supervisor.

Frase rectora: “Planificación organiza el trabajo, lo entrega al campo, recibe lo ejecutado y permite controlar y reaccionar ante lo que ocurre en la operación.”

# 2. Lugar dentro de GANUS

| **Módulo**    | **Responsabilidad**                                                 | **Relación con Planificación**                                           |
|---------------|---------------------------------------------------------------------|--------------------------------------------------------------------------|
| Organización  | Contexto, usuarios, roles, permisos, procesos y espacios.           | Aporta contexto, responsables y segmentación.                            |
| Inventario    | Identidad, atributos, relaciones, ubicación e historial de activos. | Aporta población y claves de búsqueda.                                   |
| Formularios   | Define qué y cómo se captura; versiona y publica.                   | Planificación consume formularios publicados.                            |
| Planificación | Orquesta el trabajo operativo.                                      | Responsable de tareas, calendario, despacho y control.                   |
| STAGING       | Frontera de intercambio y sincronización.                           | Recibe paquetes de tareas y resultados.                                  |
| Mobile        | Ejecución y captura en campo.                                       | Consume trabajo y retorna resultados.                                    |
| Track         | Posición, territorio y eventos espaciales.                          | Puede aportar contexto/ubicación cuando corresponda.                     |
| Knowledge     | Reglas, condiciones e indicadores.                                  | Puede aportar criterios y lectura de resultados.                         |
| Advisory      | Interpretación y recomendaciones.                                   | Puede interpretar desempeño y excepciones.                               |
| Analytics     | Explotación y visualización.                                        | Puede profundizar indicadores; Planificación mantiene control operativo. |

# 3. Alcance MVP

- Centro de Control con indicadores operativos visibles y navegables.

- Calendario operativo como puerta de entrada principal.

- Creación de tareas unitarias y masivas.

- Selección de formulario publicado y versión.

- Acceso rápido al Constructor de Formularios para consultar o generar una nueva versión cuando sea necesario.

- Definición de criterios y generación del dataset.

- Vista de resumen y validación previa a confirmar/publicar.

- Asignación a operador o supervisor.

- Estimación básica de duración, disponibilidad y sobrecarga.

- Generación del paquete de trabajo hacia STAGING.

- Consulta del estado de sincronización/despacho.

- Recepción de resultados desde STAGING.

- Control de ejecución por tarea y por activo.

- Captura exitosa, inconsistencia, no ejecución y cancelación individual.

- Registro de causa y evidencia cuando una ejecución por activo se cancela/no ejecuta.

- Indicadores de avance, cumplimiento, retrasos, tiempos, cantidades, calidad de captura y carga de operadores.

- Panel siempre visible de Alertas de Campo.

- Conversión de una alerta en nueva tarea.

- Historial y trazabilidad completa.

# 4. Fuera de alcance MVP

- Optimización avanzada automática de rutas o balanceo inteligente de recursos.

- Asignación autónoma por IA.

- Agentes autónomos que creen y publiquen tareas sin aprobación humana.

- Constructor libre de dashboards estratégicos.

- Motor avanzado de pronóstico de productividad.

- Replanificación automática masiva ante cualquier cambio de inventario.

- Edición del formulario publicado desde la tarea; los cambios generan nueva versión.

- Que Mobile modifique el dataset objetivo de la tarea.

- Que Planificación se convierta en maestro de identidad o atributos de activos.

# 5. Actores

| **Actor**     | **Responsabilidad**                                                                                           |
|---------------|---------------------------------------------------------------------------------------------------------------|
| Administrador | Configura y controla la operación; puede crear, validar, publicar, revisar y gestionar tareas según permisos. |
| Supervisor    | Planifica, asigna, controla ejecución, atiende alertas y genera tareas de respuesta.                          |
| Operador      | Recibe y ejecuta tareas en Mobile; captura resultados y reporta excepciones/alertas.                          |
| GANUS         | Calcula dataset, valida reglas, consolida resultados y presenta indicadores.                                  |

# 6. Conceptos del dominio

| **Concepto**         | **Definición**                                                                        |
|----------------------|---------------------------------------------------------------------------------------|
| Calendario operativo | Vista temporal de tareas y carga de la operación.                                     |
| Tarea                | Unidad de trabajo planificada y trazable.                                             |
| Tarea unitaria       | Tarea aplicada a un activo o unidad específica.                                       |
| Tarea masiva         | Tarea aplicada a una población resultante de criterios.                               |
| Formulario           | Parametrización publicada que define la información a capturar.                       |
| Dataset              | Conjunto de activos/unidades objetivo resultante de criterios de selección.           |
| Criterio             | Condición utilizada para construir el dataset.                                        |
| Clave                | Dimensión habilitada por Formularios para búsqueda/segmentación; no es un filtro.     |
| Filtro               | Condición concreta aplicada sobre una dimensión maestra.                              |
| Paquete de trabajo   | Información que STAGING entrega a Mobile para ejecutar una tarea.                     |
| Resultado por activo | Estado y datos de ejecución asociados a un elemento del dataset.                      |
| Captura exitosa      | Resultado que satisface las condiciones necesarias para considerar válida la captura. |
| Excepción            | Situación que impide o altera la ejecución esperada.                                  |
| Alerta de campo      | Situación extraordinaria reportada desde Mobile para atención del centro de control.  |

# 7. Modelo funcional de extremo a extremo

El ciclo funcional obligatorio es:

CALENDARIO → DEFINIR TAREA → FORMULARIO → CRITERIOS → DATASET → VALIDAR → CONFIRMAR/PUBLICAR → STAGING → MOBILE → EJECUTAR → STAGING → CONTROLAR → CERRAR

Ciclo reactivo: ALERTA DE CAMPO → CENTRO DE CONTROL → EVALUAR → CREAR TAREA → EJECUTAR → CONTROLAR.

# 8. Centro de Control

Es la pantalla principal de Planificación. Debe permitir entender el estado general de la operación sin abrir cada tarea.

Los indicadores son puertas de navegación, no elementos decorativos.

| **Indicador**   | **Qué controla**                                  | **Navegación**                      |
|-----------------|---------------------------------------------------|-------------------------------------|
| Tareas          | Cantidad por estado/periodo.                      | Listado filtrado.                   |
| Avance          | Ejecutado frente a objetivo.                      | Tareas y detalle por activo.        |
| Cumplimiento    | Tareas ejecutadas/cerradas frente a plan.         | Tareas con desviaciones.            |
| Retrasos        | Tareas fuera de tiempo.                           | Tareas atrasadas y causas.          |
| Captura exitosa | Resultados válidos frente a resultados esperados. | Activos con fallas/inconsistencias. |
| Tiempo          | Estimado frente a real.                           | Detalle de tareas y operadores.     |
| Cantidad        | Objetivo, ejecutado, pendiente.                   | Dataset y resultados.               |
| Sobrecarga      | Carga frente a disponibilidad.                    | Operadores afectados.               |
| Alertas         | Situaciones abiertas por prioridad.               | Bandeja de alertas.                 |

El centro debe soportar filtros por periodo, proceso, tipo de actividad, espacio, responsable, prioridad y estado cuando estén disponibles en el contexto.

# 9. Calendario operativo

El calendario es la base visual de Planificación. Debe permitir identificar qué trabajo está programado, quién lo ejecuta, cuándo, dónde y con qué prioridad.

- Vistas mínima: día, semana y lista.

- Identificación visual por estado/prioridad.

- Acceso directo al detalle de tarea.

- Acción para crear tarea.

- Acceso a alertas abiertas.

- Consulta de carga del responsable.

- Filtros por proceso, actividad, responsable, espacio, prioridad y estado.

- El calendario no reemplaza el detalle ni el control analítico.

# 10. Definición de una tarea

La definición debe guiar al usuario de forma estructurada, evitando crear una tarea incompleta.

1.  Seleccionar proceso y tipo de actividad.

2.  Seleccionar formulario publicado y versión.

3.  Revisar formulario, probarlo y, si se requiere cambiarlo, acceder rápidamente al Constructor de Formularios.

4.  Definir criterios de selección cuando la tarea sea masiva.

5.  Generar y revisar dataset.

6.  Definir contexto territorial cuando aplique.

7.  Asignar operador o supervisor.

8.  Definir fecha/periodo, prioridad y recurrencia si corresponde.

9.  Calcular carga estimada y disponibilidad.

10. Ejecutar validación previa.

11. Revisar resumen.

12. Confirmar y publicar.

# 11. Acceso rápido al Constructor de Formularios

Desde la definición de una tarea debe existir acceso directo a la definición del formulario.

- Ver formulario: consulta de la versión seleccionada.

- Probar formulario: muestra la experiencia que recibiría el operador.

- Editar formulario: si la versión está publicada, abre el flujo para crear una nueva versión; no modifica la versión histórica.

- Al publicar una nueva versión, la tarea en construcción puede seleccionarla explícitamente.

- Las tareas ya publicadas conservan la versión con la que fueron creadas.

# 12. Selección y construcción del dataset

El dataset representa sobre quién o sobre qué se ejecutará la tarea.

- Las dimensiones disponibles provienen de las claves de búsqueda habilitadas por Formularios y de las estructuras maestras de Inventario/Organización.

- Planificación aplica filtros concretos sobre esas dimensiones.

- Se pueden combinar varios criterios.

- El resultado debe mostrar cantidad y detalle.

- El usuario debe poder revisar incluidos y, cuando sea pertinente, excluidos/inconsistentes.

- El dataset debe quedar asociado a la tarea y ser trazable.

- Mobile no redefine la población objetivo.

Regla: CLAVE ≠ FILTRO. La clave define una dimensión disponible; el filtro define el valor/condición utilizada para construir la población.

# 13. Validación previa a confirmar

Antes de confirmar/publicar, GANUS debe mostrar un resumen comprensible para que administrador o supervisor sepan exactamente qué se va a realizar y cómo.

| **Bloque**         | **Información mínima**                                                |
|--------------------|-----------------------------------------------------------------------|
| Qué                | Proceso, actividad, formulario y versión.                             |
| Cuándo             | Fecha/periodo, prioridad y recurrencia si aplica.                     |
| Quién              | Operador/supervisor responsable.                                      |
| Dónde              | Finca, proceso y espacio/contexto cuando aplique.                     |
| Sobre qué          | Dataset, cantidad objetivo, criterios y población.                    |
| Cómo               | Campos, obligatoriedad, validaciones, evidencias y datos precargados. |
| Capacidad          | Duración estimada, disponibilidad y carga existente.                  |
| Calidad            | Inconsistencias, faltantes o condiciones que impidan publicar.        |
| Resultado esperado | Cantidad, tiempos y condiciones de cierre.                            |

La acción final debe ser inequívoca: CONFIRMAR Y PUBLICAR TAREA.

# 14. Validaciones de capacidad y sobrecarga

GANUS debe advertir cuando la carga asignada excede o se acerca a la disponibilidad conocida del responsable.

- Comparar duración estimada de la nueva tarea con carga existente.

- Mostrar horas estimadas y disponibilidad cuando el dato exista.

- Clasificar al menos: Normal, Alta carga, Sobrecarga.

- No bloquear automáticamente por sobrecarga en MVP; la decisión final corresponde al administrador/supervisor.

- Registrar la condición de carga como parte del contexto de control.

# 15. Estados de la tarea

Estados MVP:

Borrador → Validada → Programada → Disponible para campo → En ejecución → Pausada → Finalizada → Por validar → Cerrada

Salida alternativa: Cancelada.

No se incluyen como estados MVP: Aceptada ni En desplazamiento.

| **Estado**            | **Significado**                          |
|-----------------------|------------------------------------------|
| Borrador              | Tarea en construcción.                   |
| Validada              | Superó la revisión previa.               |
| Programada            | Tiene programación confirmada.           |
| Disponible para campo | Paquete preparado/entregable.            |
| En ejecución          | Existe actividad de campo.               |
| Pausada               | Ejecución suspendida temporalmente.      |
| Finalizada            | Terminó la ejecución operativa.          |
| Por validar           | Resultados disponibles para control.     |
| Cerrada               | Resultados revisados y ciclo concluido.  |
| Cancelada             | Tarea anulada con trazabilidad de causa. |

# 16. STAGING y despacho

STAGING es la frontera de intercambio. Planificación no entrega directamente el trabajo a Mobile.

Al publicar, Planificación debe generar el paquete operativo con, como mínimo:

- Identidad única de tarea.

- Formulario y versión publicada.

- Dataset/población objetivo.

- Contexto de finca/proceso/actividad/espacio.

- Responsable.

- Fechas y prioridad.

- Campos/condiciones de obligatoriedad.

- Validaciones y valores precargados aplicables.

- Información contextual necesaria para ejecución offline.

- Metadatos de trazabilidad.

STAGING controla recepción, disponibilidad, sincronización, idempotencia, reintentos y estado de intercambio conforme a la arquitectura oficial.

# 17. Ejecución Mobile y resultado por activo

Mobile recibe el paquete de trabajo y ejecuta la tarea. No administra Planificación ni redefine la población.

Para cada elemento del dataset debe existir un estado de resultado.

| **Resultado**                | **Significado**                                      |
|------------------------------|------------------------------------------------------|
| Pendiente                    | Aún no ejecutado.                                    |
| Ejecutado exitosamente       | Captura válida y completa según condiciones.         |
| Ejecutado con inconsistencia | Hubo ejecución, pero el resultado requiere revisión. |
| No ejecutado                 | No se produjo ejecución válida.                      |
| Cancelado                    | La ejecución se canceló para ese activo/unidad.      |

La captura exitosa es una métrica propia y debe distinguirse del simple avance de la tarea.

# 18. Cancelación individual en campo

Mobile debe permitir cancelar/no ejecutar uno o varios activos sin cancelar la tarea completa.

La cancelación debe registrar causa y, según las condiciones del formulario/regla, puede requerir observación o evidencia.

- Activo no encontrado.

- Condición no apta.

- Animal enfermo/lesionado.

- Activo fuera del área.

- Equipo o insumo no disponible.

- Riesgo operativo.

- Otra causa parametrizada.

Una tarea masiva puede cerrar con resultados mixtos. El sistema debe consolidar el resultado sin perder el detalle individual.

# 19. Retorno de resultados

Los resultados regresan desde Mobile a STAGING y desde allí quedan disponibles para consulta y control en Planificación.

Cada resultado debe conservar como mínimo: tarea, formulario/versión, activo cuando aplique, usuario, fecha/hora, valores, evidencias, estado de captura y trazabilidad de sincronización.

Planificación no se convierte en maestro de los datos de Inventario; consulta resultados para control operativo.

# 20. Control de la tarea

Al seleccionar una tarea ya ejecutada, Planificación debe permitir entender comportamiento y resultado.

| **Dimensión**  | **Ejemplos**                                           |
|----------------|--------------------------------------------------------|
| Cantidad       | Objetivo, ejecutada, pendiente, cancelada.             |
| Avance         | Porcentaje ejecutado.                                  |
| Captura        | Exitosa, inconsistente, no exitosa.                    |
| Tiempo         | Estimado, real, diferencia.                            |
| Cumplimiento   | En plazo, retrasada, vencida.                          |
| Productividad  | Resultado por responsable.                             |
| Excepciones    | Causas de cancelación/no ejecución.                    |
| Evidencia      | Fotos, documentos, videos, notas/firma cuando existan. |
| Sincronización | Pendiente, sincronizado, error/reintento.              |

# 21. Indicadores operativos

| **Familia**   | **Indicadores MVP**                                |
|---------------|----------------------------------------------------|
| Avance        | % ejecutado, pendientes, cantidad ejecutada.       |
| Cumplimiento  | Tareas cumplidas, atrasadas, vencidas, cerradas.   |
| Calidad       | % captura exitosa, inconsistencias, no ejecutados. |
| Tiempo        | Estimado vs real, desviación.                      |
| Cantidad      | Objetivo vs resultado.                             |
| Capacidad     | Carga por operador, disponibilidad, sobrecarga.    |
| Productividad | Resultado por operador y actividad.                |
| Alertas       | Abiertas, críticas, altas, medias y resueltas.     |

Los indicadores deben estar visibles en el Centro de Control y ser navegables hacia el nivel que explica el indicador.

# 22. Navegación de análisis

La navegación debe permitir drill-down progresivo:

INDICADOR → LISTADO → TAREA → RESULTADO POR ACTIVO → EVIDENCIA/CAUSA

Ejemplo: Retrasos 5 → tareas retrasadas → tarea 000245 → activos pendientes → causa.

# 23. Alertas de Campo

Las alertas son situaciones extraordinarias detectadas durante la operación. No son tareas ni sustituyen los formularios operacionales.

Planificación debe mantener una ventana de Alertas de Campo siempre visible en el Centro de Control, ordenada por prioridad y estado.

| **Prioridad** | **Tratamiento**       |
|---------------|-----------------------|
| Crítica       | Atención inmediata.   |
| Alta          | Atención prioritaria. |
| Media         | Atención programable. |
| Baja          | Seguimiento.          |

La alerta debe incluir como mínimo: tipo, prioridad, situación, descripción, contexto, activo relacionado cuando aplique, ubicación cuando exista, usuario, fecha/hora y evidencias.

# 24. Formulario estándar de alerta

La alerta utiliza un formulario estándar predefinido de GANUS, siempre disponible y suficientemente versátil. Es una excepción controlada al flujo de formularios operacionales.

- Tipo de alerta.

- Prioridad.

- Situación/descripción.

- Activo relacionado opcional.

- Contexto/espacio.

- Foto, video o nota de voz.

- Observación.

- Fecha/hora y usuario.

La alerta también puede ser registrada offline y enviada posteriormente mediante STAGING.

# 25. Conversión de alerta a tarea

Una alerta puede convertirse en una nueva necesidad operativa.

Flujo: ALERTA → REVISAR → DECIDIR → CREAR TAREA → FORMULARIO → DATASET → VALIDAR → PUBLICAR.

- La alerta permanece trazable como origen de la tarea.

- La tarea puede ser unitaria o masiva.

- El supervisor puede asignarla a otro responsable.

- La alerta no se cierra simplemente por crear la tarea; su estado debe reflejar si fue atendida, convertida, resuelta o descartada.

# 26. Recurrencia

La recurrencia pertenece a Planificación, no a Formularios. El formulario define captura; Planificación define cuándo y cuántas veces se ejecuta.

La implementación MVP debe conservar la trazabilidad de cada instancia generada por una programación recurrente.

# 27. Reglas de negocio

13. Una tarea siempre debe utilizar un formulario publicado y una versión identificable.

14. Una tarea debe tener responsable antes de publicarse.

15. Una tarea masiva debe tener dataset calculado y visible antes de confirmarse.

16. El formulario no puede ser editado dentro de una tarea; los cambios generan nueva versión.

17. El dataset es parte de la trazabilidad de la tarea.

18. Mobile no puede modificar la población objetivo.

19. Cada elemento del dataset debe poder tener resultado individual.

20. Una tarea puede tener resultados mixtos por activo.

21. Cancelación individual no implica cancelación global.

22. Las causas de no ejecución/cancelación deben quedar trazables.

23. Captura exitosa es diferente de ejecución.

24. Los resultados deben retornar por STAGING.

25. Los indicadores del Centro de Control deben permitir navegación hacia los datos que los explican.

26. Las alertas son situaciones, no tareas.

27. Una alerta puede originar una tarea y debe conservarse la relación de origen.

28. Los registros importantes no se eliminan físicamente; se conserva historial.

29. Las tareas publicadas conservan su formulario/version y contexto histórico.

# 28. Datos que recibe

- Usuarios, roles y permisos desde Organización.

- Procesos, tipos de actividad y espacios desde Organización.

- Activos, atributos, identificadores y relaciones desde Inventario.

- Claves de búsqueda y formulario publicado desde Formularios.

- Reglas/condiciones disponibles desde Knowledge cuando corresponda.

- Disponibilidad/carga operativa existente de Planificación.

# 29. Datos que produce

- Tarea y su ciclo de estados.

- Dataset y criterios aplicados.

- Asignación y programación.

- Resumen de validación.

- Paquete de trabajo para STAGING.

- Estados de despacho/sincronización.

- Control de ejecución.

- Resultados agregados de tarea.

- Indicadores operativos.

- Alertas de campo y su ciclo.

- Relación alerta → tarea cuando aplique.

- Historial y trazabilidad.

# 30. Contribución a la inteligencia del negocio y temas transversales

Planificación es una fuente primaria de inteligencia operacional. No es dueña de la identidad de los activos ni de las reglas maestras, pero genera evidencia sobre cómo se comporta la operación.

| **Tema**        | **Contribución**                                                           |
|-----------------|----------------------------------------------------------------------------|
| Datos           | Produce trazabilidad de trabajo planificado y ejecutado.                   |
| Calidad         | Permite medir captura exitosa e inconsistencias.                           |
| Productividad   | Permite comparar tiempo, cantidad y resultado.                             |
| Capacidad       | Hace visible carga y disponibilidad.                                       |
| Conocimiento    | Entrega evidencia para evaluar reglas y condiciones.                       |
| Advisory        | Puede recibir señales de desviación, retraso o excepción.                  |
| Analytics       | Alimenta indicadores de operación.                                         |
| Trazabilidad    | Relaciona tarea, formulario, versión, activo, usuario, tiempo y resultado. |
| Mejora continua | Permite detectar causas recurrentes de fallas y retrasos.                  |

Principio: la información debe tener sentido operativo. Un indicador debe poder explicarse mediante sus datos y una desviación debe poder conducir al elemento que la produjo.

# 31. Casos de uso principales

| **Caso**                  | **Resultado esperado**                                        |
|---------------------------|---------------------------------------------------------------|
| Crear tarea unitaria      | Tarea validada y publicada sobre un activo.                   |
| Crear tarea masiva        | Dataset calculado, validado y publicado.                      |
| Revisar antes de publicar | Supervisor entiende qué, quién, cuándo, dónde, cómo y cuánto. |
| Cambiar formulario        | Nueva versión sin alterar histórico.                          |
| Despachar a campo         | Paquete disponible en STAGING/Mobile.                         |
| Ejecutar parcialmente     | Resultados por activo conservados.                            |
| Cancelar activo           | Causa/evidencia registrada sin cancelar toda la tarea.        |
| Cerrar tarea              | Resultados revisados y consolidados.                          |
| Analizar retraso          | Navegación hasta tarea y causa.                               |
| Analizar baja captura     | Navegación hasta activos/resultados.                          |
| Detectar sobrecarga       | Visualización y advertencia al asignar.                       |
| Reportar alerta           | Situación llega al Centro de Control.                         |
| Convertir alerta en tarea | Nueva tarea trazable desde la alerta.                         |

# 32. Criterios de aceptación MVP

30. El usuario puede acceder a Planificación mediante un Centro de Control.

31. El calendario permite visualizar y navegar tareas.

32. Puede crear tareas unitarias y masivas.

33. Puede seleccionar un formulario publicado y su versión.

34. Puede acceder al formulario desde la definición de la tarea.

35. Puede generar y revisar el dataset antes de confirmar.

36. Puede ver un resumen previo con configuración, población, obligatoriedad y capacidad.

37. El sistema advierte sobre inconsistencias y sobrecarga.

38. Puede confirmar/publicar la tarea.

39. La tarea se entrega a STAGING con el paquete requerido.

40. Mobile recibe el trabajo sin redefinir población.

41. Cada activo puede tener resultado individual.

42. Mobile permite marcar ejecución exitosa, inconsistente, no ejecutada o cancelada.

43. La cancelación puede registrar causa/evidencia.

44. Resultados retornan por STAGING.

45. Planificación muestra comportamiento real de la tarea.

46. Indicadores muestran avance, retrasos, tiempos, cantidades y calidad de captura.

47. Los indicadores permiten navegar hacia su detalle.

48. Las alertas de campo son visibles permanentemente en el Centro de Control.

49. Una alerta puede convertirse en tarea.

50. Existe trazabilidad completa del ciclo.

# 33. Consideraciones UX/UI

- El calendario debe ser comprensible en pocos segundos.

- El Centro de Control debe priorizar excepciones y desviaciones sobre información ornamental.

- Los indicadores deben ser accionables y navegables.

- La creación de tarea debe seguir un flujo progresivo: definir → dataset → validar → confirmar.

- El resumen previo debe ser visual y ejecutivo, no una pantalla técnica.

- Las alertas deben permanecer visibles sin ocultar el trabajo principal.

- Mobile y Planificación deben compartir el mismo significado de estados y resultados.

- El detalle por activo debe ser accesible sin perder el contexto de la tarea.

# 34. Trazabilidad

Toda tarea debe poder reconstruirse históricamente.

Cadena mínima:

Tarea → Formulario/Versión → Criterios → Dataset → Responsable → Programación → STAGING → Mobile → Resultado por activo → Evidencia → Control → Cierre

Para alertas: Alerta → origen/usuario/contexto → decisión → Tarea relacionada → resultado.

# 35. Consideraciones para desarrollo

- Planificación debe ser un dominio funcional propio.

- No acceder directamente a tablas/repositorios de otros dominios; utilizar contratos definidos.

- El intercambio con Mobile se realiza por STAGING conforme a la arquitectura oficial.

- Las versiones publicadas de formularios son inmutables.

- Los estados deben implementarse como máquina de estados explícita.

- Los resultados por activo deben ser trazables e idempotentes durante sincronización.

- Los indicadores operativos deben derivarse de datos transaccionales/controlables y no convertirse en una segunda fuente de verdad.

- Las reglas de negocio que pertenecen a Knowledge no deben codificarse como reglas rígidas dentro de Planificación salvo las necesarias para integridad transaccional.

- El modelo debe soportar operación offline y reintentos.

- Debe existir auditoría de creación, validación, publicación, asignación, cambios de estado, cancelaciones y cierre.

- Debe ser posible consultar el detalle que explica cualquier indicador del Centro de Control.

# 36. Principio de frontera funcional

| **Elemento**                 | **Dueño**                                        |
|------------------------------|--------------------------------------------------|
| Identidad del activo         | Inventario                                       |
| Definición del formulario    | Formularios                                      |
| Dataset de una tarea         | Planificación                                    |
| Calendario y programación    | Planificación                                    |
| Paquete de intercambio       | STAGING                                          |
| Ejecución y captura          | Mobile                                           |
| Resultado de ejecución       | Resultado operativo asociado a la tarea/activo   |
| Territorio/posición          | Track                                            |
| Reglas de negocio            | Knowledge                                        |
| Interpretación/recomendación | Advisory                                         |
| Visualización analítica      | Analytics / Planificación para control operativo |

Regla transversal: “El documento funcional define el comportamiento; el modelo BD de referencia define dónde vive la información; el contrato define cómo cruza la frontera.”

# 37. Resumen ejecutivo para desarrollo

GANUS Planificación no debe implementarse como un CRUD de tareas. Debe implementarse como un Centro de Control operacional.

El núcleo es:

CALENDARIO → TAREA → FORMULARIO → DATASET → VALIDACIÓN → CONFIRMACIÓN → STAGING → MOBILE → RESULTADO POR ACTIVO → CONTROL → ALERTAS → NUEVA TAREA

La calidad de la operación se mide no solo por si una tarea terminó, sino por qué cantidad de activos fue atendida, con qué calidad de captura, en cuánto tiempo, con qué desviaciones y qué excepciones aparecieron.

El objetivo del MVP es entregar una operación de campo controlada, trazable y medible, sin introducir complejidad de optimización o IA autónoma que no sea necesaria para lograrlo.
