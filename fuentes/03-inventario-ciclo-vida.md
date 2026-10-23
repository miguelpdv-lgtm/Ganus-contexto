# 03 · Funcional Inventario y Ciclo de Vida v1.2

> **Fuente de solo lectura.** Convertido a Markdown desde `03_FUNCIONAL_INVENTARIO_Y_CICLO_DE_VIDA_GANUS_v1.2.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**GANUS**

**03 · FUNCIONAL — INVENTARIO Y CICLO DE VIDA**

Identidad digital, hoja de vida, relaciones, evidencia y evolución de los activos

Versión 1.2 · Documento funcional para desarrollo MVP

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>PROPÓSITO<br />
</strong>Definir el comportamiento funcional de Inventario como fuente única de verdad de la identidad digital de los activos, incorporando formalmente el Ciclo de Vida como capacidad transversal del dominio y dejando claras sus fronteras con Organización, Formularios, Planificación, Track, Knowledge, Analytics y Advisory.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>REGLA TRANSVERSAL<br />
</strong>Inventario debe explicar no solo cómo administra activos, sino qué contexto y datos aporta a la inteligencia del negocio. Todo funcional GANUS debe conservar esta visión: el dato producido por un dominio debe poder convertirse, cuando corresponda, en conocimiento, indicador, alerta, recomendación y decisión sin duplicar maestros.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

Base documental: GANUS Alcance y Especificación Funcional Completa v3.1; GANUS Inventario; GANUS Documento Funcional de Alcance MVP.

# 1. Propósito

Inventario administra la identidad digital permanente de los activos de la organización. Un activo existe una sola vez en GANUS, puede tener múltiples identificadores y es la fuente oficial que consumen los demás dominios.

El dominio debe permitir representar activos biológicos, territoriales, infraestructura, equipos, vehículos, personas cuando aplique, dispositivos/identidades digitales y otros activos definidos por la organización. El modelo no debe quedar diseñado únicamente alrededor de animales.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>PRINCIPIO RECTOR<br />
</strong>La información pertenece al activo, no al proceso. Los procesos registran hechos sobre el activo; Inventario conserva la identidad, atributos, relaciones, evidencias, estado e historial que permiten reconstruir su realidad.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

## 1.1 Resultado esperado

- Cada activo posee un ActivoID único y permanente.

- Los identificadores RFID, chapeta, código oficial, QR, código interno u otros resuelven al mismo ActivoID.

- El activo pertenece a una finca y puede asociarse a un espacio físico válido.

- La ficha digital reúne identidad, atributos, relaciones, documentos, estado e historial.

- El activo puede tener un Ciclo de Vida asignado desde su creación.

- Los cambios relevantes quedan trazables.

- Los consumidores reciben la identidad sin duplicarla.

# 2. Lugar dentro del ecosistema

| **Dominio**               | **Responsabilidad**                                                                 | **Relación con Inventario**                                                                    |
|---------------------------|-------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| Organización              | Contexto empresarial y estructura funcional de la finca.                            | Entrega finca y espacios; Inventario referencia esos IDs.                                      |
| Configuración transversal | Taxonomía, secuencias, seguridad, catálogos y servicios comunes.                    | Inventario consume configuración publicada.                                                    |
| Inventario                | Identidad, atributos, relaciones, estado, documentos, ciclo e historial del activo. | Fuente única de verdad.                                                                        |
| Formularios dinámicos     | Define cómo se captura.                                                             | Entrega plantillas/formularios publicados que Inventario consume; no se duplica su definición. |
| Planificación             | Define trabajo, población, asignación y ejecución.                                  | Consume activos y ciclo/tareas referenciales cuando corresponda.                               |
| Móvil                     | Ejecuta y captura.                                                                  | Identifica activos dentro del paquete recibido y registra resultados.                          |
| Track                     | Territorio, posiciones, geocercas y eventos espaciales.                             | Usa ActivoID y espacios; no crea identidad maestra.                                            |
| Knowledge                 | Reglas y evaluación de condiciones.                                                 | Evalúa datos del activo/ciclo; no modifica identidad por inferencia.                           |
| Analytics                 | Indicadores y visualización.                                                        | Consume dimensiones y datos históricos del activo.                                             |
| Advisory                  | Interpretación y recomendación.                                                     | Explica estado y situación usando datos y reglas gobernadas.                                   |

# 3. Alcance funcional MVP

- Consultar tipos de activo y plantillas publicadas.

- Crear activos usando una plantilla publicada.

- Generar código interno automático por secuencia definida; no editable.

- Registrar múltiples identificadores y resolverlos al mismo ActivoID.

- Registrar atributos definidos por la plantilla.

- Registrar ubicación dentro de la finca.

- Registrar relaciones entre activos, incluyendo Padre/Hijo cuando aplique.

- Adjuntar documentos y evidencias.

- Consultar ficha digital.

- Buscar, filtrar y consultar activos por atributos disponibles.

- Cambiar estado sin eliminación física.

- Mantener historial completo.

- Importar activos desde Excel/archivo estructurado con plantilla, validación previa, estandarización/mapeo y reporte.

- Asignar un Ciclo de Vida al crear el activo.

- Determinar y mostrar etapa actual según definición, fechas, tiempo y condiciones configuradas.

- Mostrar tareas referenciales, condiciones e indicadores aplicables a la etapa.

- Conservar historial de transición de etapas.

- Publicar identidad y cambios mediante contratos definidos para los consumidores.

## 3.1 Fuera de alcance

- Administrar la taxonomía maestra o crear plantillas: pertenece a Configuración/Formularios dinámicos.

- Definir formularios dinámicos.

- Programar, asignar o ejecutar tareas de campo.

- Definir reglas de negocio, umbrales, indicadores o recomendaciones.

- Crear geocercas o geometrías.

- Ser fuente maestra de posiciones o eventos territoriales.

- Construir dashboards.

- Modificar retrospectivamente una versión histórica del Ciclo de Vida.

# 4. Modelo de activo

**TIPO DE ACTIVO → PLANTILLA → VERSIÓN → ACTIVO → IDENTIFICADORES → UBICACIÓN → RELACIONES → ATRIBUTOS → DOCUMENTOS → HISTORIAL**

| **Componente**        | **Función**                                                                          |
|-----------------------|--------------------------------------------------------------------------------------|
| Tipo de activo        | Clasificación funcional publicada para identificar la familia del activo.            |
| Plantilla             | Estructura de datos aplicable al tipo de activo.                                     |
| Versión               | Versión publicada de la plantilla utilizada para registrar/editar el activo.         |
| Activo                | Instancia real e identidad digital única.                                            |
| Identificadores       | Claves externas/internas que permiten localizar el mismo activo.                     |
| Ubicación             | Finca y espacio funcional donde se encuentra/asocia el activo.                       |
| Relaciones            | Vínculos con otros activos: padre/hijo, composición, pertenencia u otros permitidos. |
| Atributos             | Datos propios definidos por la configuración publicada.                              |
| Documentos/evidencias | Archivos y soportes asociados a la hoja de vida.                                     |
| Historial             | Cambios, estados, relaciones y eventos relevantes del activo.                        |

# 5. Taxonomía, tipos y plantillas

La taxonomía maestra es parametrizable bajo la estructura Dominio → Categoría → Tipo → Subtipo. Inventario la consume; no la administra.

| **Elemento**      | **Regla**                                                        |
|-------------------|------------------------------------------------------------------|
| Dominio           | Nivel superior de clasificación.                                 |
| Categoría         | Agrupa tipos dentro del dominio.                                 |
| Tipo              | Define una familia funcional de activos.                         |
| Subtipo           | Especializa el tipo cuando sea necesario.                        |
| Plantilla         | Define la estructura de captura del activo para un tipo/subtipo. |
| Versión publicada | Es la versión válida que Inventario puede utilizar.              |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>NO DUPLICAR CONFIGURACIÓN<br />
</strong>Inventario no debe crear una copia editable de la plantilla. Debe conservar la referencia a la versión utilizada para registrar el activo, de forma que la ficha pueda explicar con qué estructura fue creada.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 6. Alta y administración del activo

**6.0 Relación con Formularios de Levantamiento**

Además del levantamiento inicial, una actividad operacional puede producir información que actualice de manera controlada la información del activo. Por tanto, la relación entre Inventario, Formularios y MAKE no se limita al alta o levantamiento: también cubre la evolución de la ficha maestra.

ACTIVO → ACTIVIDAD → FORMULARIO/VERSIÓN → CAPTURA → RESULTADO → TRANSACCIÓN → ACTUALIZACIÓN MAESTRA AUTORIZADA Y/O HISTORIAL

Un resultado operacional puede: (1) actualizar un atributo maestro cuando exista un mapeo explícito; (2) registrar información histórica sin modificar la ficha maestra; (3) crear, modificar o finalizar una relación; (4) generar un movimiento o cambio de ubicación; (5) aportar evidencia; o (6) aportar datos para que Knowledge evalúe una condición o transición de ciclo.

MAKE ejecuta y produce el resultado; Formularios Dinámicos define qué y cómo se captura; Inventario conserva la identidad y aplica únicamente las actualizaciones maestras autorizadas. Ningún dominio externo debe modificar directamente las tablas internas de Inventario.

El registro del activo no siempre puede resolverse únicamente con la información estructural de su plantilla. Cuando se necesite levantar información adicional del activo —por ejemplo características complementarias, antecedentes, observaciones, identificación visual o información requerida durante una visita de campo— Inventario debe poder iniciar o asociar un Formulario de Levantamiento publicado por Formularios Dinámicos.

La responsabilidad queda separada: Inventario mantiene la identidad y la ficha del activo; Formularios Dinámicos define qué se captura y cómo; la respuesta queda vinculada al ActivoID. Solo los campos que tengan un mapeo explícito y autorizado pueden actualizar atributos maestros. El resto permanece como resultado del levantamiento y/o evidencia vinculada al activo.

**ACTIVO → FORMULARIO DE LEVANTAMIENTO → CAPTURA → VALIDACIÓN → RESULTADO VINCULADO AL ActivoID → ACTUALIZACIÓN DE ATRIBUTOS SOLO SI EXISTE MAPEO AUTORIZADO.**

| **Componente**        | **Responsabilidad**                                                                       |
|-----------------------|-------------------------------------------------------------------------------------------|
| Inventario            | Identifica el activo, inicia/asocia el levantamiento y conserva la relación con ActivoID. |
| Formularios Dinámicos | Define campos, validaciones, métodos de captura y versión publicada.                      |
| Respuesta             | Registra los datos capturados y queda vinculada al activo.                                |
| Mapeo                 | Solo campos explícitamente autorizados pueden actualizar atributos maestros.              |
| Evidencia             | Fotos, documentos y soportes quedan vinculados al activo y/o al levantamiento.            |
| Historial             | Cualquier cambio sobre atributos maestros conserva trazabilidad.                          |

## 6.1 Flujo de alta

1.  Seleccionar finca desde el contexto autorizado.

2.  Seleccionar tipo de activo publicado.

3.  Seleccionar plantilla y versión publicada.

4.  Completar los campos definidos por la plantilla.

5.  Capturar identificadores disponibles.

6.  Seleccionar ubicación física cuando aplique; si no se selecciona espacio, la ubicación queda en la finca.

7.  Registrar relaciones aplicables.

8.  Asignar el Ciclo de Vida aplicable o aceptar la propuesta configurada por el sistema.

9.  Validar obligatoriedad, unicidad de identificadores, consistencia y reglas estructurales de la plantilla.

10. Generar ActivoID y código interno automático.

11. Guardar el activo.

12. Publicar la identidad para consumidores autorizados.

## 6.2 Validaciones mínimas

- Tipo obligatorio.

- Plantilla obligatoria y publicada.

- Campos obligatorios según plantilla.

- Identificadores únicos cuando la configuración los marque como únicos.

- Estado obligatorio.

- Finca obligatoria.

- Relaciones válidas y sin referencias imposibles.

- Espacio perteneciente a la misma finca.

- Ciclo compatible con el tipo/familia de activo cuando exista esa restricción.

# 7. Identidad e identificadores

El ActivoID es la identidad interna de GANUS. Los identificadores son medios de identificación del activo, no identidades paralelas.

| **Identificador** | **Ejemplos**                     | **Regla**                                                   |
|-------------------|----------------------------------|-------------------------------------------------------------|
| Código interno    | GAN-000123                       | Generado automáticamente; no editable.                      |
| RFID              | EPC/ID electrónico               | Puede resolver directamente al ActivoID.                    |
| Chapeta           | Número visible del animal        | Puede cambiar o coexistir con otros identificadores.        |
| Código oficial    | Identificación legal/regulatoria | Se conserva como identificador del activo.                  |
| QR                | Código visual                    | Resuelve al ActivoID.                                       |
| Clave antigua     | Identificador histórico          | Debe conservarse para búsqueda/trazabilidad cuando aplique. |
| Otro              | Código de tercero/dispositivo    | Debe registrar tipo y valor según configuración.            |

- Varios identificadores pueden apuntar al mismo ActivoID.

- Un identificador que ya resuelve a otro activo no puede reasignarse silenciosamente.

- Los cambios de identificadores deben conservar historial.

- El lector/dispositivo no se convierte en maestro del activo.

# 8. Activos biológicos y relaciones

Las relaciones forman parte extensiva de la información del activo y no deben modelarse como referencias estáticas sin contexto. Una relación debe poder conservar tipo, activo origen, activo destino, vigencia/estado, fuente y trazabilidad.

Cuando una relación sea creada o modificada por una actividad, formulario, evento o integración, la relación debe conservar la referencia al hecho que la originó. Esto permite explicar, por ejemplo, por qué GANUS considera que un animal es madre de otro o que un activo pertenece a determinado conjunto.

La terminación o corrección de una relación no debe destruir la relación histórica. Debe registrarse su vigencia y conservarse la evidencia de la modificación.

Los activos biológicos usan el mismo modelo universal, con atributos y relaciones específicos según plantilla.

| **Capacidad**              | **Comportamiento**                                                              |
|----------------------------|---------------------------------------------------------------------------------|
| Sexo                       | Disponible cuando el tipo/plantilla lo requiera.                                |
| Raza                       | Dato configurable del activo biológico.                                         |
| Edad/fecha aplicable       | Puede derivarse o registrarse según configuración; el valor debe ser trazable.  |
| Padre/Madre                | Relación de genealogía cuando exista.                                           |
| Hijo                       | Relación inversa derivada/registrada según modelo.                              |
| Otros activos relacionados | Permite vínculos adicionales sin convertir la relación en una copia del activo. |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>PRINCIPIO<br />
</strong>La genealogía es una relación entre activos. No debe almacenarse como texto aislado si el activo relacionado existe en Inventario.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 9. Ubicación y relación con la estructura física

La ubicación debe entenderse como información con evolución temporal. La ficha puede mostrar la ubicación actual, pero Inventario debe poder conservar el historial de ubicaciones cuando exista un cambio relevante.

Un movimiento puede originarse por una actividad ejecutada, una captura manual, un evento de automatización, RFID, TRACK o una integración autorizada. El origen no cambia la propiedad de la identidad: ActivoID continúa siendo la referencia transversal.

Ejemplo conceptual: Potrero 2 → Potrero 5 → Potrero 7. La ubicación actual representa el estado vigente; el historial representa la trayectoria del activo.

- El activo pertenece a una finca.

- Puede asociarse a un espacio definido por Organización.

- El espacio debe existir dentro de la finca.

- Inventario conserva la referencia funcional al espacio.

- Track puede agregar representación geográfica, posición o geocerca sin crear otra identidad de activo.

- Un cambio de ubicación debe poder quedar en historial cuando el modelo funcional lo requiera.

**ORGANIZACIÓN define ESPACIO → INVENTARIO asocia ACTIVO → TRACK territorializa ESPACIO/ACTIVO**

# 10. CICLO DE VIDA DEL ACTIVO

El Ciclo de Vida es una capacidad central de Inventario y no un simple estado manual. Representa la evolución esperada de una familia de activos mediante etapas, tareas referenciales, condiciones, indicadores, eventos de transición e historial.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>OBJETIVO<br />
</strong>Asignar el ciclo al crear el activo permite que GANUS conozca no solo qué es el activo, sino dónde se encuentra dentro de su evolución y qué debería observarse o hacerse alrededor de él.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

## 10.1 Modelo conceptual

| **Componente**       | **Definición**                                                                           |
|----------------------|------------------------------------------------------------------------------------------|
| Ciclo de Vida        | Plantilla de evolución aplicable a una familia/tipo de activo.                           |
| Versión del ciclo    | Definición versionada; cambios futuros no reescriben historia.                           |
| Etapa                | Periodo o condición dentro del ciclo, con orden, duración esperada y/o transición.       |
| Tarea referencial    | Actividad que normalmente debería ejecutarse durante una etapa; orienta a Planificación. |
| Condición            | Situación esperada, riesgo, desviación o criterio que Knowledge puede evaluar.           |
| Indicador de etapa   | Variable útil para observar evolución/desempeño en esa etapa.                            |
| Evento de transición | Hecho o condición que confirma, adelanta, retrasa o cierra una etapa.                    |
| Historial del ciclo  | Registro de etapas atravesadas, fechas, fuente/evento y trazabilidad.                    |

## 10.2 Ejemplo conceptual para un activo biológico

Un ciclo puede contener etapas como cría → predestete → levante → desarrollo → pre-servicio → gestación → lactancia → descarte. Este ejemplo no es un catálogo rígido y no debe quedar codificado exclusivamente para bovinos.

## 10.3 Asignación

13. Al crear el activo, el usuario autorizado selecciona el CicloID aplicable o el sistema propone uno según taxonomía/configuración.

14. El activo conserva la referencia al CicloID y a la versión correspondiente.

15. La etapa actual se determina según definición del ciclo, fechas relevantes, tiempo transcurrido y condiciones parametrizadas.

16. La ficha muestra la etapa actual y permite consultar la explicación de cómo fue determinada.

## 10.4 Tareas referenciales

- Son guía de lo que normalmente debería ocurrir en una etapa.

- No son tareas de campo por sí mismas.

- Planificación puede utilizarlas para generar trabajo cuando una regla o usuario autorizado lo determine.

- Si se genera una tarea, Planificación se convierte en dueño de la ocurrencia, asignación, fecha y ejecución.

## 10.5 Condiciones e indicadores

- Las condiciones son elementos que Knowledge puede evaluar.

- Inventario conserva la definición contextual de la etapa, pero no ejecuta reglas de negocio.

- Los indicadores de etapa son referencias funcionales; su cálculo/publicación corresponde a Knowledge/Analytics según el caso.

- Una condición incumplida no crea automáticamente una acción: una regla gobernada debe determinar el resultado.

## 10.6 Transiciones

- Una transición puede ser temporal, basada en fecha, evento o condición.

- El sistema debe conservar fecha, fuente/evento y trazabilidad.

- La etapa actual puede cambiar sin borrar las etapas anteriores.

- Una corrección no debe destruir la historia.

# 11. Reglas de negocio del Ciclo de Vida

| **ID**   | **Regla**                                                                                        |
|----------|--------------------------------------------------------------------------------------------------|
| RN-CV-01 | Todo activo con ciclo definido mantiene referencia al CicloID aplicable.                         |
| RN-CV-02 | El ciclo es versionable; cambios de definición no reescriben retrospectivamente la historia.     |
| RN-CV-03 | La etapa actual debe poder explicarse mediante los parámetros/reglas que la determinaron.        |
| RN-CV-04 | Las tareas referenciales orientan; Planificación controla creación, asignación y ejecución real. |
| RN-CV-05 | Una condición incumplida no genera por sí sola una acción sin una regla de Knowledge.            |
| RN-CV-06 | Advisory recomienda únicamente a partir de datos, indicadores y reglas gobernadas.               |
| RN-CV-07 | Cada transición conserva fecha, fuente/evento y trazabilidad.                                    |
| RN-CV-08 | El ciclo puede aplicarse a cualquier familia de activo que tenga un modelo de evolución.         |

## 11.1 Reglas críticas de Inventario

| **ID**    | **Regla**                                                    |
|-----------|--------------------------------------------------------------|
| RN-INV-01 | Un activo existe una sola vez.                               |
| RN-INV-02 | El ActivoID es único y permanente.                           |
| RN-INV-03 | El código interno es automático y no editable.               |
| RN-INV-04 | Un identificador adicional no crea un nuevo activo.          |
| RN-INV-05 | Todo activo pertenece a una finca.                           |
| RN-INV-06 | El activo usa una plantilla publicada.                       |
| RN-INV-07 | No existe eliminación física del activo.                     |
| RN-INV-08 | Toda modificación relevante genera historial.                |
| RN-INV-09 | Inventario no define reglas, indicadores ni planificación.   |
| RN-INV-10 | Ningún otro dominio duplica la identidad maestra del activo. |

**11.5 Fotos de identificación**

Las fotos no deben tratarse únicamente como documentos adjuntos. GANUS debe diferenciar las fotos de identificación del activo de las evidencias fotográficas producidas por una actividad o por un levantamiento.

Las fotos de identificación forman parte de la hoja de vida visual del activo. Pueden capturarse durante el alta, mediante un Formulario de Levantamiento o posteriormente por un usuario autorizado. Deben quedar vinculadas al ActivoID y conservar fecha, usuario y contexto de captura.

**Tipos funcionales:**

- Foto principal: imagen representativa que aparece en la ficha del activo.

- Fotos de identificación: vistas o características útiles para reconocer físicamente el activo.

- Foto de identificador: evidencia visual de chapeta, placa, marca, RFID visible u otro identificador cuando aplique.

- Fotos de condición: evidencia visual asociada a una condición/estado cuando la configuración lo permita.

- Fotos de levantamiento: imágenes capturadas como parte de un formulario; son evidencia contextual, pero quedan vinculadas al activo.

**Regla: una foto de identificación no reemplaza el ActivoID ni el identificador formal; es evidencia visual complementaria. Las fotos no se convierten automáticamente en atributos estructurados.**

# 12. Ficha digital del activo

## 11.7 Actualización de información maestra desde la operación

Un resultado de actividad no debe modificar libremente la ficha maestra. Solo los campos configurados explícitamente como actualizables desde resultados pueden producir una actualización maestra.

La configuración debe identificar, como mínimo: campo capturado, tipo de dato, si produce resultado histórico, si puede actualizar atributo maestro y atributo maestro destino.

Ejemplo: un campo 'Peso' de una actividad de pesaje puede actualizar 'Peso actual' y conservar simultáneamente el resultado histórico del pesaje. Un campo 'Observación del operador' puede conservarse como resultado/evidencia sin convertirse automáticamente en un atributo maestro.

La actualización debe conservar valor anterior, valor nuevo, fecha efectiva, origen, actividad, formulario/versión, usuario y demás trazabilidad aplicable.

## 11.6 Transacciones de Inventario

Inventario debe formalizar las transacciones que producen cambios o enriquecen la información del activo. Una transacción es el registro trazable de un hecho que incorpora, modifica, relaciona, mueve, desactiva o contextualiza un activo.

Tipos mínimos: Alta; Actualización maestra; Relación; Movimiento; Baja lógica/desactivación; Levantamiento; Resultado operacional; Cambio de ciclo/etapa.

Una transacción debe conservar, cuando aplique: TransacciónID, ActivoID, tipo, fecha/hora efectiva, usuario u origen técnico, ActividadID, FormularioID + versión, EventoID, ReglaID + versión, valores anterior/nuevo, evidencia y referencias de contexto.

Las transacciones no sustituyen el historial de negocio ni el log de auditoría. La transacción explica el hecho; el historial muestra la evolución; la auditoría permite reconstruir quién, cuándo y cómo se produjo el cambio.

| **Sección**           | **Contenido**                                                                          |
|-----------------------|----------------------------------------------------------------------------------------|
| Identidad             | ActivoID, código interno, tipo, plantilla/versión, estado.                             |
| Identificadores       | RFID, chapeta, oficial, QR, legado y otros.                                            |
| Ubicación             | Finca, espacio y referencias aplicables.                                               |
| Características       | Atributos definidos por la plantilla.                                                  |
| Relaciones            | Genealogía y otras relaciones configuradas.                                            |
| Ciclo de Vida         | Ciclo, versión, etapa actual, fecha de etapa, guía y transición.                       |
| Documentos/Evidencias | Fotos, documentos y soportes.                                                          |
| Historial             | Cambios, estados, identificadores, ubicación, relaciones y ciclo.                      |
| Actividad relacionada | Consulta de hechos/trabajos relacionados, sin convertirlos en propiedad de Inventario. |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>UNA SOLA FICHA<br />
</strong>La ficha digital es el punto de consulta de identidad. Los resultados de procesos no deben copiarse como atributos permanentes si pertenecen a otra entidad; deben vincularse al activo mediante referencias.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

**12.5 Levantamiento como parte de la ficha**

| **Capa**                   | **Ejemplo**                                                  | **Dueño**                                                    |
|----------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| Identidad maestra          | ActivoID, código, tipo, identificadores, finca.              | Inventario                                                   |
| Atributo maestro           | Sexo, raza, fecha de nacimiento u otros atributos definidos. | Inventario/configuración                                     |
| Resultado de levantamiento | Observación, medición, respuesta o dato contextual.          | Formularios / dominio correspondiente                        |
| Evidencia                  | Foto, documento, firma u otro soporte.                       | Formulario/servicio de evidencias + referencia en Inventario |

La ficha digital puede mostrar la información estructural del activo y los levantamientos realizados sobre él. Esto separa claramente identidad maestra, atributos actualizados mediante mapeo autorizado y resultados/evidencias de captura.

# 13. Migración masiva

## 12.6 Información histórica y contexto de la ficha

La ficha digital debe distinguir claramente entre información actual, histórico de resultados, relaciones vigentes/históricas, movimientos y evidencias.

Cuando exista información histórica suficiente, la ficha debe permitir acceder al contexto temporal relevante sin perder el dato actual. El patrón 'dato actual + últimos datos relevantes' es preferente cuando sea útil, pero no constituye una regla rígida para todos los tipos de información.

El contexto mostrado depende de la naturaleza del dato. Por ejemplo, peso puede requerir últimos pesajes y evolución; vacunación puede requerir última aplicación, anterior y próxima fecha; reproducción puede requerir eventos reproductivos y sus intervalos; ubicación puede requerir movimientos recientes.

Inventario entrega el histórico y sus metadatos. Knowledge/Analytics determina el contexto comparativo apropiado para indicadores e interpretación.

La migración permite incorporar inventarios existentes, especialmente desde Excel, sin sacrificar la estructura ni la identidad.

17. Seleccionar finca y tipo de activo.

18. Descargar plantilla de importación basada en la configuración publicada.

19. Completar o preparar archivo.

20. Cargar archivo.

21. Validar estructura, columnas, tipos, obligatoriedad e identificadores.

22. Aplicar estandarización/mapeo configurado cuando corresponda.

23. Mostrar errores por registro/campo antes de importar.

24. Importar únicamente registros válidos según la regla del proceso.

25. Generar ActivoID/código para nuevos activos.

26. Resolver identificadores existentes para evitar duplicados.

27. Generar reporte final de cargados, rechazados y causas.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>REGLA ESTRICTA<br />
</strong>No debe importarse silenciosamente un registro que incumpla la estructura. La importación debe ser explicable y producir evidencia del resultado.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 14. Publicación e interoperabilidad

Las publicaciones de cambios deben conservar la referencia a la transacción que los originó. Un consumidor debe poder identificar ActivoID y, cuando aplique, la actividad, formulario/versión, evento o regla que produjo el cambio.

La interoperabilidad no debe transmitir solamente el valor final cuando el caso de uso requiera contexto o trazabilidad. Los contratos definirán el mínimo necesario para cada consumidor, sin convertir a otros dominios en maestros de Inventario.

Cuando un activo se crea o modifica, los consumidores autorizados deben poder recibir su identidad y cambios mediante los mecanismos definidos por Contratos e Integraciones. La publicación no significa que Inventario escriba directamente en otros dominios.

| **Consumidor** | **Consume de Inventario**                                                                      |
|----------------|------------------------------------------------------------------------------------------------|
| Planificación  | ActivoID, identidad, atributos autorizados, estado, ubicación/contexto y referencias de ciclo. |
| Formularios    | Identidad para seleccionar/relacionar activos.                                                 |
| Móvil          | Datos precargados del activo dentro del paquete de trabajo.                                    |
| Track          | ActivoID y referencias territoriales; Track mantiene posiciones/eventos.                       |
| Knowledge      | Datos del activo y contexto del ciclo para evaluación.                                         |
| Analytics      | Dimensiones, estado, atributos e historial autorizado.                                         |
| Advisory       | Contexto de identidad y ciclo para interpretación.                                             |

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>ARQUITECTURA<br />
</strong>No se deben usar accesos directos a tablas de Inventario desde otros dominios. La frontera se resuelve mediante interfaces de aplicación, contratos y/o eventos según la arquitectura oficial.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

# 15. Contribución a la inteligencia del negocio

## 15.2 Principio transversal de contexto

GANUS no debe limitarse a almacenar o mostrar datos: debe preservar su contexto, evolución y origen para que puedan convertirse en información confiable y posteriormente en inteligencia de negocio.

Cuando exista histórico suficiente, un dato actual debe poder complementarse con contexto histórico relevante. La selección debe depender de la naturaleza del dato, el activo, su ciclo de vida, la actividad y la regla de conocimiento aplicable. Puede utilizarse como patrón preferente el dato actual + últimos 3 datos relevantes, pero no debe imponerse como regla universal.

El contexto puede incluir: dato anterior, últimos N registros, período equivalente, tendencia, variación, acumulado, etapa del ciclo, población comparable, actividad que originó el dato, ubicación, relación y fuente.

La responsabilidad se distribuye así: Inventario conserva identidad e histórico; Formularios conserva definición y versión de captura; MAKE produce resultados; Knowledge determina contexto, cálculos y reglas; Dashboard e Informes presentan información contextualizada; Advisory interpreta y recomienda con base en información sustentada.

Por tanto, un número correcto pero aislado no debe considerarse suficiente para representar información de negocio cuando exista contexto relevante disponible.

Los Formularios de Levantamiento amplían la capacidad de conocimiento del activo sin romper su identidad. Permiten capturar información contextual que no necesariamente debe convertirse en un atributo fijo. La ficha mantiene la identidad; el formulario captura la situación; Knowledge y Advisory pueden posteriormente utilizar el resultado bajo las reglas autorizadas.

Inventario es uno de los principales productores de contexto confiable para la inteligencia de GANUS. Sin una identidad única, los eventos no pueden atribuirse correctamente; sin historial, no existe evolución; sin ciclo, no existe contexto temporal del activo.

**ACTIVO → DATO → HISTORIAL / CICLO → KNOWLEDGE → INDICADOR / ALERTA → ADVISORY → DECISIÓN → ACCIÓN**

| **Dato/contexto producido** | **Valor para inteligencia**                                     |
|-----------------------------|-----------------------------------------------------------------|
| Identidad única             | Permite consolidar todos los hechos de un mismo activo.         |
| Tipo/taxonomía              | Permite comparar familias y segmentos.                          |
| Ubicación                   | Permite análisis por finca/espacio y correlación con Track.     |
| Relaciones                  | Permite genealogía, composición y análisis de dependencias.     |
| Historial                   | Permite reconstruir evolución y detectar cambios.               |
| Estado                      | Permite conocer disponibilidad/situación actual.                |
| Ciclo + etapa               | Permite interpretar qué debería estar ocurriendo en el momento. |
| Tareas referenciales        | Permiten orientar qué trabajo podría ser relevante.             |
| Condiciones                 | Dan contexto a Knowledge para evaluar desviaciones.             |
| Evidencias                  | Aumentan trazabilidad y capacidad de explicar decisiones.       |

## 15.1 Qué NO decide Inventario

- No determina si una condición es riesgosa: Knowledge.

- No calcula/publica indicadores de negocio: Knowledge/Analytics.

- No decide generar una alerta: Knowledge/Advisory según regla.

- No recomienda una acción: Advisory.

- No crea ni asigna una tarea: Planificación.

- No determina ubicación geográfica: Track.

# 16. Relación con temas transversales

| **Tema**                | **Inventario**                               | **Dueño principal**                    |
|-------------------------|----------------------------------------------|----------------------------------------|
| Identidad/autorización  | Consume usuario/finca/ámbito.                | Servicios transversales + Organización |
| Taxonomía               | Consume estructura publicada.                | Configuración transversal              |
| Plantillas/formularios  | Consume versión publicada.                   | Formularios dinámicos                  |
| Secuencias              | Consume generación de código.                | Configuración transversal              |
| Auditoría               | Genera cambios relevantes.                   | Servicio transversal                   |
| Documentos/evidencias   | Gestiona referencias y asociación al activo. | Inventario + servicio de archivos      |
| Seguridad               | Respeta ámbito autorizado.                   | Servicios transversales                |
| Eventos/integración     | Publica cambios del activo.                  | Contratos e Integraciones              |
| Reglas de negocio       | No las administra.                           | Knowledge                              |
| Indicadores             | Aporta datos; no los gobierna.               | Knowledge/Analytics                    |
| Alertas/recomendaciones | Aporta contexto; no las decide.              | Knowledge/Advisory                     |

**16.5 Reglas específicas de levantamiento y fotografía**

RN-INV-17: Un resultado de actividad puede actualizar un atributo maestro únicamente cuando exista un mapeo explícito y autorizado en la configuración de captura.

RN-INV-18: Todo cambio maestro originado por una actividad conserva el valor anterior, valor nuevo, ActividadID, FormularioID + versión, usuario/origen y fecha efectiva.

RN-INV-19: Las relaciones entre activos son información extensiva del activo y deben conservar tipo, origen/destino, vigencia/estado y trazabilidad cuando hayan sido producidas o modificadas por una transacción.

RN-INV-20: Los movimientos relevantes de ubicación deben conservar historial y origen del movimiento.

RN-INV-21: Toda modificación relevante de Inventario debe generar un log de auditoría inmutable. El log debe permitir reconstruir qué cambió, cuándo, quién o qué lo originó, valor anterior, valor nuevo y fuente del cambio, cuando aplique.

RN-INV-22: El log de auditoría no sustituye el historial de negocio; ambos deben coexistir.

RN-INV-23: Los cambios automáticos deben registrar su origen técnico y, cuando corresponda, EventoID y ReglaID + versión.

RN-INV-24: La información histórica no debe sobrescribirse para ocultar la evolución del activo. Las correcciones deben quedar trazadas.

RN-INV-25: Inventario debe conservar el histórico necesario para que otros dominios puedan construir contexto temporal y comparativo.

RN-INV-26: La presentación contextual de información es una responsabilidad transversal de GANUS; Knowledge, Dashboard, Informes y Advisory deben utilizar histórico y contexto cuando exista información suficiente y relevante.

- RN-INV-11: Un formulario de levantamiento que amplíe información de un activo existente queda vinculado a su ActivoID.

- RN-INV-12: Formularios Dinámicos es dueño de la definición/versionado del formulario; Inventario es dueño de la identidad a la que se vincula.

- RN-INV-13: Una respuesta de levantamiento no modifica automáticamente atributos maestros; solo campos con mapeo explícito pueden hacerlo.

- RN-INV-14: Toda actualización de un atributo maestro originada en un levantamiento conserva formulario, versión, respuesta, usuario y fecha.

- RN-INV-15: Las fotos de identificación son evidencia visual del activo y no sustituyen identificadores formales.

- RN-INV-16: Las fotos de actividad o levantamiento conservan su tipo y contexto y no se confunden con fotos de identificación.

# 17. Requerimientos funcionales

| **ID**  | **Requerimiento**                                                         | **Prioridad** |
|---------|---------------------------------------------------------------------------|---------------|
| INV-001 | Consultar tipos de activo publicados.                                     | P0            |
| INV-002 | Consultar plantillas y versiones publicadas.                              | P0            |
| INV-003 | Registrar activo con plantilla publicada.                                 | P0            |
| INV-004 | Generar ActivoID y código interno automático.                             | P0            |
| INV-005 | Registrar múltiples identificadores.                                      | P0            |
| INV-006 | Resolver identificadores al ActivoID.                                     | P0            |
| INV-007 | Registrar ubicación y espacio válido.                                     | P0            |
| INV-008 | Registrar relaciones entre activos.                                       | P0            |
| INV-009 | Consultar ficha digital completa.                                         | P0            |
| INV-010 | Editar activo respetando versión/configuración aplicable.                 | P0            |
| INV-011 | Cambiar estado conservando historial.                                     | P0            |
| INV-012 | Buscar y filtrar por atributos disponibles.                               | P0            |
| INV-013 | Adjuntar documentos/evidencias.                                           | P0            |
| INV-014 | Importar activos con validación previa.                                   | P1            |
| INV-015 | Estandarizar/mapear archivo de migración.                                 | P1            |
| INV-016 | Generar reporte de migración.                                             | P1            |
| INV-017 | Asignar Ciclo de Vida al activo.                                          | P0            |
| INV-018 | Determinar y mostrar etapa actual.                                        | P0            |
| INV-019 | Mostrar guía de etapa.                                                    | P0            |
| INV-020 | Conservar historial de transiciones.                                      | P0            |
| INV-021 | Publicar identidad/cambios a consumidores autorizados.                    | P0            |
| INV-022 | Iniciar/asociar un Formulario de Levantamiento publicado al activo.       | P1            |
| INV-023 | Vincular la respuesta del levantamiento al ActivoID.                      | P1            |
| INV-024 | Aplicar mapeo explícito de campos del levantamiento a atributos maestros. | P1            |
| INV-025 | Registrar fotos de identificación vinculadas al ActivoID.                 | P0            |
| INV-026 | Diferenciar fotos de identificación de fotos de evidencia/levantamiento.  | P0            |

# 18. Casos de uso y criterios de aceptación

## 17.1 Requerimientos adicionales de transacciones, contexto y auditoría

INV-027 · Registrar transacciones de Inventario para altas, actualizaciones maestras, relaciones, movimientos, bajas lógicas, levantamientos, resultados operacionales y cambios de ciclo.

INV-028 · Permitir mapear explícitamente resultados de formularios/actividades hacia atributos maestros autorizados.

INV-029 · Conservar resultado histórico aun cuando un campo también actualice un valor maestro actual.

INV-030 · Conservar trazabilidad de relaciones creadas o modificadas por actividades, formularios, eventos o integraciones.

INV-031 · Conservar historial de movimientos de ubicación cuando el modelo funcional lo requiera.

INV-032 · Generar log de auditoría para toda modificación relevante, incluyendo valor anterior/nuevo y origen cuando aplique.

INV-033 · Permitir reconstruir el origen de una modificación desde ActivoID hacia transacción, actividad, formulario/versión, evento o regla.

INV-034 · Exponer histórico y metadatos necesarios para que Knowledge/Analytics construyan contexto temporal y comparativo.

INV-035 · Permitir consultar la evolución de un atributo sin perder el valor actual.

| **Caso**  | **Escenario**                | **Criterio de aceptación**                                                                      |
|-----------|------------------------------|-------------------------------------------------------------------------------------------------|
| CU-INV-01 | Crear activo                 | Activo válido recibe ActivoID y código automático.                                              |
| CU-INV-02 | Registrar identificador RFID | RFID resuelve al mismo ActivoID y no duplica activo.                                            |
| CU-INV-03 | Crear activo biológico       | Sexo, raza, edad/fecha y relaciones aplicables están disponibles.                               |
| CU-INV-04 | Cambiar ubicación            | La ubicación válida queda asociada a la finca y trazable.                                       |
| CU-INV-05 | Consultar ficha              | Toda la identidad disponible se presenta sin duplicación.                                       |
| CU-INV-06 | Importar Excel               | Se validan registros y se genera reporte; inválidos no se cargan.                               |
| CU-INV-07 | Asignar ciclo                | Activo queda vinculado a CicloID/versión y relación visible.                                    |
| CU-INV-08 | Determinar etapa             | Se muestra etapa según definición, fechas y condiciones configuradas.                           |
| CU-INV-09 | Mostrar guía                 | Se muestran tareas referenciales, condiciones e indicadores aplicables.                         |
| CU-INV-10 | Evaluar desviación           | Knowledge puede comparar esperado vs registrado.                                                |
| CU-INV-11 | Advisory contextual          | Puede explicar etapa, situación, desviaciones y recomendación basada en reglas/datos.           |
| CU-INV-12 | Historial de transición      | Cada transición conserva fecha, fuente/evento y trazabilidad.                                   |
| CU-INV-13 | Levantamiento complementario | El formulario publicado se ejecuta sobre un ActivoID y la respuesta queda vinculada.            |
| CU-INV-14 | Mapeo de levantamiento       | Solo campos con mapeo explícito actualizan atributos maestros y quedan auditados.               |
| CU-INV-15 | Foto de identificación       | La foto principal/identificación aparece en ficha y queda vinculada al ActivoID.                |
| CU-INV-16 | Foto de evidencia            | La foto conserva tipo y contexto de levantamiento/actividad sin confundirse con identificación. |

# 19. Consideraciones para desarrollo

## 18.1 Casos de aceptación adicionales

CP-INV-17 · Una actividad de pesaje registra un nuevo peso; el peso actual se actualiza si está configurado y el resultado histórico permanece consultable.

CP-INV-18 · Una actividad modifica una relación Madre/Hijo; la relación queda trazada con actividad, formulario/versión y fecha.

CP-INV-19 · Un movimiento cambia el espacio del activo; la ubicación actual cambia y el historial conserva la ubicación anterior cuando aplique.

CP-INV-20 · Una modificación manual muestra valor anterior, valor nuevo, usuario y fecha en auditoría.

CP-INV-21 · Una modificación automática identifica su evento/regla de origen cuando corresponda.

CP-INV-22 · La ficha puede consultar datos actuales e históricos sin sobrescribir el pasado.

CP-INV-23 · Un consumidor autorizado puede recibir un cambio de activo con su referencia de transacción y origen.

CP-INV-24 · El sistema conserva información suficiente para que Knowledge pueda comparar un dato actual con históricos relevantes.

CP-INV-25 · Un dato histórico no se presenta como interpretación de negocio por sí solo; la interpretación requiere regla/contexto sustentado.

## 19.1 Principios técnicos funcionales

El historial de hechos relevantes debe ser append-oriented; una corrección genera un nuevo hecho o registro de auditoría, no la eliminación silenciosa del anterior.

Las transacciones deben tener identificador estable y permitir idempotencia cuando provengan de integraciones o sincronización.

Los logs de auditoría deben ser inmutables desde el punto de vista funcional y contar con controles de acceso para consulta.

Los contratos de integración deben transportar las referencias de trazabilidad necesarias sin exponer tablas internas.

La capa de presentación no debe reconstruir contexto de forma ad hoc. Cuando el contexto requiera reglas de negocio, debe provenir de Knowledge/servicios de información definidos para ello.

- El dominio debe ser dueño de la identidad del activo y su persistencia funcional.

- Los demás dominios consumen ActivoID y datos autorizados; no crean una copia maestra.

- Las relaciones con otros dominios deben usar referencias estables.

- Las plantillas y tipos se consumen desde configuración publicada.

- El versionamiento debe permitir reconstruir con qué configuración se creó el activo.

- El historial debe ser append-oriented para hechos relevantes y no perder trazabilidad.

- El modelo debe ser extensible a activos no biológicos.

- El ciclo de vida debe ser versionable e independiente del tipo concreto de animal.

- Las transiciones deben ser explicables.

## 19.2 Desarrollo asistido por IA

- La IA puede generar entidades, DTOs, casos de uso, validaciones, pruebas y adaptadores a partir de este contrato.

- Debe verificar antes de modificar código si una capacidad pertenece realmente a Inventario o a otro dominio.

- No debe convertir tareas referenciales del ciclo en tareas reales automáticamente.

- No debe crear reglas de negocio no definidas.

- Debe generar pruebas para unicidad de ActivoID/identificadores, aislamiento por finca, integridad de relaciones, versionamiento y transiciones.

- Debe detectar y evitar accesos directos de otros dominios a tablas internas.

## 19.3 Definición de terminado

- Alta, edición, consulta, búsqueda y cambio de estado operativos.

- Identidad e identificadores múltiples operativos.

- Ficha digital completa.

- Relaciones y ubicación funcionales.

- Migración con validación y reporte.

- Ciclo de vida asignable y versionado.

- Etapa actual visible y trazable.

- Historial de transición operativo.

- Contratos de publicación probados.

- Pruebas unitarias/integración/autorización/consistencia.

- Demostración de consumo desde Planificación y Track sin duplicar identidad.

# 20. Fronteras funcionales

| **Componente** | **Sí hace**                                                             | **No hace**                                       |
|----------------|-------------------------------------------------------------------------|---------------------------------------------------|
| Organización   | Finca, espacios, contexto empresarial.                                  | Mantener identidad del activo.                    |
| Inventario     | Identidad, atributos, relaciones, ciclo, estado, documentos, historial. | Planificar/ejecutar trabajo o definir reglas.     |
| Formularios    | Definir captura.                                                        | Crear identidad maestra.                          |
| Planificación  | Crear/asignar/programar trabajo.                                        | Ser maestro de activos.                           |
| Móvil          | Ejecutar/capturar.                                                      | Modificar población objetivo o identidad maestra. |
| Track          | Territorio/posición/geocercas/eventos.                                  | Crear identidad maestra.                          |
| Knowledge      | Evaluar reglas/condiciones.                                             | Ser maestro de identidad.                         |
| Analytics      | Presentar indicadores.                                                  | Crear reglas paralelas.                           |
| Advisory       | Interpretar/recomendar.                                                 | Inventar datos.                                   |

# 21. Decisiones cerradas

Inventario conserva identidad, histórico y trazabilidad; no se convierte en motor de indicadores o recomendaciones.

Las actividades pueden producir cambios en la información del activo, pero solo mediante mapeos explícitos y autorizados.

Toda modificación relevante debe dejar log de auditoría.

Historial de negocio y auditoría son conceptos distintos y ambos son obligatorios.

La información presentada por GANUS debe buscar contexto relevante; 'actual + últimos 3' es patrón preferente, no regla rígida.

Knowledge determina el contexto analítico y comparativo; Dashboard e Informes lo presentan; Advisory lo interpreta.

Los demás documentos funcionales deben adoptar estas reglas como principios transversales, con especial profundidad en Knowledge, Dashboard, Informes y Advisory.

| **Tema**             | **Decisión**                                                                                                                        |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Identidad            | Un activo existe una sola vez; ActivoID es único y permanente.                                                                      |
| Código               | Automático y no editable.                                                                                                           |
| Identificadores      | Múltiples identificadores resuelven un mismo ActivoID.                                                                              |
| Taxonomía            | Se consume desde configuración maestra.                                                                                             |
| Plantillas           | Se consumen publicadas y versionadas.                                                                                               |
| Ubicación            | Finca + espacio de Organización; geografía en Track.                                                                                |
| Ciclo                | Se asigna al crear y pertenece al dominio Inventario.                                                                               |
| Etapa                | Se determina dinámicamente y conserva historial.                                                                                    |
| Tareas referenciales | Orientan a Planificación; no son ejecución automática.                                                                              |
| Condiciones          | Knowledge las evalúa.                                                                                                               |
| Indicadores          | Knowledge/Analytics los gobiernan.                                                                                                  |
| Recomendaciones      | Advisory las interpreta/genera con reglas gobernadas.                                                                               |
| Eliminación          | No se elimina físicamente; se cambia estado.                                                                                        |
| Levantamientos       | Un formulario puede ampliar la información del activo; su definición pertenece a Formularios y su vínculo al ActivoID a Inventario. |
| Mapeo                | Solo campos explícitamente mapeados actualizan atributos maestros.                                                                  |
| Fotos                | Las fotos de identificación son parte de la ficha visual; las fotos de levantamiento/actividad son evidencia contextual.            |

# 22. Referencias documentales

GANUS · Alcance y Especificación Funcional Completa v3.1 — definición consolidada de Inventario y Ciclo de Vida.

GANUS · Inventario — detalle histórico de identidad, administración y migración.

GANUS · Documento Funcional de Alcance MVP — hallazgos y criterios de cierre.

GANUS · Organización v1.1 — contexto de finca y espacios.

GANUS · Arquitectura de Referencia v1.1 — fronteras técnicas y patrón de integración.

GANUS · Principio transversal de contexto, historial y auditoría — decisión funcional v1.2.

- GANUS · Alcance y Especificación Funcional Completa v3.1 — definición consolidada de Inventario y Ciclo de Vida.

- GANUS · Inventario — detalle histórico de identidad, administración y migración.

- GANUS · Documento Funcional de Alcance MVP — hallazgos y criterios de cierre.

- GANUS · Organización v1.1 — contexto de finca y espacios.

- GANUS · Arquitectura de Referencia v1.1 — fronteras técnicas y patrón de integración.

- GANUS · Contratos e Integraciones — mecanismo técnico para intercambio de información.

- GANUS · Modelo BD Referencia MVP — ubicación física de la información.

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>REGLA DE LA SUITE FUNCIONAL GANUS<br />
</strong>El documento funcional define el comportamiento; el modelo BD de referencia define dónde vive la información; el contrato define cómo cruza la frontera. Inventario debe ser la fuente única de identidad del activo.</th>
</tr>
</thead>
<tbody>
</tbody>
</table>
