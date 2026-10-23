# 02 · Funcional Organización v1.2

> **Fuente de solo lectura.** Convertido a Markdown desde `02_FUNCIONAL_ORGANIZACION_GANUS_v1.2_COMPLETO_FINAL.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

# 02 · FUNCIONAL — ORGANIZACIÓN

Gobierno funcional de la empresa, finca, estructura física, procesos, actividades, usuarios y contexto empresarial

Versión 1.2 · Documento funcional para desarrollo MVP

## PROPÓSITO DEL DOCUMENTO

Definir de manera completa qué debe hacer Organización dentro de GANUS, qué información gobierna, qué información de referencia registra, qué contexto entrega a los demás dominios y cuáles son sus límites. Organización establece el contexto empresarial y operativo sobre el cual los demás dominios trabajan; no administra la operación diaria ni interpreta el negocio.

## REGLA TRANSVERSAL INCORPORADA

Todo funcional GANUS debe explicar su contribución a la inteligencia del negocio. Organización produce contexto confiable y gobernado. Ese contexto puede ser utilizado por Knowledge, Analytics y Advisory, pero Organización no define reglas, indicadores, alertas ni recomendaciones.

## 1. Propósito y resultado esperado

Organización es el dominio que establece el contexto empresarial y operativo mínimo sobre el cual funciona GANUS. Define quién es la organización, qué fincas existen, cómo se estructura físicamente cada finca, qué procesos y tipos de actividad reconoce el negocio, quién puede operar en cada contexto, qué configuración aplica y cuál es la clasificación empresarial de cada finca.

El resultado es un contexto estructurado y trazable que permite a Inventario, Formularios, Planificación, Mobile, Track, Knowledge, Analytics y Advisory trabajar sobre una realidad empresarial común.

Organización no crea el dato operativo final. Crea y gobierna el contexto que da significado y ámbito a esos datos.

## 2. Lugar de Organización dentro de GANUS

| **Dominio**               | **Responsabilidad**                                                                                                 | **Relación con Organización**                                  |
|---------------------------|---------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| Organización              | Gobierno empresarial, fincas, estructura física, procesos/actividad, usuarios, ámbitos y clasificación empresarial. | Fuente del contexto organizacional y empresarial.              |
| Configuración transversal | Roles, permisos, seguridad, navegación, catálogos maestros, secuencias y servicios comunes.                         | Organización consume y aplica; no redefine.                    |
| Inventario                | Identidad y hoja de vida de activos.                                                                                | Usa finca y espacios; mantiene el activo.                      |
| Formularios               | Define cómo se captura información.                                                                                 | Consume procesos/tipos de actividad y contexto permitido.      |
| Planificación             | Define trabajo, programación, población y responsable.                                                              | Usa finca, espacios, procesos y actividades.                   |
| Mobile                    | Ejecuta y captura en campo.                                                                                         | Recibe contexto a través de la operación.                      |
| Track                     | Representa geográficamente territorio y eventos.                                                                    | Consume los IDs funcionales de espacios.                       |
| Knowledge                 | Gobierna conocimiento, reglas y su aplicación.                                                                      | Toma contexto de Organización como referencia para sus reglas. |
| Analytics                 | Presenta información e indicadores.                                                                                 | Usa dimensiones de Organización para segmentar.                |
| Advisory                  | Interpreta y recomienda.                                                                                            | Usa contexto autorizado y resultados gobernados.               |

### LÍMITE FUNDAMENTAL

Organización define la estructura funcional y empresarial de la finca. Track define su representación geográfica. Knowledge define y aplica conocimiento. No debe existir un segundo maestro de espacios ni un segundo maestro de clasificación empresarial en otro dominio.

## 3. Alcance funcional MVP

• Grupos empresariales opcionales.

• Fincas independientes o asociadas a un Grupo Empresarial.

• Datos básicos y ubicación de la finca: Departamento → Municipio.

• Clasificación empresarial de la finca: Industria → Sector → Negocio.

• Definición de estructura física mediante espacios parametrizables y relaciones padre/hijo.

• Definición del catálogo funcional de procesos y tipos de actividad.

• Usuarios y asociación a una o varias fincas según autorización.

• Asignación de roles predefinidos; administración técnica de roles/permisos por configuración transversal.

• Aplicación de configuración corporativa y específica de finca cuando corresponda.

• Separación de información por ámbito autorizado.

• Desactivación conservando historial.

• Auditoría mediante servicios transversales.

• Entrega de contexto estructurado a dominios consumidores.

• Entrega de la clasificación empresarial como referencia para que Knowledge pueda utilizarla en sus reglas y relaciones de conocimiento.

### 3.1 Fuera de alcance de Organización

• Crear o modificar permisos técnicos.

• Diseñar o modificar roles predefinidos.

• Definir reglas de Knowledge, condiciones, umbrales o fórmulas de indicadores.

• Generar alertas o recomendaciones.

• Ejecutar o programar tareas operativas.

• Capturar resultados de campo.

• Mantener identidad de activos.

• Definir ciclo de vida de activos.

• Definir polígonos, geocercas o coordenadas.

• Construir dashboards o indicadores.

• Interpretar datos mediante IA.

• Administrar datos específicos de mercado; estos pueden ser utilizados por Knowledge según sus fuentes y reglas.

## 4. Conceptos funcionales

| **Concepto**         | **Definición**                                             | **Responsabilidad**          |
|----------------------|------------------------------------------------------------|------------------------------|
| Grupo Empresarial    | Entidad opcional que agrupa una o varias fincas.           | Organización                 |
| Finca                | Unidad operacional donde ocurre la actividad productiva.   | Organización                 |
| Industria            | Nivel superior de clasificación empresarial de la finca.   | Organización / catálogo      |
| Sector               | Clasificación empresarial dependiente de una Industria.    | Organización / catálogo      |
| Negocio              | Clasificación específica dependiente de un Sector.         | Organización / catálogo      |
| Contexto empresarial | Conjunto Industria → Sector → Negocio asociado a la finca. | Organización                 |
| Espacio              | Unidad de segmentación física definida por la finca.       | Organización                 |
| Tipo de espacio      | Clase parametrizable de espacio.                           | Configuración + Organización |
| Proceso              | Agrupador funcional de actividades del negocio.            | Organización                 |
| Tipo de actividad    | Actividad reconocida por el negocio dentro de un proceso.  | Organización                 |
| Usuario              | Cuenta de acceso de una persona o colaborador autorizado.  | Organización + transversal   |
| Rol                  | Conjunto predefinido de capacidades.                       | Configuración transversal    |
| Ámbito               | Conjunto de finca/contexto donde puede operar un usuario.  | Organización + seguridad     |
| Configuración        | Parámetro corporativo o específico de finca.               | Configuración transversal    |

## 5. Modelo organizacional

Modelo conceptual de contexto:

GRUPO (opcional) → FINCA → CLASIFICACIÓN EMPRESARIAL / ESTRUCTURA FÍSICA / PROCESOS → TIPOS DE ACTIVIDAD

USUARIO → ROL PREDEFINIDO → ÁMBITO AUTORIZADO → FINCA / CONTEXTO

### 5.1 Grupo Empresarial

• Es opcional.

• Puede agrupar una o varias fincas.

• Una finca puede existir sin grupo.

• La separación de información funciona tanto para fincas independientes como agrupadas.

• El código es automático y no editable por el cliente.

### 5.2 Finca

• Es la unidad operacional principal de la solución.

• Toda operación debe quedar contextualizada por una finca cuando corresponda.

• Registra Departamento → Municipio mediante catálogos.

• Registra clasificación Industria → Sector → Negocio.

• Puede estar activa o inactiva sin destruir historial.

• Puede tener configuración específica cuando el parámetro transversal lo permita.

### 5.3 Clasificación empresarial y contexto para Knowledge

La clasificación empresarial es una funcionalidad propia de Organización que permite registrar el tipo de negocio al que pertenece una finca. Su modelo es estrictamente: Industria → Sector → Negocio.

Su propósito es establecer una referencia empresarial estable. No es un módulo de mercado, no es una regla y no interpreta información. Es un dato de contexto que otros dominios pueden consumir.

### 5.3.1 Modelo de referencia

FINCA → INDUSTRIA → SECTOR → NEGOCIO

La clasificación debe conservar la dependencia jerárquica: un Sector pertenece a una Industria y un Negocio pertenece a un Sector. La finca mantiene una clasificación empresarial vigente.

### 5.3.2 Funcionalidad

1\. Seleccionar la finca.

2\. Seleccionar la Industria.

3\. Consultar los Sectores disponibles para la Industria.

4\. Seleccionar el Sector.

5\. Consultar los Negocios disponibles para el Sector.

6\. Seleccionar el Negocio.

7\. Guardar la clasificación como parte del contexto empresarial de la finca.

8\. Permitir su consulta por los dominios autorizados.

9\. Registrar mediante auditoría los cambios de clasificación.

### 5.3.3 Para qué sirve en GANUS

La clasificación permite que GANUS conozca el contexto empresarial antes de interpretar los datos de una finca. Por ejemplo, Agropecuario → Ganadería → Leche y Agropecuario → Ganadería → Carne pertenecen a un mismo sector, pero no necesariamente requieren las mismas reglas, referencias o conocimiento.

La clasificación, por tanto, funciona como una referencia que permite a Knowledge saber sobre qué tipo de negocio está trabajando.

### 5.3.4 Relación con Knowledge

La relación funcional es:

ORGANIZACIÓN → INDUSTRIA → SECTOR → NEGOCIO → REFERENCIA DE CONTEXTO → KNOWLEDGE

Knowledge toma esta información como referencia y puede utilizarla dentro de sus reglas para determinar el contexto de aplicación, seleccionar conocimiento preconstruido, relacionar fuentes o interpretar resultados según el tipo de negocio.

Ejemplo: una regla de Knowledge puede estar definida para un Negocio determinado. La clasificación de la finca permite identificar si esa regla es aplicable al contexto de la finca.

Organización no crea la regla ni decide su aplicación. Entrega la referencia empresarial; Knowledge la utiliza según su propia lógica.

### 5.3.5 Relación con información de mercado

La clasificación empresarial también puede servir como referencia para que Knowledge utilice información de mercado pertinente. Por ejemplo, una finca clasificada como Ganadería → Carne puede requerir referencias de mercado diferentes a una finca Ganadería → Leche.

Organización solamente registra la clasificación. Los datos, precios, tendencias, referencias externas o fuentes de mercado son responsabilidad de las fuentes y mecanismos que Knowledge utilice. No se convierten en datos administrados por Organización.

### 5.3.6 Qué administra y qué no administra Organización

| **Organización administra**                               | **Organización NO administra** |
|-----------------------------------------------------------|--------------------------------|
| Industria                                                 | Precios o datos de mercado     |
| Sector                                                    | Reglas de Knowledge            |
| Negocio                                                   | Indicadores o umbrales         |
| Relación de la clasificación con la finca                 | Alertas o recomendaciones      |
| Estado/vigencia e historial de la clasificación           | Interpretación mediante IA     |
| Disponibilidad de la referencia para dominios autorizados | Aplicación de la regla         |

### 5.3.7 Resultado funcional

El resultado es un contexto empresarial estructurado de la finca, disponible para consumo autorizado. Knowledge lo toma como referencia y lo utiliza dentro de sus reglas, sin duplicar el maestro de Organización.

DECISIÓN: Industria → Sector → Negocio pertenece a Organización. Es referencia de contexto; Knowledge la consume y la utiliza en sus reglas.

## 6. Estructura física de la finca — capacidad funcional de primer nivel

La segmentación física es una capacidad propia de Organización porque define cómo el negocio nombra y estructura físicamente su unidad productiva.

### DECISIÓN DE DISEÑO

GANUS no impone una única jerarquía. Cada finca define los niveles que realmente utiliza.

| **Elemento**         | **Comportamiento**                                                                         |
|----------------------|--------------------------------------------------------------------------------------------|
| Tipo de espacio      | Define el nombre funcional del nivel: Lote, Parcela, Potrero, Corral, Sector, Zona u otro. |
| Espacio              | Instancia concreta dentro de la finca.                                                     |
| Padre/hijo           | Permite representar jerarquía sin profundidad fija.                                        |
| Estado               | Activa/desactiva conservando historial.                                                    |
| Identidad            | ID único dentro del contexto GANUS.                                                        |
| Ubicación geográfica | Track puede asociar posteriormente geometría/coordenadas.                                  |

### 6.1 Ejemplo

Finca La Esperanza → Sector Norte → Potrero 01 / Potrero 02; Sector Sur → Potrero 03.

### 6.2 Reglas de segmentación

• La estructura pertenece a una finca.

• Un espacio tiene un tipo parametrizado.

• Un espacio puede tener padre salvo la raíz.

• No se permiten ciclos.

• Desactivar no elimina historia.

• Los nombres funcionales no quedan codificados en frontend.

• Otros dominios consumen el maestro sin duplicarlo.

## 7. Procesos y tipos de actividad

Organización define el vocabulario funcional con el que la finca describe su operación; no programa, planifica ni ejecuta.

| **Nivel**             | **Ejemplo**             | **Qué hace**                               |
|-----------------------|-------------------------|--------------------------------------------|
| Proceso               | Manejo Animal           | Agrupa familia de actividades.             |
| Tipo de actividad     | Vacunación              | Actividad reconocida dentro del proceso.   |
| Actividad planificada | Vacunación lote A       | Se crea/programa en Planificación.         |
| Captura               | Resultado de vacunación | Se define en Formularios y ejecuta Mobile. |

• Manejo Animal → vacunación, desparasitación, pesaje, revisión, identificación.

• Reproducción → diagnóstico, inseminación, revisión reproductiva.

• Nutrición → suministro, revisión de consumo, ajuste.

• Mantenimiento → inspección, limpieza, reparación.

• Administración → actividades documentales o de control.

Cada negocio puede configurar su catálogo. No debe convertirse en catálogo rígido codificado.

## 8. Usuarios, roles y ámbito de acceso

Organización administra la relación usuario-organización-finca. Roles y permisos técnicos pertenecen a configuración transversal.

| **Responsabilidad**      | **Organización** | **Configuración transversal** |
|--------------------------|------------------|-------------------------------|
| Crear/desactivar usuario | Sí               | Servicio de identidad         |
| Asociar usuario a finca  | Sí               | Valida seguridad              |
| Asignar rol predefinido  | Sí               | Sí, según autorización        |
| Definir rol              | No               | Superusuario GANUS            |
| Definir permisos         | No               | Superusuario GANUS            |
| Resolver autorización    | Consume          | Sí                            |
| Navegación según permiso | Consume          | Sí                            |

### 8.1 Roles MVP

• Dueño.

• Administrador.

• Supervisor.

• Operador.

Otros perfiles de negocio no se convierten automáticamente en roles técnicos independientes.

## 9. Configuración corporativa y por finca

| **Nivel**   | **Uso**                             | **Regla**                                        |
|-------------|-------------------------------------|--------------------------------------------------|
| Corporativo | Valor común para varias fincas.     | Puede heredarse cuando corresponda.              |
| Finca       | Excepción o particularidad.         | Prevalece si el parámetro lo permite.            |
| Dominio     | Configuración específica de módulo. | La administra el dominio o servicio transversal. |

## 10. Flujos funcionales

### 10.1 Alta de organización

1\. Crear/identificar Grupo si aplica.

2\. Crear finca.

3\. Registrar Departamento → Municipio.

4\. Registrar Industria → Sector → Negocio.

5\. Definir estructura física.

6\. Definir procesos y tipos de actividad.

7\. Crear/activar usuarios.

8\. Asociar usuarios a fincas autorizadas.

9\. Asignar roles predefinidos.

10\. Aplicar configuración corporativa/finca.

11\. Dejar contexto disponible para dominios consumidores.

### 10.2 Configuración de estructura física

1\. Seleccionar finca.

2\. Seleccionar tipo de espacio.

3\. Crear espacio.

4\. Definir nombre/código y estado.

5\. Seleccionar padre cuando aplique.

6\. Validar ausencia de ciclos.

7\. Guardar y auditar.

8\. Publicar identificador para consumo.

### 10.3 Configuración de procesos y actividades

1\. Seleccionar/crear proceso.

2\. Crear tipo de actividad bajo proceso.

3\. Definir estado.

4\. Permitir consulta por Formularios y Planificación.

5\. No crear tareas desde Organización.

### 10.4 Configuración de clasificación empresarial

1\. Seleccionar finca.

2\. Seleccionar Industria.

3\. Seleccionar Sector válido para la Industria.

4\. Seleccionar Negocio válido para el Sector.

5\. Guardar clasificación.

6\. Auditar cambio.

7\. Dejar referencia disponible para consumidores autorizados, especialmente Knowledge.

## 11. Datos funcionales

| **Entidad**       | **Datos mínimos**                                                                  | **Salida principal**        |
|-------------------|------------------------------------------------------------------------------------|-----------------------------|
| Grupo             | ID, código, nombre, datos básicos, estado.                                         | Contexto corporativo        |
| Finca             | ID, nombre, código, grupo opcional, ubicación, Industria, Sector, Negocio, estado. | Contexto de finca           |
| Tipo de espacio   | ID, nombre, estado, configuración.                                                 | Catálogo de segmentación    |
| Espacio           | ID, finca, tipo, nombre/código, padre, estado.                                     | Estructura física           |
| Industria         | ID, nombre/código, estado.                                                         | Referencia de clasificación |
| Sector            | ID, Industria, nombre/código, estado.                                              | Referencia de clasificación |
| Negocio           | ID, Sector, nombre/código, estado.                                                 | Referencia de clasificación |
| Proceso           | ID, nombre/código, estado.                                                         | Catálogo funcional          |
| Tipo de actividad | ID, proceso, nombre/código, estado.                                                | Catálogo de actividades     |
| Usuario           | ID, identidad, estado.                                                             | Cuenta contextualizada      |
| Usuario-Finca     | Usuario, finca, estado, vigencia.                                                  | Ámbito de acceso            |
| Usuario-Rol       | Usuario, rol, contexto, estado.                                                    | Asignación de capacidad     |

## 12. Reglas de negocio

| **ID**     | **Regla**                                                                             | **Objetivo**                           |
|------------|---------------------------------------------------------------------------------------|----------------------------------------|
| RN-ORG-001 | Grupo Empresarial es opcional.                                                        | Permitir finca independiente.          |
| RN-ORG-002 | Una finca pertenece a un único grupo cuando existe relación corporativa.              | Evitar ambigüedad.                     |
| RN-ORG-003 | Toda operación queda contextualizada por finca cuando corresponda.                    | Aislamiento y trazabilidad.            |
| RN-ORG-004 | Ningún usuario consulta información fuera de su ámbito autorizado.                    | Seguridad.                             |
| RN-ORG-005 | Roles del cliente son predefinidos y no editables.                                    | Centralizar seguridad.                 |
| RN-ORG-006 | La asignación consume catálogo transversal de roles.                                  | Evitar duplicación.                    |
| RN-ORG-007 | Desactivar usuario conserva historial.                                                | Trazabilidad.                          |
| RN-ORG-008 | Los espacios pertenecen a una finca.                                                  | Evitar huérfanos.                      |
| RN-ORG-009 | La jerarquía de espacios no contiene ciclos.                                          | Consistencia.                          |
| RN-ORG-010 | Tipos de espacio son parametrizables.                                                 | Evitar rigidez.                        |
| RN-ORG-011 | Procesos y actividades son contexto/catálogo, no tareas.                              | Separar configuración de ejecución.    |
| RN-ORG-012 | Tipos de actividad pertenecen a un proceso.                                           | Estructura funcional.                  |
| RN-ORG-013 | Cambios administrativos quedan auditados.                                             | Trazabilidad.                          |
| RN-ORG-014 | Organización no define indicadores, alertas ni recomendaciones.                       | Evitar duplicidad.                     |
| RN-ORG-015 | Track es responsable de representación geográfica.                                    | Único dueño geográfico.                |
| RN-ORG-016 | La clasificación de la finca se registra en orden Industria → Sector → Negocio.       | Consistencia del contexto empresarial. |
| RN-ORG-017 | Sector debe pertenecer a la Industria seleccionada y Negocio al Sector seleccionado.  | Integridad de la clasificación.        |
| RN-ORG-018 | La clasificación empresarial es referencia de contexto para dominios consumidores.    | Facilitar interpretación contextual.   |
| RN-ORG-019 | Knowledge puede consumir la clasificación y utilizarla en sus reglas.                 | Aplicación correcta del contexto.      |
| RN-ORG-020 | Organización no administra los datos específicos de mercado utilizados por Knowledge. | Separar referencia de fuente/dato.     |

## 13. Requerimientos funcionales

| **ID**  | **Requerimiento**                                                                      | **Prioridad** |
|---------|----------------------------------------------------------------------------------------|---------------|
| ORG-001 | Crear y administrar Grupos Empresariales.                                              | MVP           |
| ORG-002 | Crear y administrar fincas con grupo opcional.                                         | MVP           |
| ORG-003 | Registrar Departamento → Municipio mediante catálogos.                                 | MVP           |
| ORG-004 | Registrar Industria → Sector → Negocio.                                                | MVP           |
| ORG-005 | Consultar y mantener clasificación empresarial de la finca.                            | MVP           |
| ORG-006 | Entregar clasificación empresarial como referencia contextual a dominios consumidores. | MVP           |
| ORG-007 | Definir tipos de espacio parametrizables.                                              | MVP           |
| ORG-008 | Crear estructura física padre/hijo por finca.                                          | MVP           |
| ORG-009 | Consultar estructura física por finca.                                                 | MVP           |
| ORG-010 | Crear y administrar procesos.                                                          | MVP           |
| ORG-011 | Crear y administrar tipos de actividad asociados a procesos.                           | MVP           |
| ORG-012 | Crear/desactivar usuarios.                                                             | MVP           |
| ORG-013 | Asociar usuarios a fincas autorizadas.                                                 | MVP           |
| ORG-014 | Asignar roles predefinidos.                                                            | MVP           |
| ORG-015 | Consultar roles/permisos efectivos sin modificarlos.                                   | MVP           |
| ORG-016 | Consumir configuración corporativa/específica.                                         | MVP           |
| ORG-017 | Aplicar separación por ámbito.                                                         | MVP           |
| ORG-018 | Mantener historial mediante auditoría.                                                 | MVP           |
| ORG-019 | Entregar contexto estructurado a dominios consumidores.                                | MVP           |

## 14. Contribución a la inteligencia del negocio

Organización no interpreta el negocio. Construye el contexto que permite que los datos posteriores tengan significado.

### 14.1 ¿Qué contexto aporta Organización?

| **Contexto producido**          | **Para qué sirve en inteligencia**                                                                                     |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Finca y pertenencia corporativa | Segmentar resultados y controlar ámbito.                                                                               |
| Ubicación administrativa        | Análisis territoriales y comparativos.                                                                                 |
| Industria / Sector / Negocio    | Contextualizar el tipo de negocio y servir como referencia para que Knowledge aplique conocimiento/reglas pertinentes. |
| Estructura física               | Interpretar dónde ocurre un evento o desviación.                                                                       |
| Proceso                         | Agrupar desempeño.                                                                                                     |
| Tipo de actividad               | Medir frecuencia, cumplimiento y resultados.                                                                           |
| Usuario / rol / ámbito          | Trazabilidad.                                                                                                          |
| Estado e historial              | Análisis temporal.                                                                                                     |
| Configuración aplicable         | Interpretar resultados según configuración vigente.                                                                    |

### 14.2 Cadena de inteligencia

ORGANIZACIÓN → CONTEXTO EMPRESARIAL Y OPERATIVO → DATOS OPERATIVOS → KNOWLEDGE → ANALYTICS / ADVISORY → DECISIÓN

La clasificación Industria → Sector → Negocio no produce inteligencia por sí misma. Es una referencia que Knowledge toma para determinar el contexto de aplicación de sus reglas.

### 14.3 Datos que pueden alimentar inteligencia

• Grupo, finca y clasificación empresarial.

• Espacios, procesos y actividades.

• Usuario, rol y ámbito.

• Estados, vigencias y cambios.

• Auditoría y contexto administrativo.

### 14.4 Qué NO debe aparecer aquí

• Reglas de umbral.

• Cálculo de productividad.

• Determinación de riesgo.

• Generación de recomendaciones.

• Interpretación IA.

## 15. Relación con temas transversales

| **Tema**                | **Cómo participa Organización**                               | **Dueño principal**       |
|-------------------------|---------------------------------------------------------------|---------------------------|
| Identidad               | Consume identidad.                                            | Servicios transversales   |
| Roles/permisos          | Asigna roles predefinidos y consume autorización.             | Configuración transversal |
| Seguridad               | Define grupo/finca/ámbito.                                    | Seguridad + Organización  |
| Navegación              | Aporta contexto y rol.                                        | Transversal               |
| Catálogos               | Consume tipos parametrizados y publica catálogos funcionales. | Configuración + dominio   |
| Secuencias              | Consume generación automática.                                | Configuración transversal |
| Auditoría               | Genera/consulta trazabilidad.                                 | Transversal               |
| Notificaciones          | Consume mecanismos comunes.                                   | Transversal               |
| Integración/API/eventos | Publica cambios de contexto necesarios.                       | Contratos e Integraciones |

## 16. Integraciones y contratos de salida

Los demás dominios no deben acceder directamente a tablas/repositorios internos de Organización. El contexto se expone mediante contratos internos o interfaces de aplicación.

| **Consumidor** | **Necesita**                                                          | **Salida de Organización**                               |
|----------------|-----------------------------------------------------------------------|----------------------------------------------------------|
| Inventario     | Finca y espacio válido.                                               | Contexto de finca + IDs de espacios.                     |
| Formularios    | Procesos/tipos de actividad.                                          | Catálogo funcional.                                      |
| Planificación  | Finca, espacios, procesos, actividades.                               | Contexto y referencias.                                  |
| Mobile         | Contexto de tarea.                                                    | Indirecto vía Planificación.                             |
| Track          | Estructura territorial funcional.                                     | Espacios e IDs.                                          |
| Knowledge      | Finca + Industria + Sector + Negocio + contexto operativo autorizado. | Referencia de contexto empresarial y cambios relevantes. |
| Analytics      | Dimensiones organizacionales.                                         | Dimensiones de segmentación.                             |
| Advisory       | Contexto autorizado.                                                  | Contexto, no reglas.                                     |

### 16.1 Eventos conceptuales

• GroupCreated / GroupUpdated / GroupDeactivated.

• FarmCreated / FarmUpdated / FarmDeactivated.

• BusinessClassificationUpdated.

• SpaceTypeUpdated.

• SpaceCreated / SpaceUpdated / SpaceDeactivated / SpaceMoved.

• ProcessCreated / ProcessUpdated / ProcessDeactivated.

• ActivityTypeCreated / ActivityTypeUpdated / ActivityTypeDeactivated.

• UserFarmAssigned / UserFarmUnassigned.

• UserRoleAssigned / UserRoleUnassigned.

Los nombres son contratos conceptuales; la implementación final sigue Contratos e Integraciones y la arquitectura oficial.

## 17. Experiencia funcional / pantallas

| **Pantalla / vista**         | **Objetivo**                          | **Contenido**                                 |
|------------------------------|---------------------------------------|-----------------------------------------------|
| OR-01 Organización           | Ver y administrar contexto principal. | Grupo, fincas, estado, filtros.               |
| OR-02 Finca                  | Administrar datos de finca.           | Ubicación, clasificación empresarial, estado. |
| OR-03 Estructura física      | Definir jerarquía.                    | Árbol, tipos, altas, edición, estado.         |
| OR-04 Procesos               | Administrar procesos.                 | Listado, estado, detalle.                     |
| OR-05 Tipos de actividad     | Administrar actividades.              | Proceso, actividad, estado.                   |
| OR-06 Usuarios               | Administrar usuarios y ámbitos.       | Usuario, fincas, rol, estado.                 |
| OR-07 Contexto/configuración | Consultar configuración.              | Valores corporativos/finca.                   |
| OR-08 Auditoría              | Consultar trazabilidad.               | Acción, usuario, fecha, objeto, resultado.    |

La clasificación empresarial debe formar parte de OR-02 Finca. No requiere un módulo independiente de mercado.

## 18. Casos de uso y criterios de aceptación

| **Caso**  | **Escenario**                     | **Criterio**                                                                  |
|-----------|-----------------------------------|-------------------------------------------------------------------------------|
| CU-ORG-01 | Crear finca independiente         | Se crea sin Grupo y queda operable.                                           |
| CU-ORG-02 | Crear finca con grupo             | Queda asociada a un único Grupo.                                              |
| CU-ORG-03 | Definir clasificación empresarial | Se selecciona Industria, luego Sector válido y luego Negocio válido.          |
| CU-ORG-04 | Cambiar clasificación             | El cambio queda auditado y la nueva clasificación queda vigente.              |
| CU-ORG-05 | Definir estructura física         | Se construye jerarquía válida.                                                |
| CU-ORG-06 | Consultar estructura en Track     | Track resuelve IDs sin duplicar maestro.                                      |
| CU-ORG-07 | Definir proceso y actividad       | Actividad queda vinculada a proceso.                                          |
| CU-ORG-08 | Asignar usuario                   | Opera solo en ámbito autorizado.                                              |
| CU-ORG-09 | Asignar rol                       | Se asigna rol predefinido.                                                    |
| CU-ORG-10 | Auditar cambio                    | Modificación queda trazable.                                                  |
| CU-ORG-11 | Consumir contexto en Knowledge    | Knowledge puede recibir Industria/Sector/Negocio como referencia para reglas. |

### 18.1 Criterios de aceptación críticos

• Una finca puede existir sin Grupo.

• Industria → Sector → Negocio se mantiene como clasificación empresarial de la finca.

• El Sector y Negocio deben corresponder a los niveles superiores seleccionados.

• El cambio de clasificación es auditable.

• Knowledge puede consumir la clasificación sin acceso directo a tablas de Organización.

• Organización no contiene reglas, indicadores, alertas ni recomendaciones.

• La estructura física es parametrizable.

• No existe acceso directo de Track a tablas internas.

• El cliente no modifica roles/permisos.

• La seguridad impide acceso fuera del ámbito.

## 19. Consideraciones para desarrollo

### 19.1 Principios

• Implementar Organización como dominio claramente delimitado.

• No permitir acceso directo desde otros dominios a repositorios/tablas internas.

• Exponer casos de uso/servicios de aplicación.

• Usar IDs estables.

• No codificar tipos de espacio, procesos ni clasificación empresarial como valores rígidos en frontend.

• Mantener estados y desactivación en lugar de borrado físico.

• Aplicar auditoría transversal.

• Mantener aislamiento por contexto.

• Tratar Industria → Sector → Negocio como referencia maestra de contexto, no como lógica de Knowledge.

• Knowledge debe consumir el contexto mediante contrato y utilizarlo dentro de sus propias reglas.

### 19.2 Uso de IA durante desarrollo

• La IA puede generar entidades, DTOs, casos de uso, validaciones y pruebas a partir de este contrato.

• No debe inventar reglas funcionales.

• Debe comprobar límites de dominio: ciclo de vida en Inventario; reglas/indicadores en Knowledge/Analytics/Advisory; geografía en Track.

• Debe probar la integridad Industria → Sector → Negocio.

• No debe crear acceso directo de Knowledge a tablas de Organización.

### 19.3 Definición de terminado

• CRUD funcional de grupos/fincas.

• Clasificación Industria → Sector → Negocio operativa y validada.

• Estructura física parametrizable y jerárquica.

• Procesos y actividades operativos como catálogos.

• Usuarios y roles.

• Integración con autorización.

• Auditoría.

• Contratos de consulta/salida.

• Pruebas unitarias, integración, autorización, aislamiento y reglas.

• Prueba de consumo del contexto por Knowledge.

## 20. Trazabilidad y decisiones cerradas

| **Tema**                   | **Decisión definitiva**                                                                                  |
|----------------------------|----------------------------------------------------------------------------------------------------------|
| Grupo                      | Opcional; finca independiente permitida.                                                                 |
| Clasificación empresarial  | Industria → Sector → Negocio pertenece a Organización y se registra por finca.                           |
| Uso por Knowledge          | La clasificación es referencia de contexto; Knowledge la toma y la utiliza en sus reglas.                |
| Mercado                    | Los datos de mercado no son administrados por Organización; pueden ser fuentes utilizadas por Knowledge. |
| Roles                      | Predefinidos para cliente; configuración técnica por Superusuario GANUS.                                 |
| Segmentación física        | Capacidad propia de Organización; niveles parametrizables.                                               |
| Procesos/actividades       | Catálogo/contexto; ejecución y programación fuera.                                                       |
| Ciclo de vida              | Pertenece a Inventario.                                                                                  |
| Reglas/condiciones/alertas | Pertenece a Knowledge/Analytics/Advisory según función.                                                  |
| Geografía                  | Track es responsable.                                                                                    |
| Auditoría                  | Servicio transversal.                                                                                    |
| Inteligencia               | Organización aporta dimensiones y contexto; no interpreta ni recomienda.                                 |

## 21. Referencias documentales

• GANUS · Alcance y Especificación Funcional Completa v3.1 — fuente funcional consolidada.

• GANUS · Organización — documento funcional base histórico.

• GANUS · Visión y Alcance Unificado del Ecosistema v2.0 — referencia de visión y fuentes de conocimiento.

• GANUS · Arquitectura de Referencia v1.1 — arquitectura oficial.

• GANUS · Contratos e Integraciones — mecanismo de intercambio entre dominios.

• GANUS · Modelo BD Referencia MVP — dónde vive la información.

## REGLA DE LA SUITE FUNCIONAL GANUS

El documento funcional define el comportamiento; el modelo BD de referencia define dónde vive la información; el contrato define cómo cruza la frontera. Ninguno debe asumir el trabajo de los otros.
