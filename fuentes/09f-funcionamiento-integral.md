# 09F · Funcionamiento Integral v1.0

> **Fuente de solo lectura.** Convertido a Markdown desde `09_FUNCIONAMIENTO_INTEGRAL_GANUS_OPERATIVO_v1.0.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**FUNCIONAMIENTO INTEGRAL DE GANUS OPERATIVO**

Acceso · Seguridad · Contexto · Navegación · Flujos · Intercambio · Conectividad · Control · Trazabilidad

Versión 1.0 · 08 octubre 2026

**Propósito.** Definir cómo funciona GANUS como una sola aplicación empresarial B2B, desde el acceso del usuario hasta la ejecución, control, conocimiento y decisión. Este documento complementa los documentos funcionales de Organización, Inventario, Formularios, Planificación, Mobile, Seguimiento/Control/Knowledge/Advisory y Servicios Transversales.

# 0. Jerarquía y límites

La Arquitectura GANUS oficial es la autoridad para las decisiones técnicas. Este documento no la reemplaza ni redefine frameworks, patrones de infraestructura, bases de datos o despliegue. Define comportamiento funcional, experiencia, flujos, controles y contratos funcionales de intercambio.

| **Documento**                 | **Responsabilidad**                                                                               |
|-------------------------------|---------------------------------------------------------------------------------------------------|
| 00 CEREBRO GANUS              | Visión, principios, alcance, vocabulario y reglas no negociables.                                 |
| 01 ARQUITECTURA GANUS         | Cómo se construye técnicamente la plataforma.                                                     |
| 02–08                         | Capacidades funcionales por dominio y servicios transversales.                                    |
| 09 FUNCIONAMIENTO INTEGRAL    | Cómo se comporta GANUS como aplicación única y cómo fluyen usuarios, datos, trabajo y decisiones. |
| 10 MODELO BD                  | Modelo lógico/físico y propiedad de datos.                                                        |
| 11 CONSTITUCIÓN DE DESARROLLO | Reglas obligatorias para construcción y desarrollo asistido por IA.                               |

# 1. Definición de GANUS OPERATIVO

GANUS es una plataforma B2B de gestión empresarial y operacional para fincas ganaderas. Su propósito es transformar la finca en una empresa organizada, controlada, trazable y capaz de tomar decisiones basadas en datos.

**Contexto → Activo → Actividad → Dato → Regla → Conocimiento → Indicador / Alerta / Recomendación → Decisión → Acción → Nuevo dato**

Principio rector: EL OPERADOR NO ADMINISTRA GANUS. GANUS le entrega el trabajo, le facilita capturarlo y le permite informar lo extraordinario.

# 2. Principios de diseño de la aplicación

| **Principio**                            | **Regla funcional**                                                                |
|------------------------------------------|------------------------------------------------------------------------------------|
| Minimalismo                              | Mostrar solo la complejidad necesaria para la acción o decisión actual.            |
| Mínimo de clics                          | Una intención debe resolverse por la ruta más corta posible.                       |
| Contexto persistente                     | Usuario, finca, rol y permisos se reutilizan durante la sesión.                    |
| Progressive disclosure                   | Primero lo esencial; el detalle aparece cuando se solicita.                        |
| Acción contextual                        | Las acciones relevantes están disponibles desde el objeto que el usuario consulta. |
| Configurar una vez                       | Las definiciones se parametrizan y se reutilizan.                                  |
| Una sola experiencia                     | El usuario percibe GANUS como una aplicación, no como módulos independientes.      |
| Complejidad interna, simplicidad externa | La seguridad, trazabilidad e integración no deben crear fricción innecesaria.      |
| Offline-first donde corresponde          | La operación de campo no depende de conectividad permanente.                       |
| B2B por diseño                           | Aislamiento de información, permisos, auditoría y trazabilidad son estructurales.  |

# 3. Actores y experiencia

| **Actor**            | **Nivel**              | **Experiencia principal**                                                             |
|----------------------|------------------------|---------------------------------------------------------------------------------------|
| Superusuario GANUS   | Plataforma             | Backoffice de configuración técnica y gobierno de plataforma.                         |
| Dueño                | Estratégico            | Inicio, indicadores, alertas críticas, Control y Advisory.                            |
| Administrador        | Administrativo/táctico | Organización, usuarios, inventario, formularios, planificación y seguimiento.         |
| Supervisor           | Táctico                | Planificación, validación/publicación, alertas y control de ejecución.                |
| Operador             | Operativo              | Mobile, tareas, captura, resultados y alertas.                                        |
| Perfil especializado | Según responsabilidad  | Usa los permisos del rol asignado; no constituye necesariamente un rol independiente. |

Los roles del cliente son predefinidos; la configuración técnica avanzada de roles/permisos permanece en el ámbito del Superusuario GANUS.

# 4. Acceso, identidad y contexto

## 4.1 Flujo de entrada

**Login → Autenticación → Usuario identificado → Contexto autorizado → Inicio GANUS**

- Ingreso con credenciales autorizadas.

- Recuperación/activación/cambio de credenciales cuando corresponda.

- Gestión de sesión y cierre de sesión.

- Control de sesión expirada.

- Si el usuario tiene varias fincas, puede cambiar de finca dentro de sus autorizaciones.

- El contexto activo acompaña la navegación y las operaciones.

## 4.2 Contexto

**Grupo (si aplica) → Finca → Usuario → Rol → Permisos → Contexto operativo**

El sistema no debe pedir repetidamente información que ya está determinada por el contexto. Cambiar de finca cambia el alcance de las consultas y acciones posteriores.

## 4.3 Acceso y separación

- Un usuario solo puede acceder a organizaciones/fincas autorizadas.

- Ningún usuario puede consultar información de otro grupo empresarial sin autorización explícita.

- Toda operación relevante queda asociada al contexto.

- La autorización se valida en el servidor; ocultar un botón no constituye control de seguridad.

# 5. Seguridad integral

## 5.1 Seguridad de la aplicación

- Autenticación y gestión segura de sesiones.

- Autorización por usuario, rol, permiso, contexto y recurso.

- Aislamiento entre organizaciones y fincas.

- Mínimo privilegio.

- Validación de entradas y protección de APIs.

- Protección contra escalamiento de privilegios y acceso horizontal indebido.

- Auditoría de accesos y operaciones administrativas/críticas.

- Errores sin exposición innecesaria de información sensible.

- Control de acceso a archivos y evidencias.

## 5.2 Seguridad de base de datos

- La aplicación es la vía normal de acceso; Web y Mobile no acceden directamente a PostgreSQL.

- Credenciales y secretos no deben estar embebidos en código.

- Principio de mínimo privilegio para cuentas de BD.

- Integridad referencial y restricciones de datos.

- Migraciones versionadas.

- Backups y recuperación protegidos.

- Separación de propiedad por dominio.

- Un dominio no modifica directamente tablas de otro dominio fuera de los mecanismos definidos.

## 5.3 Seguridad de intercambio

- Intercambio productivo cifrado mediante canales seguros.

- Autenticación de aplicaciones/sistemas y usuarios.

- Identificación de origen y contexto.

- Validación de esquema/versiones.

- Idempotencia para evitar duplicación.

- CorrelationID/identificador de mensaje para trazabilidad.

- Reintentos controlados y tratamiento de mensajes no procesables.

- No compartir credenciales humanas para integraciones sistema-a-sistema.

## 5.4 Seguridad offline y dispositivo

- La base local Android debe estar protegida/cifrada mediante mecanismos apropiados del sistema.

- Tokens/credenciales no deben almacenarse en texto plano.

- El paquete offline debe contener solo la información necesaria para las tareas autorizadas.

- Las evidencias locales deben protegerse hasta su sincronización.

- El cierre de sesión o desautorización debe impedir nuevas operaciones cuando corresponda.

## 5.5 Seguridad de archivos

- El archivo no se considera público por conocer una referencia.

- El acceso valida usuario, permiso, contexto, entidad y autorización del archivo.

- Se conserva metadata y trazabilidad.

- El almacenamiento pesado se gestiona separadamente de la información transaccional cuando la arquitectura oficial así lo determine.

# 6. Application Shell y experiencia única

GANUS debe sentirse como una sola aplicación. El Shell común concentra navegación, contexto, búsqueda, notificaciones, perfil y estado de conectividad; cada dominio aporta su contenido y lógica.

| **Elemento**     | **Comportamiento**                          |
|------------------|---------------------------------------------|
| Contexto         | Siempre visible o fácilmente identificable. |
| Navegación       | Adaptada al rol y al trabajo autorizado.    |
| Búsqueda         | Disponible transversalmente.                |
| Notificaciones   | Accesibles desde cualquier punto.           |
| Conectividad     | Estado visible, especialmente en Mobile.    |
| Acción principal | Visualmente priorizada.                     |
| Errores          | Mensajes accionables y comprensibles.       |
| Ayuda            | Contextual, no invasiva.                    |

# 7. Navegación

La navegación no debe reflejar la estructura técnica interna. Debe reflejar las necesidades del usuario.

| **Perfil**      | **Navegación base**                                           |
|-----------------|---------------------------------------------------------------|
| Dueño           | Inicio · Control · Indicadores · Advisory                     |
| Administrador   | Inicio · Inventario · Planificación · Control · Configuración |
| Supervisor      | Inicio · Trabajo · Inventario · Planificación · Control       |
| Operador Mobile | Tareas · Alertas · Historial                                  |

La navegación móvil mantiene el diseño simplificado acordado: Tareas, Alertas e Historial. No se crea una navegación independiente para Novedades; estas se absorben en Alertas como tipo correspondiente.

# 8. Inicio / Home

El Home no es un depósito de gráficas. Es el punto de orientación del usuario.

- Qué está pasando.

- Qué requiere atención.

- Qué trabajo está pendiente.

- Qué cambió desde la última entrada.

- Acceso directo a la acción prioritaria.

- Resumen proporcional al rol.

**Inicio → Situación / trabajo relevante → Acción → Resultado**

# 9. Búsqueda global

La búsqueda transversal permite localizar información autorizada sin recorrer menús.

- Activos.

- Personas/usuarios.

- Tareas.

- Formularios.

- Espacios.

- Documentos/evidencias.

- Alertas.

- Situaciones.

- Resultados.

Toda búsqueda respeta contexto y permisos. La búsqueda no constituye una nueva fuente maestra de información.

# 10. Capacidades funcionales integradas

| **Dominio**   | **Responsabilidad dentro de la experiencia**                                                                                 |
|---------------|------------------------------------------------------------------------------------------------------------------------------|
| Organización  | Contexto empresarial, fincas, usuarios, roles, permisos, estructura física, procesos y actividades.                          |
| Inventario    | Identidad única de activos, atributos, relaciones, ubicación, documentos, historial y ciclo de vida.                         |
| Formularios   | Define contenido exacto de captura, claves, contexto, campos, validaciones y versiones.                                      |
| Planificación | Convierte necesidad en trabajo: calendario, tarea, dataset, validación, publicación, control.                                |
| Mobile        | Ejecuta el paquete recibido, captura resultados, evidencias, excepciones y alertas offline.                                  |
| Control       | Muestra avance, cumplimiento, calidad, tiempos, capacidad, productividad y alertas.                                          |
| Knowledge     | Gobierna reglas, indicadores, situaciones y conocimiento estructurado.                                                       |
| Advisory      | Interpreta contexto y recomienda acciones explicables.                                                                       |
| Transversales | Shell, sesión, navegación, búsqueda, notificaciones, auditoría, archivos, importación/exportación, estados y sincronización. |

# 11. Flujo principal de operación

**Configurar → Inventario → Definir captura → Planificar → Validar → Publicar → STAGING → Mobile → Ejecutar → Sincronizar → Controlar → Entender → Decidir**

La cadena funcional debe ser continua. El usuario no debe reconstruir manualmente el contexto entre módulos.

# 12. Acciones contextuales y mínimo de clics

- Desde un activo se puede iniciar una acción permitida sobre ese activo sin volver a buscarlo.

- Desde una tarea se accede directamente a su formulario, dataset, ejecución, resultados y evidencias.

- Desde una alerta se puede consultar la situación y, cuando corresponda, iniciar/solicitar una tarea.

- Desde un indicador se puede hacer drill-down hasta situación, datos, activos/evidencia y recomendación.

- Cuando una configuración ya determina un valor, GANUS lo precarga en lugar de solicitarlo nuevamente.

Regla: no crear una pantalla adicional únicamente para transportar al usuario de una pantalla a otra si la acción puede ejecutarse contextualmente.

# 13. Planificación → Mobile → Control

**Calendario → Tarea → Formulario publicado → Dataset calculado → Resumen/validación → Confirmar → STAGING → Paquete Mobile → Ejecución → Resultado por activo → STAGING → Control**

Antes de publicar, el sistema muestra al administrador/supervisor qué se hará, sobre qué población, con qué formulario, cuándo, quién lo ejecutará y las condiciones relevantes.

El Mobile no cambia la población objetivo. Solo trabaja sobre el paquete recibido. La segmentación ocurre en Planificación.

# 14. STAGING e intercambio funcional

STAGING es la frontera controlada de intercambio entre el centro de operación y Mobile y sirve como punto de integración definido para los contratos autorizados.

## 14.1 Centro → campo

| **Elemento**             | **Propósito**                                          |
|--------------------------|--------------------------------------------------------|
| TaskID                   | Identificación de tarea.                               |
| FormID + versión         | Determina exactamente qué se captura.                  |
| Dataset                  | Población/objetos sobre los que se trabaja.            |
| Contexto                 | Finca, proceso, actividad y demás contexto autorizado. |
| Condiciones              | Reglas operativas que deben cumplirse.                 |
| Preloads                 | Valores ya conocidos por GANUS.                        |
| Programación/responsable | Quién y cuándo.                                        |
| Metadatos                | Trazabilidad y control.                                |

## 14.2 Campo → centro

| **Elemento**         | **Propósito**                                      |
|----------------------|----------------------------------------------------|
| Resultado            | Datos capturados.                                  |
| Resultado por activo | Éxito, inconsistencia, no ejecución o cancelación. |
| Evidencia            | Foto, documento, video, audio, firma, etc.         |
| Excepción            | Situación que impide o modifica la ejecución.      |
| Alerta               | Situación comunicada al centro.                    |
| Estado               | Progreso de tarea/resultado.                       |
| Metadatos            | Usuario, tiempo, contexto y trazabilidad.          |

# 15. Sincronización y conectividad

- Mobile funciona sin conectividad una vez que el paquete necesario está disponible localmente.

- La información se almacena localmente y se sincroniza posteriormente.

- La sincronización debe ser incremental y no descargar repetidamente información sin cambios.

- Cada elemento debe tener estado de sincronización: pendiente, en cola, enviando, enviado, confirmado, error temporal o error que requiere atención.

- Los mensajes se procesan de forma idempotente.

- Las alertas tienen prioridad de transmisión cuando existe conectividad.

- Un corte de conectividad no debe destruir una captura ya confirmada localmente.

**STAGING → Mobile → paquete local → ejecución offline → resultados locales → cola → STAGING → procesamiento**

# 16. Mobile: captura y voz

Voice es un mecanismo primario de captura, no una definición de formulario. Formularios define el dato; Mobile decide cómo capturarlo.

**Voz → Reconocimiento → Interpretación contextual → Normalización → Validación → Confirmación → Valor estructurado**

- La captura convencional siempre está disponible.

- Una interpretación ambigua o inválida no debe guardarse silenciosamente.

- El usuario puede repetir/corregir.

- Ejemplo funcional: una expresión verbal de peso debe convertirse en un valor numérico validado antes de almacenarse.

## 16.1 Alerta persistente

- Botón ALERTA visible desde cualquier pantalla.

- Permite video, nota de voz, foto o texto.

- Puede incluir prioridad y contexto.

- Se envía con prioridad mediante STAGING cuando hay conexión.

- Si está offline, se almacena localmente y se transmite al recuperar conexión.

# 17. Estados y ciclos

| **Objeto**           | **Estados principales**                                                                                                                                |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tarea                | Borrador → Validada → Programada → Disponible para campo → En ejecución → Pausada → Finalizada → Por validar → Cerrada; Cancelada como salida alterna. |
| Resultado por activo | Pendiente → En ejecución → Ejecutado exitosamente / Ejecutado con inconsistencia / No ejecutado / Cancelado.                                           |
| Formulario           | Borrador → Publicado → Obsoleto.                                                                                                                       |
| Sincronización       | Pendiente local → En cola → Enviando → Enviado → Confirmado / Error temporal / Requiere atención.                                                      |
| Situación            | Normal / Desviación / Incumplimiento / Riesgo / Oportunidad / Tendencia / Datos insuficientes.                                                         |

# 18. Alertas, notificaciones y situaciones

Una alerta es una situación priorizada; no es sinónimo de tarea. Una situación puede originar una recomendación y una tarea, pero Planning continúa siendo dueño de la tarea.

**Dato / evento → Regla → Situación → Alerta o recomendación → Acción / tarea**

- Notificación comunica.

- Alerta prioriza una situación.

- Tarea representa trabajo ejecutable.

- Los tres mantienen relación con su origen y trazabilidad.

# 19. Knowledge y Advisory dentro del flujo

**Contexto → Regla → Indicador → Situación → Recomendación → Acción**

Knowledge estructura y gobierna conocimiento; Advisory interpreta y recomienda. Los cálculos críticos deben ser deterministas. El lenguaje generado por IA no modifica directamente sistemas transaccionales.

# 20. Auditoría y trazabilidad

Toda acción administrativa, de seguridad o crítica debe dejar trazabilidad suficiente para reconstruir el hecho.

| **Dato mínimo** | **Descripción**                           |
|-----------------|-------------------------------------------|
| Timestamp       | Momento de la acción.                     |
| Usuario         | Quién la ejecutó.                         |
| Contexto/Finca  | Dónde aplica.                             |
| Acción          | Qué hizo.                                 |
| Entidad/ID      | Sobre qué objeto.                         |
| Antes/Después   | Cambio relevante cuando aplique.          |
| Origen          | Web, Mobile, integración, automatización. |
| CorrelationID   | Relación con el flujo/transacción.        |

La auditoría transversal no reemplaza los historiales funcionales de cada dominio.

# 21. Errores, excepciones y recuperación

| **Situación**        | **Comportamiento esperado**                                                          |
|----------------------|--------------------------------------------------------------------------------------|
| Sin conexión         | Informar estado; conservar trabajo local cuando aplique; sincronizar después.        |
| Error de validación  | Indicar qué debe corregirse y por qué.                                               |
| Permiso insuficiente | Informar que la acción no está autorizada sin revelar información indebida.          |
| Conflicto            | No sobrescribir silenciosamente; mostrar situación y permitir recuperación definida. |
| Integración caída    | Aislar el error; conservar trazabilidad y reintentar según política.                 |
| Mensaje duplicado    | Reconocer idempotencia y evitar doble procesamiento.                                 |
| Dato no encontrado   | Mostrar estado comprensible y alternativa contextual.                                |
| Error de sistema     | No perder información confirmada; registrar y permitir recuperación.                 |

# 22. Integraciones externas

Las integraciones se realizan mediante los contratos definidos para cada frontera. GANUS no debe depender de acceso directo a tablas internas de sistemas externos.

| **Sistema**         | **Papel funcional**                                                                                         |
|---------------------|-------------------------------------------------------------------------------------------------------------|
| ODOO / GANUS CORE   | Dimensión empresarial/ERP e intercambio de información autorizada.                                          |
| n8n                 | Orquestación de integraciones y automatizaciones externas; no es dueño de la lógica transaccional de GANUS. |
| RFID / dispositivos | Origen de eventos físicos/identificación según capacidades contratadas.                                     |
| GANUS TRACK         | Eventos territoriales/posición/geocercas cuando corresponda al ecosistema.                                  |

# 23. Eficiencia de recursos

- No cargar información que la pantalla no necesita.

- Usar paginación y carga progresiva en conjuntos grandes.

- Evitar consultas repetitivas cuando el contexto o caché pueda reutilizarse sin comprometer consistencia.

- Mobile recibe paquetes mínimos y sincronización incremental.

- Las evidencias se optimizan antes/durante el almacenamiento según política.

- No mantener conexiones o procesos persistentes sin necesidad.

- El diseño debe considerar CPU, memoria, batería, almacenamiento y consumo de datos.

La eficiencia es parte del diseño B2B: reduce costo de infraestructura y hace viable la operación rural.

# 24. UX B2B: reglas no negociables

1.  Una pantalla debe tener una acción principal claramente identificable.

2.  Evitar pasos de selección que GANUS ya puede determinar por contexto.

3.  No pedir al operador datos administrativos que el sistema ya conoce.

4.  No duplicar información maestra entre módulos.

5.  No obligar al usuario a regresar al menú para continuar un flujo relacionado.

6.  Usar el mismo patrón visual y de interacción en todos los dominios.

7.  Mostrar estado y siguiente acción.

8.  Los errores deben explicar cómo continuar.

9.  El detalle avanzado debe aparecer bajo demanda.

10. El sistema debe ser rápido aun cuando procese grandes volúmenes.

# 25. Flujos end-to-end de aceptación

## 25.1 Usuario → operación

**Login → Contexto → Inicio → Buscar activo → Ficha → Acción contextual → Tarea → Ejecución → Resultado**

## 25.2 Actividad de campo

**Planificación → Formulario + Dataset → Validación → Publicación → STAGING → Mobile → Offline/Online → Resultado por activo → Control**

## 25.3 Alerta de campo

**Operador → ALERTA → STAGING → Centro de Control → Supervisor → Nueva tarea → Mobile → Resultado**

## 25.4 Inteligencia → acción

**Datos → Indicador → Situación → Advisory → Recomendación → Planificación → Tarea → Mobile → Nuevo dato**

## 25.5 Seguridad

**Usuario → Autenticación → Contexto → Permiso → Acción → Backend valida → Datos autorizados → Auditoría**

# 26. Criterios de aceptación global

- El usuario inicia sesión y llega a un contexto válido sin configurar manualmente información que GANUS ya conoce.

- La navegación visible corresponde al rol y permisos.

- Un usuario no puede acceder a datos de otra organización/finca fuera de su autorización.

- Una acción contextual evita búsquedas o navegación innecesarias.

- Una tarea publicada mantiene formulario, versión, dataset y contexto trazables.

- Mobile puede ejecutar una tarea descargada sin conectividad.

- Una captura confirmada localmente no se pierde por interrupción de red.

- Los resultados se procesan sin duplicación mediante idempotencia.

- El resultado de cada activo es identificable individualmente.

- Una alerta puede llegar al centro aun cuando haya sido generada desde Mobile offline.

- El usuario puede navegar desde indicador/situación hasta datos, activos, evidencia y recomendación.

- Las acciones críticas quedan auditadas.

- Los archivos y evidencias respetan permisos y contexto.

- Las integraciones no requieren acceso directo de usuario a bases externas.

- El usuario percibe una sola aplicación coherente y no módulos desconectados.

- El sistema minimiza consumo innecesario de red, almacenamiento, CPU, memoria y batería.

# 27. Reglas de frontera entre documentos

| **Tema**                        | **Dónde se define**                                                                                        |
|---------------------------------|------------------------------------------------------------------------------------------------------------|
| Qué debe hacer el usuario       | Este documento + documentos funcionales 02–08.                                                             |
| Cómo se construye técnicamente  | 01 Arquitectura GANUS.                                                                                     |
| Propiedad y estructura de datos | 10 Modelo BD.                                                                                              |
| Contratos técnicos detallados   | Artefacto de contratos/integraciones técnico correspondiente, subordinado a este comportamiento funcional. |
| Reglas de desarrollo            | 11 Constitución de Desarrollo.                                                                             |
| Reglas de negocio               | Knowledge / dominio correspondiente.                                                                       |
| Identidad del activo            | Inventario.                                                                                                |
| Dataset y población             | Planificación.                                                                                             |
| Captura                         | Formularios + Mobile según frontera definida.                                                              |
| Ejecución de campo              | Mobile.                                                                                                    |
| Control e interpretación        | Seguimiento / Knowledge / Advisory.                                                                        |

# 28. Regla de cierre

**GANUS OPERATIVO estará correctamente construido cuando el usuario pueda entrar una sola vez, trabajar dentro de su contexto, encontrar información sin recorrer innecesariamente el sistema, recibir el trabajo que debe realizar, ejecutarlo aun con conectividad limitada, informar excepciones, recibir control y orientación, y convertir los resultados en decisiones, todo con seguridad, trazabilidad y una experiencia mínima en clics.**

Documento: 09 · Funcionamiento Integral de GANUS Operativo · v1.0
