# Visión, Arquitectura y Alcance Marco v1.0

> **Fuente de solo lectura.** Convertido a Markdown desde `GANUS_Vision_Arquitectura_Alcance_Marco_v1.0.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**GANUS**

**VISIÓN, ARQUITECTURA Y ALCANCE MARCO DEL ECOSISTEMA**

*Documento base para definición funcional, arquitectura y cerebro de desarrollo*

Versión 1.0 · Octubre 2026

# 0. Estado y propósito del documento

| **Elemento** | **Definición**                                                                                       |
|--------------|------------------------------------------------------------------------------------------------------|
| Producto     | GANUS                                                                                                |
| Documento    | Visión, Arquitectura y Alcance Marco del Ecosistema                                                  |
| Propósito    | Fijar los conceptos estructurales antes de derivar el alcance funcional y técnico.                   |
| Uso          | Insumo maestro para Producto, UX, Arquitectura y Claude Code.                                        |
| Naturaleza   | Documento conceptual y funcional de alto nivel; no sustituye los contratos técnicos.                 |
| Prioridad    | La definición del ecosistema establece el destino; el MVP establece el primer alcance implementable. |

**Regla de este documento:** cuando un concepto no esté definido, no debe ser inventado durante el desarrollo. Debe convertirse en una decisión pendiente o en un contrato técnico posterior.

# 1. Definición maestra de GANUS

GANUS es un ecosistema B2B de soluciones especializadas para transformar la finca ganadera en una empresa organizada, controlada, trazable y basada en datos.

El ecosistema conecta la dimensión empresarial, la operación de campo, los activos, el territorio, la automatización, la información y la inteligencia. Cada solución tiene una responsabilidad específica y comparte identidad e información mediante una arquitectura común de datos e intercambio.

El objetivo no es construir una única aplicación que haga todo. El objetivo es construir un ecosistema integrado donde cada componente haga muy bien lo que le corresponde y donde la información pueda circular de manera controlada desde el evento del campo hasta la decisión empresarial y nuevamente hacia la operación.

## 1.1 Principio rector

**De cada evento del campo a las decisiones que generan valor.**

GANUS debe conectar:

- la realidad física de la finca;

- la identificación de activos;

- las actividades y el trabajo;

- los eventos territoriales y automáticos;

- la información empresarial;

- los indicadores y resultados;

- la interpretación y recomendación;

- la decisión y la nueva acción.

# 2. Visión del ecosistema

| **Solución**      | **Responsabilidad principal**                                                                        | **No debe convertirse en**                             |
|-------------------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| ODOO / GANUS CORE | Dimensión empresarial: procesos administrativos, financieros, comerciales y ERP.                     | Sistema de captura especializada de campo.             |
| GANUS OPERATIVO   | Organización, inventario operacional, formularios, planificación y control del trabajo.              | ERP ni sistema territorial.                            |
| APP MÓVIL         | Ejecución del trabajo y captura de resultados en campo, con operación offline.                       | Herramienta de planificación o administración.         |
| GANUS TRACK       | Territorio, ubicación, espacios, geocercas, movimiento y eventos territoriales.                      | Maestro de identidad de activos.                       |
| GANUS POTREROS    | Captura automática de eventos físicos mediante puntos de paso, RFID, cámaras, pesaje y dispositivos. | Motor de reglas empresariales.                         |
| INDICA            | Información e indicadores publicados para comprender el estado del negocio.                          | Fuente independiente de reglas o datos maestros.       |
| ADVISORY          | Interpretación, explicación y recomendación basada en datos y conocimiento autorizado.               | Fuente de datos ni autoridad para inventar resultados. |

## 2.1 Arquitectura conceptual

La arquitectura debe entenderse como un conjunto de soluciones especializadas conectadas por una infraestructura común de información y una frontera explícita de intercambio.

**ODOO · TRACK · POTREROS · APP MÓVIL · GANUS OPERATIVO  
↓  
ÁREA DE INTERCAMBIO / STAGING  
↓  
INFORMACIÓN COMPARTIDA GANUS  
↓  
INDICA / ADVISORY**

# 3. Arquitectura de información

GANUS utilizará una base de datos común al ecosistema, organizada mediante esquemas separados por solución. La separación de esquemas establece ownership y evita que una solución dependa de las tablas internas de otra.

| **Concepto**     | **Regla**                                                                                                                          |
|------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Base de datos    | Infraestructura de información común del ecosistema.                                                                               |
| Esquema          | Espacio lógico propiedad de una solución o dominio.                                                                                |
| Ownership        | Cada solución es responsable de sus entidades y lógica.                                                                            |
| Identidad común  | Los objetos que atraviesan soluciones utilizan identificadores comunes.                                                            |
| Intercambio      | La comunicación funcional se realiza mediante staging y contratos definidos.                                                       |
| Fuente de verdad | Cada entidad tiene un dueño funcional explícito.                                                                                   |
| Histórico        | La información histórica consolidada puede ser explotada por la capa de datos sin convertirse en una segunda fuente transaccional. |

## 3.1 Principio de ownership

| **Objeto / información**             | **Fuente maestra**                       |
|--------------------------------------|------------------------------------------|
| Empresa / procesos ERP               | ODOO                                     |
| Identidad operacional del activo     | GANUS OPERATIVO / Inventario             |
| Trabajo programado                   | GANUS OPERATIVO / Planificación          |
| Ejecución y resultado de campo       | APP MÓVIL → dominio operacional          |
| Posición y evento territorial        | GANUS TRACK                              |
| Evento físico RFID / portal / pesaje | GANUS POTREROS                           |
| Indicador publicado                  | INDICA / capa de conocimiento autorizada |
| Interpretación y recomendación       | ADVISORY                                 |

# 4. Área de intercambio / STAGING

STAGING es la frontera controlada de intercambio entre soluciones. Su función es recibir, dejar disponible, validar técnicamente, registrar estado y permitir procesar información entre sistemas sin acoplar sus bases internas.

STAGING no es una fuente maestra, no reemplaza los esquemas de las soluciones y no debe contener lógica de negocio que pertenezca al sistema productor o consumidor.

| **Flujo**               | **Origen**      | **Destino**     | **Ejemplo**                                                     |
|-------------------------|-----------------|-----------------|-----------------------------------------------------------------|
| Salida de trabajo       | GANUS OPERATIVO | APP MÓVIL       | Paquete de trabajo con tarea, formulario, población y contexto. |
| Resultado de campo      | APP MÓVIL       | GANUS OPERATIVO | Resultado de formulario, evidencia, ubicación y estado.         |
| Evento territorial      | TRACK           | Ecosistema      | Entrada, salida, permanencia, movimiento.                       |
| Evento físico           | POTREROS        | Ecosistema      | RFID detectado, pesaje, paso por portal.                        |
| Información empresarial | ODOO            | Ecosistema      | Datos administrativos, comerciales o financieros autorizados.   |
| Evento / acción         | Ecosistema      | OPERATIVO       | Alerta o condición que puede convertirse en trabajo.            |

## 4.1 Estados mínimos de intercambio

**PENDIENTE → RECIBIDO → VALIDADO → PROCESADO → CONFIRMADO**

Con estados de error/rechazo y reintento cuando corresponda. Todo intercambio debe conservar origen, fecha/hora, versión, identificador y trazabilidad.

# 5. Identidad transversal

Las soluciones pueden tener identificadores internos propios, pero cuando un objeto cruza el ecosistema debe existir una identidad GANUS resoluble.

| **Identificador**      | **Uso**                                                     |
|------------------------|-------------------------------------------------------------|
| GrupoID                | Contexto empresarial, cuando exista.                        |
| FincaID                | Unidad operacional.                                         |
| UsuarioID              | Identidad de acceso y responsabilidad.                      |
| ActivoID               | Identidad transversal del activo.                           |
| Identificador externo  | RFID, código oficial, QR, código anterior u otro.           |
| ActividadID            | Identidad de la actividad.                                  |
| FormularioID + versión | Definición exacta de captura.                               |
| TrabajoID              | Identidad de la orden/tarea planificada.                    |
| EventoID               | Identidad del evento operacional, territorial o automático. |
| EspacioTerritorialID   | Identidad del espacio geográfico del negocio.               |

# 6. GANUS OPERATIVO — núcleo del MVP

GANUS OPERATIVO es el núcleo de gestión del trabajo de la finca. Convierte la necesidad del negocio en trabajo estructurado, lo prepara para el campo y recibe los resultados de ejecución mediante el área de intercambio.

**Cadena principal:** INGRESO → ORGANIZACIÓN → INVENTARIO → FORMULARIOS → PLANIFICACIÓN → INTERCAMBIO → EJECUCIÓN → RECEPCIÓN → CONTROL

| **Dominio**           | **Qué resuelve en el MVP**                                                  |
|-----------------------|-----------------------------------------------------------------------------|
| Ingreso               | Autenticación, contexto de finca y acceso autorizado.                       |
| Organización          | Grupo opcional, finca, usuarios, roles predefinidos y permisos.             |
| Inventario            | Activos, taxonomía, identificadores, relaciones, documentos y migración.    |
| Formularios dinámicos | Definición de captura, campos, catálogos, validaciones, claves y versiones. |
| Planificación         | Qué hacer, sobre quién, cuándo, prioridad y quién ejecuta.                  |
| Intercambio           | Publicación de trabajo al móvil y recepción de resultados.                  |
| Seguimiento           | Estado de tareas, cumplimiento, vencimientos y trazabilidad.                |
| Informes / Dashboard  | Lectura operativa/táctica de resultados definidos.                          |
| Advisory inicial      | Interpretación básica de datos y resultados autorizados.                    |

# 7. Fronteras funcionales

| **Componente** | **Sí hace**                                                                | **No hace**                                                  |
|----------------|----------------------------------------------------------------------------|--------------------------------------------------------------|
| Operativo      | Organiza, administra activos operacionales, configura captura y planifica. | No reemplaza ODOO ni administra territorio avanzado.         |
| Inventario     | Mantiene identidad, atributos, relaciones e historial del activo.          | No decide el trabajo ni construye indicadores.               |
| Formularios    | Define cómo capturar.                                                      | No agenda ni asigna trabajo.                                 |
| Planificación  | Segmenta, programa, asigna y controla el trabajo.                          | No es maestro de activos.                                    |
| Móvil          | Ejecuta, captura, evidencia y sincroniza.                                  | No planifica ni segmenta población.                          |
| TRACK          | Detecta y administra contexto territorial.                                 | No es maestro de activos ni de trabajo.                      |
| Potreros       | Detecta eventos físicos y los normaliza.                                   | No decide su significado empresarial.                        |
| STAGING        | Intercambia información y controla estados técnicos.                       | No es fuente maestra ni motor de negocio.                    |
| ODOO           | Administra procesos ERP.                                                   | No reemplaza la operación de campo.                          |
| Advisory       | Interpreta y recomienda.                                                   | No inventa datos ni modifica transacciones sin autorización. |

# 8. Alcance del MVP

El MVP debe demostrar el funcionamiento del núcleo operacional y su capacidad de integrarse al ecosistema. No pretende implementar todas las capacidades de GANUS.

| **Capacidad**         | **MVP**                          | **Observación**                                                  |
|-----------------------|----------------------------------|------------------------------------------------------------------|
| Organización          | P0                               | Implementar.                                                     |
| Inventario            | P0                               | Implementar.                                                     |
| Formularios dinámicos | P0                               | Implementar.                                                     |
| Planificación         | P0                               | Implementar.                                                     |
| App móvil             | P0                               | Implementar ejecución y captura.                                 |
| Staging               | P0                               | Implementar frontera de intercambio.                             |
| Seguimiento           | P0                               | Implementar control operativo.                                   |
| Informes / Dashboard  | P0                               | Implementar conjunto inicial.                                    |
| Advisory              | P1/P0 inicial                    | Interpretación basada en datos/reglas disponibles.               |
| ODOO                  | Integración inicial              | No construir ERP.                                                |
| TRACK                 | Contrato + integración preparada | La implementación avanzada de tracking puede evolucionar aparte. |
| POTREROS              | Contrato + integración preparada | Hardware/automatización no debe bloquear el MVP Operativo.       |
| IA avanzada           | Fuera                            | No es condición para validar el MVP.                             |
| Optimización avanzada | Fuera                            | No es condición para validar planificación básica.               |

## 8.1 Qué debe poder demostrarse

- Crear una finca y usuarios autorizados.

- Crear/importar activos y resolver sus identificadores.

- Crear un formulario dinámico y publicarlo.

- Seleccionar una población de activos.

- Crear y programar una tarea.

- Asignar el trabajo a un recurso.

- Generar el paquete de trabajo.

- Entregarlo a la aplicación móvil mediante staging.

- Ejecutarlo con o sin conectividad.

- Capturar resultado y evidencia.

- Recibir el resultado en staging.

- Procesar el resultado en Operativo.

- Actualizar el estado de la tarea y la trazabilidad del activo.

- Mostrar cumplimiento en seguimiento/dashboard.

- Consumir eventos externos mediante contratos sin modificar la lógica interna.

# 9. Modelo operacional fundamental

**ACTIVO → ACTIVIDAD → DATO → INFORMACIÓN → CONOCIMIENTO → DECISIÓN → ACCIÓN → NUEVO DATO**

El ecosistema debe permitir reconstruir qué se definió, qué se planificó, qué se ejecutó, qué resultado se obtuvo y qué decisión o acción puede derivarse.

# 10. Flujos maestros del ecosistema

## 10.1 Trabajo operativo

Necesidad → Formulario → Población → Planificación → Staging → Móvil → Ejecución → Staging → Operativo → Seguimiento

## 10.2 Evento territorial

TRACK → Evento → Staging → Normalización → Dominio consumidor → Regla/resultado → Operativo o Advisory

## 10.3 Evento físico automático

POTREROS → RFID/Cámara/Pesaje → Evento → Staging → Normalización → Dominio consumidor

## 10.4 Dimensión empresarial

ODOO → Información empresarial autorizada → Staging/Integración → Información GANUS → Indicadores/Advisory

# 11. Principios que deben quedar congelados para desarrollo

1.  GANUS es un ecosistema, no una aplicación monolítica.

2.  Cada solución tiene responsabilidad y ownership funcional propio.

3.  Los esquemas de datos de las soluciones están separados aunque compartan la misma base de datos.

4.  Una solución no debe consultar directamente tablas internas de otra solución para implementar lógica funcional.

5.  El intercambio se realiza mediante contratos y staging.

6.  El ActivoID es transversal y no se duplica entre soluciones.

7.  El operador no administra GANUS; recibe trabajo, ejecuta y captura.

8.  El móvil no planifica ni segmenta.

9.  Planificación es maestra del trabajo programado.

10. Inventario es maestro de la identidad operacional del activo.

11. TRACK es maestro de sus eventos territoriales.

12. POTREROS es productor de eventos físicos normalizados.

13. ODOO es maestro de sus procesos ERP.

14. STAGING no es fuente maestra.

15. Dashboard no crea reglas de negocio por sí mismo.

16. Advisory no inventa información.

17. La ausencia de una integración externa no debe bloquear el desarrollo; se utilizan contratos y mocks.

18. Una capacidad del ecosistema no entra automáticamente al MVP.

19. Si falta una decisión funcional, se registra como pendiente; no se inventa durante el desarrollo.

# 12. Fuera del MVP inicial

- Implementación completa de GANUS TRACK y hardware territorial avanzado.

- Implementación completa de GANUS POTREROS y automatización física como dependencia obligatoria.

- Integración profunda y sustitución de ODOO.

- Modelos predictivos avanzados.

- Agente autónomo avanzado de Advisory.

- Optimización avanzada de recursos y planificación.

- Canvas libre de dashboards.

- Marketplace o ecosistema de terceros.

- Automatizaciones no sustentadas por un caso de uso definido.

# 13. Reglas para el cerebro de desarrollo / Claude Code

Este documento es el nivel conceptual. Claude Code deberá recibir, además, los artefactos técnicos derivados. No debe convertir inferencias técnicas en decisiones funcionales.

| **Artefacto posterior** | **Contenido mínimo**                                       |
|-------------------------|------------------------------------------------------------|
| Modelo de dominio / ERD | Entidades, ownership, cardinalidades y restricciones.      |
| Diccionario de datos    | Campo, tipo, obligatoriedad, catálogo, origen y destino.   |
| Schema SQL              | Esquemas por solución, tablas, índices y constraints.      |
| Contrato de staging     | Estructura, estados, versiones, idempotencia y errores.    |
| Event Contract          | Productor, consumidor, payload, versión y trazabilidad.    |
| API Contract            | Endpoints, request/response, autenticación y errores.      |
| Offline / Sync          | Cola, reintentos, conflictos, recuperación y confirmación. |
| RBAC                    | Rol, dominio, entidad y acción.                            |
| Migration Spec          | Formato, mapping, validación y reporte de errores.         |
| Test Matrix             | Requisito → caso → prueba → resultado.                     |

# 14. Criterios conceptuales de aceptación

| **Dimensión** | **Debe poder demostrarse que...**                                                              |
|---------------|------------------------------------------------------------------------------------------------|
| Ecosistema    | Cada solución puede evolucionar sin convertirse en dependencia de las tablas internas de otra. |
| Identidad     | Un activo puede ser reconocido por múltiples identificadores y resolverse a un único ActivoID. |
| Intercambio   | Una solución puede publicar y recibir información mediante staging con estado trazable.        |
| Planificación | Lo que llega al campo fue definido y asignado previamente.                                     |
| Móvil         | El operador puede ejecutar trabajo sin conectividad y sincronizar posteriormente.              |
| Trazabilidad  | Puede reconstruirse qué se planificó, asignó, ejecutó y qué resultado se obtuvo.               |
| Integración   | TRACK, POTREROS y ODOO pueden conectarse sin rediseñar el núcleo de Operativo.                 |
| Escalabilidad | La incorporación de nuevas soluciones no exige convertirlas en parte del mismo módulo.         |

# 15. Decisiones que deben cerrarse antes del diseño técnico

- Nombre definitivo de los esquemas de cada solución.

- Qué entidades de identidad estarán físicamente en el esquema común y cuáles serán referencias.

- Modelo definitivo del staging: tablas, colas o combinación.

- Contrato de salida Operativo → Móvil.

- Contrato de entrada Móvil → Operativo.

- Contratos mínimos de TRACK, POTREROS y ODOO.

- Política de versionamiento de contratos.

- Política de idempotencia y reintentos.

- Modelo definitivo de sincronización offline.

- Modelo de almacenamiento de evidencias.

- Reglas de ownership y actualización de entidades compartidas.

# 16. Definición oficial para iniciar el diseño del MVP

GANUS es un ecosistema de soluciones especializadas conectadas por una base común de información, esquemas separados y una frontera controlada de intercambio. El MVP se concentra en GANUS OPERATIVO y demuestra el ciclo completo de organizar, administrar activos, definir captura, planificar, entregar trabajo al campo, recibir resultados, controlar la ejecución y presentar información útil para la decisión. ODOO, TRACK y POTREROS forman parte del ecosistema y se integran mediante contratos, pero sus implementaciones especializadas no deben convertirse en dependencias que bloqueen el primer release.

**CAPTURAR → INTEGRAR → CONTROLAR → ENTENDER → DECIDIR → ACTUAR**

**Nota de control:** Este documento reemplaza el enfoque de tratar el MVP como una plataforma única. El documento funcional v3.1 y sus artefactos detallados deben reinterpretarse bajo esta arquitectura antes de derivar código.
