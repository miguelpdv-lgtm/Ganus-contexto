# 04 · Funcional Formularios Dinámicos v1.3

> **Fuente de solo lectura.** Convertido a Markdown desde `04_FUNCIONAL_FORMULARIOS_DINAMICOS_GANUS_v1.3.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**Especificación funcional · Versión 1.3**

Documento funcional orientado a producto, desarrollo y pruebas. Esta versión consolida la definición de Formularios acordada y elimina responsabilidades que pertenecen a Inventario, Organización, Planificación o Mobile.

Principio rector: el documento funcional define el comportamiento; el modelo BD de referencia define dónde vive la información; el contrato define cómo cruza la frontera.

# 1. Propósito

Formularios Dinámicos es el componente de GANUS que define, parametriza, versiona y publica el contenido inequívoco que debe capturarse durante una actividad. El formulario es una parametrización almacenada en base de datos; no es una pantalla ni una población de activos.

Su función es definir: las claves de búsqueda que pueden utilizarse para seleccionar población, las claves de contexto que acompañan y explican la captura, los filtros disponibles derivados de maestros y jerarquías existentes, y los campos concretos que deben capturarse, con su tipo, orden, obligatoriedad y validaciones.

Planificación utiliza esta parametrización para configurar una tarea y construir su dataset. Mobile ejecuta el mismo formulario sobre ese dataset y devuelve resultados trazables.

# 2. Principio de simplicidad funcional

Formularios debe ser potente por la calidad de su parametrización, no por la cantidad de funcionalidades que incorpora. No debe convertirse en un motor de planificación, un administrador de maestros, un motor de ejecución ni un sistema de reglas de negocio.

| **Formularios sí hace**                               | **Formularios no hace**                       |
|-------------------------------------------------------|-----------------------------------------------|
| Define claves de búsqueda y contexto.                 | No administra activos.                        |
| Define filtros disponibles sobre maestros/jerarquías. | No decide la población concreta de una tarea. |
| Define campos, tipo, orden y obligatoriedad.          | No programa ni asigna trabajo.                |
| Define validaciones de los campos.                    | No administra agenda/recurrencia.             |
| Versiona y publica.                                   | No administra procesos o espacios maestros.   |
| Previsualiza la parametrización.                      | No ejecuta reglas de Knowledge.               |
| Entrega la definición a Planificación/Mobile.         | No define métodos tecnológicos de captura.    |
| Mantiene la estructura de captura.                    | No mantiene un dataset de población.          |

# 3. Lugar dentro de GANUS

| **Módulo**    | **Responsabilidad**                          | **Relación**                                                                                                 |
|---------------|----------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| Organización  | Estructura funcional de la finca             | Define procesos, tipos de actividad y segmentación física que pueden actuar como contexto.                   |
| Inventario    | Identidad, estructura y atributos de activos | Define las claves disponibles dentro de las estructuras de activos y sus aptitudes de búsqueda/segmentación. |
| Formularios   | Definición de captura                        | Selecciona claves, define contexto, filtros disponibles y campos.                                            |
| Planificación | Trabajo y población                          | Aplica criterios concretos y construye el dataset de la tarea.                                               |
| Mobile        | Ejecución                                    | Consume formulario + dataset + contexto y captura resultados.                                                |
| Knowledge     | Reglas y conocimiento                        | Interpreta resultados; no administra Formularios.                                                            |
| Analytics     | Visualización                                | Consume resultados y dimensiones.                                                                            |
| Advisory      | Interpretación/recomendación                 | Utiliza datos, contexto y resultados.                                                                        |

# 4. Alcance MVP

- Crear y administrar plantillas reutilizables.

- Definir claves de búsqueda a partir de claves disponibles en las estructuras de activos.

- Definir claves de contexto a partir de dimensiones funcionales de Organización.

- Definir filtros disponibles mediante campos maestros y jerarquías existentes.

- Construir campos uno a uno.

- Definir tipo, orden, obligatoriedad, valor inicial/precargado y validaciones por campo.

- Configurar listas y opciones cuando el campo lo requiera.

- Permitir dependencias sencillas entre campos.

- Permitir obligatoriedad condicional cuando sea necesaria.

- Previsualizar el formulario.

- Versionar y publicar.

- Conservar historial de versiones.

- Entregar la definición publicada a Planificación y Mobile.

# 5. Fuera de alcance

- Crear o administrar activos y sus atributos: Inventario.

- Crear maestros de procesos, actividades o espacios: Organización.

- Construir la población de una tarea: Planificación.

- Programar recurrencia: Planificación.

- Asignar recursos: Planificación.

- Ejecutar la actividad: Mobile.

- Definir tecnologías o métodos de captura por campo: Mobile/ejecución.

- Calcular indicadores: Knowledge/Analytics.

- Ejecutar reglas de negocio: Knowledge.

- Generar recomendaciones: Advisory.

- Crear formularios distintos para Web y Mobile.

- Crear copias de maestros dentro del formulario.

# 6. Conceptos fundamentales

## 6.1 Formulario

Parametrización versionada de la información que debe capturarse. Contiene claves, contexto, filtros disponibles y campos.

## 6.2 Clave de búsqueda

Una dimensión disponible dentro de la estructura de un activo en Inventario, habilitada para localizar, segmentar o seleccionar población. Puede ser identificador o atributo estructurado: RFID, ID GANUS, especie, raza, sexo, edad, estado, categoría, peso u otros.

La clave de búsqueda no es un campo de captura. Es una dimensión utilizada para construir la población sobre la cual se aplicará el formulario.

## 6.3 Clave de contexto

Dimensión que contextualiza la captura y sus resultados. En el modelo definido para GANUS proviene principalmente de Organización y comprende proceso, tipo de actividad y segmentación física.

La segmentación física utiliza la estructura definida por Organización: tipo de espacio, espacio y relación padre/hijo. Ejemplos: finca → sector → potrero, finca → lote → potrero, finca → corral, según la estructura elegida por cada finca.

## 6.4 Filtro

Condición concreta utilizada para seleccionar población. Los filtros se construyen sobre campos maestros o jerarquías ya definidas en GANUS. El formulario declara las dimensiones que pueden utilizarse; Planificación establece los valores y operadores concretos.

## 6.5 Campo

Unidad de información que forma parte del resultado que se captura. Los campos sí son parte del contenido del formulario y tienen definición individual.

## 6.6 Dataset

Conjunto de registros/activos resultante de la configuración de una tarea en Planificación. El dataset no pertenece al formulario.

## 6.7 Resultado

Información capturada durante la ejecución del formulario, asociada a tarea, formulario/versión, activo cuando corresponda, usuario, fecha/hora y contexto.

# 7. Estructura funcional del formulario

El formulario tiene tres componentes funcionales principales y un estado de publicación:

| **Componente**      | **Qué contiene**                                                                                    |
|---------------------|-----------------------------------------------------------------------------------------------------|
| Claves              | Claves de búsqueda y claves de contexto.                                                            |
| Filtros disponibles | Dimensiones maestras y jerarquías sobre las cuales Planificación puede configurar criterios.        |
| Campos              | Definición uno a uno: nombre, tipo, orden, obligatoriedad, valor inicial/precargado y validaciones. |
| Publicación         | Borrador, Publicado, Obsoleto y versiones históricas.                                               |

No se agrega un componente de 'métodos de captura'. Tampoco se agregan como tipos de campo 'activo relacionado' o 'ubicación'. Activo y ubicación/contexto se resuelven mediante claves y contexto.

# 8. Claves de búsqueda

Formularios no crea las claves. Inventario define las estructuras de activos y determina cuáles atributos pueden ser utilizados como claves de búsqueda/segmentación.

| **Dimensión de activo** | **Ejemplos**                                                                                  |
|-------------------------|-----------------------------------------------------------------------------------------------|
| Identificación          | RFID, ID GANUS, ID oficial, código anterior.                                                  |
| Clasificación           | Especie, raza, categoría.                                                                     |
| Estado                  | Estado del activo, estado productivo/reproductivo cuando esté estructurado.                   |
| Características         | Sexo, edad, peso u otros atributos estructurados.                                             |
| Otros atributos         | Cualquier atributo publicado por Inventario con aptitud explícita para búsqueda/segmentación. |

No todo atributo es automáticamente una clave. Texto libre, notas, fotos, videos y documentos no son dimensiones de selección por defecto. La aptitud debe estar gobernada en Inventario.

La aptitud funcional no se deduce únicamente del tipo técnico del dato. Un decimal puede ser estructurado pero no necesariamente ser una dimensión adecuada para seleccionar población; Inventario determina su uso.

# 9. Claves de contexto

Las claves de contexto no son campos duplicados. Referencian dimensiones maestras ya existentes.

| **Grupo**                   | **Claves**                                                                   |
|-----------------------------|------------------------------------------------------------------------------|
| Proceso                     | Proceso definido en Organización.                                            |
| Actividad                   | Tipo de actividad definido en Organización.                                  |
| Segmentación física         | Tipo de espacio, espacio y jerarquía padre/hijo definida por la finca.       |
| Otros contextos autorizados | Dimensiones funcionales que se definan como contexto, sin duplicar maestros. |

Ejemplo: Proceso = Manejo Animal; Actividad = Vacunación; Sector = Norte; Potrero = P-03. El formulario utiliza estas dimensiones para contextualizar el resultado.

# 10. Filtros y Planificación

Los filtros no son un maestro independiente. Son condiciones construidas por Planificación utilizando las claves disponibles y los campos maestros o jerarquías existentes.

| **Clave/dimensión** | **Ejemplo de criterio**           |
|---------------------|-----------------------------------|
| Sexo                | Sexo = Hembra                     |
| Edad                | Edad \>= 24 meses                 |
| Estado              | Estado = Activo                   |
| Raza                | Raza = Brahman                    |
| Jerarquía física    | Sector = Norte AND Potrero = P-03 |

La combinación de criterios determina la población objetivo. Formularios define el universo de dimensiones que puede utilizarse; Planificación decide la selección concreta.

Regla: CLAVE ≠ FILTRO. La clave es la dimensión; el filtro es la condición aplicada sobre ella.

# 11. Campos: definición uno a uno

Cada campo del formulario debe tener una definición inequívoca. El objetivo es que el desarrollador pueda construir el renderer sin interpretar intenciones.

| **Propiedad**            | **Definición**                                                                      |
|--------------------------|-------------------------------------------------------------------------------------|
| Nombre                   | Nombre funcional del dato.                                                          |
| Tipo                     | Tipo de campo seleccionado de la lista soportada.                                   |
| Orden                    | Posición de presentación.                                                           |
| Obligatorio              | Indica si debe existir valor para completar.                                        |
| Valor inicial/precargado | Valor aportado por contexto, Inventario, tarea o configuración, cuando corresponda. |
| Validaciones             | Reglas de calidad aplicables al tipo de dato.                                       |
| Ayuda                    | Orientación opcional para el usuario.                                               |

# 12. Lista de campos disponibles

Los siguientes son tipos de información que pueden formar parte del contenido del formulario. No son métodos de captura.

| **Tipo de campo**  | **Ejemplo / propósito**                       |
|--------------------|-----------------------------------------------|
| Texto corto        | Nombre, código, identificación textual.       |
| Texto largo        | Descripción, observación o nota escrita.      |
| Número entero      | Edad, cantidad, conteo.                       |
| Decimal            | Peso, temperatura, medición.                  |
| Fecha              | Fecha de nacimiento, fecha de aplicación.     |
| Fecha y hora       | Momento de un evento.                         |
| Selección única    | Sexo, estado, condición.                      |
| Selección múltiple | Síntomas, condiciones o categorías múltiples. |
| Sí / No            | Preguntas binarias.                           |
| Foto               | Evidencia fotográfica.                        |
| Documento          | Certificado, soporte o documento asociado.    |
| Video              | Evidencia audiovisual.                        |
| Nota de voz        | Observación o evidencia sonora hablada.       |
| Firma              | Firma del responsable o participante.         |

No se incluyen como tipos de campo: Activo relacionado, Ubicación, RFID, QR, báscula, sensor IoT, cámara, voz, API u otros mecanismos tecnológicos. Si una tecnología permite obtener un valor, ese valor se almacena según el tipo de campo correspondiente. La tecnología de interacción pertenece a la capa de ejecución.

# 13. Campos de evidencia

Foto, documento, video y nota de voz son información que el formulario puede exigir o admitir como resultado. No son métodos de captura.

- Una foto puede ser evidencia requerida cuando una condición lo determine.

- Un documento puede ser obligatorio para respaldar una actividad.

- Un video puede constituir evidencia de una condición o procedimiento.

- Una nota de voz puede conservar una observación hablada como resultado.

- Estas evidencias deben quedar asociadas al resultado y a su trazabilidad.

# 14. Orden de presentación

El orden es una propiedad simple de cada campo dentro de la versión del formulario. El formulario puede organizarse en secciones y cada campo mantiene una posición explícita.

El orden no constituye un motor independiente. Es parte de la parametrización que el renderer interpreta.

El mismo orden funcional debe respetarse en la vista previa de Formularios, en la prueba de una tarea de Planificación y en la ejecución Mobile/Web, salvo adaptaciones puramente visuales del dispositivo que no cambien la lógica ni el orden funcional.

# 15. Obligatoriedad

La obligatoriedad es una propiedad del campo.

| **Modalidad** | **Comportamiento**                                                              |
|---------------|---------------------------------------------------------------------------------|
| Obligatorio   | Debe existir un valor válido para completar el formulario.                      |
| Opcional      | Puede quedar sin valor.                                                         |
| Condicional   | Se vuelve obligatorio cuando se cumple una condición definida en el formulario. |

Visibilidad, obligatoriedad y validación son conceptos distintos. Un campo puede ser visible y opcional, visible y obligatorio, o mostrarse únicamente bajo una condición.

Un campo obligatorio no significa que el operador deba escribirlo: puede llegar precargado desde Inventario, Planificación o contexto.

# 16. Validaciones

Las validaciones garantizan calidad antes de completar el formulario. Deben ser simples y asociadas al tipo de campo o catálogo.

- Rangos numéricos.

- Formato y longitud de texto.

- Catálogos permitidos.

- Fechas válidas.

- Obligatoriedad.

- Dependencias entre campos.

- Condiciones de evidencia cuando correspondan.

Ejemplo: si '¿Presenta lesión?' = Sí, 'Tipo de lesión' puede ser visible y obligatorio y 'Foto' puede ser requerida. Si = No, esos campos pueden permanecer ocultos o no obligatorios.

# 17. Listas y catálogos

Una selección puede consumir un catálogo maestro existente o una lista local al formulario. Cuando el catálogo es maestro, el formulario lo referencia y no lo duplica. Cuando la lista es local, sus opciones pueden administrarse dentro de la configuración del formulario, con desactivación sin eliminación histórica cuando sea necesario.

Las opciones utilizadas por resultados históricos deben conservarse de acuerdo con la versión del formulario.

# 18. Previsualización, prueba y ejecución: una misma parametrización

La presentación no es un módulo funcional adicional. Es el uso de la misma parametrización del formulario en tres momentos del ciclo.

| **Momento**                  | **Qué utiliza**                                       | **Resultado**                                                   |
|------------------------------|-------------------------------------------------------|-----------------------------------------------------------------|
| Formularios – Vista previa   | Definición del formulario + datos de ejemplo          | Permite verificar contenido y orden.                            |
| Planificación – Probar tarea | Formulario + criterios + contexto + dataset calculado | Permite confirmar exactamente qué trabajo recibirá el operador. |
| Mobile/Web – Ejecución       | Formulario publicado + dataset + contexto de tarea    | Permite ejecutar y capturar resultados.                         |

La interfaz puede adaptarse físicamente al canal, pero la definición funcional es única. No debe existir una versión lógica de formulario para Web y otra para Mobile.

# 19. Parametrización en base de datos

El formulario no debe tratarse como una pantalla codificada. Su estructura debe existir como configuración persistida y versionada.

Conceptualmente:

FORMULARIO → VERSIÓN → CLAVES + CONTEXTO + FILTROS DISPONIBLES + CAMPOS + VALIDACIONES

La definición publicada es consumida por Planificación y Mobile. El renderer interpreta la parametrización.

# 20. Flujo funcional completo

1.  Usuario autorizado crea una plantilla.

2.  Selecciona las claves de búsqueda disponibles desde las estructuras publicadas por Inventario.

3.  Selecciona las claves de contexto disponibles desde Organización.

4.  Define las dimensiones maestras y jerarquías que Planificación podrá utilizar como filtros.

5.  Construye los campos uno a uno.

6.  Define tipo, orden, obligatoriedad, valores iniciales/precargados y validaciones.

7.  Configura listas/catálogos cuando corresponda.

8.  Previsualiza el formulario.

9.  Publica una versión.

10. Planificación selecciona la versión publicada.

11. Planificación configura criterios de selección y contexto.

12. El sistema calcula el dataset.

13. Planificación utiliza 'Probar tarea' para visualizar la misma definición sobre el dataset.

14. Al publicar la tarea, Mobile recibe la versión exacta del formulario, dataset y contexto.

15. Mobile ejecuta y captura.

16. El sistema valida y guarda resultados.

17. Los resultados conservan trazabilidad y quedan disponibles para los módulos posteriores.

# 21. Modelo operativo: Formulario → Dataset → Resultado

Este es el modelo central que debe comprender el desarrollador.

| **Etapa**                | **Responsable**              | **Qué produce**                                  |
|--------------------------|------------------------------|--------------------------------------------------|
| Definición               | Formularios                  | Formulario parametrizado y versionado.           |
| Selección                | Planificación                | Criterios y contexto de tarea.                   |
| Resolución               | Planificación                | Dataset objetivo.                                |
| Ejecución                | Mobile                       | Capturas y evidencias.                           |
| Resultado                | GANUS                        | Resultados trazables.                            |
| Interpretación posterior | Knowledge/Analytics/Advisory | Reglas, indicadores, análisis y recomendaciones. |

El formulario no contiene la población. La configuración de la tarea determina el dataset.

# 22. Contrato funcional hacia Mobile

- FormularioID.

- Versión publicada.

- TareaID.

- Dataset objetivo.

- Claves y contexto resueltos.

- Campos en el orden definido.

- Obligatoriedad.

- Validaciones.

- Catálogos/opciones.

- Valores precargados autorizados.

- Evidencias requeridas.

- Información necesaria para ejecución offline.

Mobile no redefine claves, filtros ni población. Mobile ejecuta el paquete recibido y devuelve resultados.

# 23. Resultado y trazabilidad

| **Elemento** | **Trazabilidad**                                            |
|--------------|-------------------------------------------------------------|
| Tarea        | Qué actividad se ejecutó.                                   |
| Formulario   | Qué definición se utilizó.                                  |
| Versión      | Qué versión exacta.                                         |
| Activo       | Sobre qué activo, cuando corresponda.                       |
| Usuario      | Quién ejecutó.                                              |
| Fecha/hora   | Cuándo ocurrió.                                             |
| Contexto     | Proceso, actividad, espacio y demás dimensiones aplicables. |
| Resultado    | Valores y evidencias capturadas.                            |

# 24. Versionamiento y publicación

- Estados mínimos: Borrador, Publicado, Obsoleto.

- Una versión publicada es inmutable.

- Todo cambio que afecte la definición genera nueva versión.

- La tarea conserva la versión utilizada.

- Los resultados históricos permanecen interpretables con la versión correspondiente.

- Un formulario obsoleto no se utiliza para nuevas tareas, pero permanece disponible para trazabilidad.

# 25. Reglas de negocio

18. Formulario define qué información debe capturarse.

19. Las claves de búsqueda provienen de estructuras de activos gobernadas por Inventario.

20. Las claves de contexto provienen de dimensiones funcionales gobernadas por Organización.

21. Claves, contexto y filtros no son tipos de campo.

22. Los filtros se construyen sobre maestros y jerarquías existentes.

23. Planificación define los criterios concretos y construye el dataset.

24. El formulario no almacena la población objetivo.

25. Los campos tienen definición individual, orden, obligatoriedad y validaciones.

26. Foto, documento, video y nota de voz son tipos de información/campos; no métodos de captura.

27. Los mecanismos tecnológicos de interacción pertenecen a ejecución.

28. Una versión publicada no se modifica.

29. Preview, prueba y ejecución utilizan la misma parametrización.

30. Web y Mobile deben ser funcionalmente equivalentes respecto de la definición.

31. Mobile no cambia el dataset objetivo.

32. Los resultados conservan tarea, formulario, versión y contexto.

# 26. Casos de uso

| **ID** | **Caso**                       | **Resultado**                                                   |
|--------|--------------------------------|-----------------------------------------------------------------|
| FRM-01 | Crear formulario               | Plantilla en borrador.                                          |
| FRM-02 | Configurar claves              | Claves de búsqueda/contexto seleccionadas desde maestros.       |
| FRM-03 | Configurar filtros disponibles | Planificación conoce qué dimensiones/jerarquías puede utilizar. |
| FRM-04 | Construir campos               | Campos definidos con tipo, orden y obligatoriedad.              |
| FRM-05 | Configurar validaciones        | Datos inválidos controlados.                                    |
| FRM-06 | Previsualizar                  | Se muestra la parametrización.                                  |
| FRM-07 | Publicar                       | Versión inmutable.                                              |
| FRM-08 | Configurar tarea               | Planificación aplica criterios y genera dataset.                |
| FRM-09 | Probar tarea                   | Se visualiza la misma experiencia que recibirá el operador.     |
| FRM-10 | Ejecutar Mobile                | Se captura sobre el dataset.                                    |
| FRM-11 | Guardar resultado              | Resultado trazable.                                             |

# 27. Criterios de aceptación

- El formulario puede configurarse sin crear maestros paralelos.

- Las claves de búsqueda disponibles provienen de estructuras de activos publicadas por Inventario.

- Las claves de contexto provienen de Organización y su segmentación física.

- Los filtros se basan en maestros y jerarquías existentes.

- Los campos disponibles incluyen texto, números, fechas, selecciones, sí/no, foto, documento, video, nota de voz y firma.

- No existen métodos de captura dentro de la configuración funcional del formulario.

- No existen 'Activo relacionado' ni 'Ubicación' como tipos de campo; se resuelven mediante claves/contexto.

- Cada campo tiene orden explícito.

- Los campos obligatorios bloquean la finalización si no tienen valor válido.

- Las validaciones se ejecutan según la definición.

- Las condiciones pueden activar visibilidad/obligatoriedad cuando estén definidas.

- Preview y prueba de tarea utilizan la misma parametrización.

- Mobile recibe la versión exacta y el dataset de la tarea.

- Mobile no modifica la población.

- Los resultados mantienen trazabilidad.

- Las versiones publicadas son inmutables.

# 28. Contribución a la inteligencia del negocio

Formularios no interpreta ni decide. Su aporte es asegurar que la información que alimentará Inventario, Knowledge, Analytics y Advisory tenga una estructura consistente, contexto y trazabilidad.

- Las claves permiten segmentar resultados usando dimensiones gobernadas.

- El contexto permite interpretar resultados por proceso, actividad y territorio físico.

- Las validaciones mejoran la calidad de los datos.

- Los campos de evidencia permiten conservar soporte multimodal.

- El versionamiento permite interpretar correctamente resultados históricos.

- La uniformidad de la parametrización evita divergencias entre Web, Planificación y Mobile.

# 29. Consideraciones para desarrollo

- Implementar el formulario como configuración persistida y versionada.

- No hard-codear formularios individuales.

- No crear maestros duplicados dentro de Formularios.

- No introducir métodos de captura como propiedad funcional del formulario.

- Implementar un renderer común que interprete la parametrización.

- El renderer debe respetar campos, orden, obligatoriedad, validaciones, catálogos y dependencias.

- Planificación resuelve criterios y dataset.

- Mobile consume el paquete y ejecuta.

- El contrato de ejecución debe identificar FormularioID + versión + TareaID.

- La versión publicada debe ser inmutable.

- Los cambios de definición deben producir una nueva versión.

- La lógica de negocio no debe quedar embebida en componentes visuales.

# 30. Ejemplo integral

Caso: Evaluación reproductiva de hembras adultas.

| **Elemento**        | **Configuración**                                                                               |
|---------------------|-------------------------------------------------------------------------------------------------|
| Formulario          | EVAL-REPRO v3                                                                                   |
| Claves de búsqueda  | Especie, Sexo, Edad, Estado                                                                     |
| Claves de contexto  | Proceso, Actividad, Sector, Potrero                                                             |
| Filtros disponibles | Sexo, Edad, Estado, jerarquía física                                                            |
| Campos              | Condición corporal, Peso, ¿Presenta anomalía?, Tipo de anomalía, Observación, Foto, Nota de voz |
| Orden               | Identificación/contexto → evaluación → hallazgos → evidencia → cierre                           |
| Obligatorios        | Condición corporal y Peso; Tipo de anomalía y evidencia según condición.                        |
| Validaciones        | Rangos, catálogos y dependencias.                                                               |

Planificación configura: Sexo = Hembra; Edad \>= 24 meses; Estado = Activo; Sector = Norte; Potrero = P-03. El sistema obtiene el dataset objetivo. La función 'Probar tarea' presenta ese dataset con EVAL-REPRO v3. Mobile recibe exactamente esa definición y ejecuta sobre los mismos registros. Cada resultado queda asociado a la tarea, formulario, versión, activo y contexto.

La tecnología mediante la cual el operador proporciona el peso, una observación o una evidencia no cambia la definición del formulario. El dato final se almacena según el tipo de campo definido.

# 31. Principio final

**FORMULARIOS = DEFINICIÓN · PLANIFICACIÓN = DATASET · MOBILE = EJECUCIÓN**

El formulario es una parametrización simple, versionada y reutilizable. Define las dimensiones de selección/contexto y el contenido exacto de captura. Planificación convierte esa definición en una tarea y dataset. Mobile ejecuta y devuelve resultados. La misma parametrización se utiliza en Preview, Planificación, Web y Mobile.
