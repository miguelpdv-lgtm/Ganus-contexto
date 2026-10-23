# 01 · Arquitectura de referencia v1.1 (ARQ)

> **Fuente de solo lectura.** Convertido a Markdown desde `GANUS_Arquitectura_Referencia_v1.1_Evaluacion_y_Base_Oficial.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

**GANUS**

**ARQUITECTURA DE REFERENCIA**

*Evaluación arquitectónica y base normativa para el desarrollo de GANUS OPERATIVO*

Versión 1.1 · Documento de referencia para equipo de desarrollo y Claude Code

# 0. Decisión ejecutiva

**Dictamen:** APROBADO COMO ARQUITECTURA DE REFERENCIA PARA EL MVP DE GANUS OPERATIVO.

La arquitectura definida presenta un equilibrio adecuado entre velocidad de construcción, simplicidad operacional, separación de responsabilidades, capacidad de integración y evolución futura. No se recomienda rediseñarla antes de iniciar el diseño funcional. A partir de esta versión, la arquitectura debe considerarse congelada como referencia y cualquier excepción debe registrarse mediante una decisión arquitectónica explícita.

| **Dimensión evaluada**     | **Dictamen** | **Razón**                                                                                                                        |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------------------|
| Adecuación al MVP          | ALTA         | Evita complejidad prematura y permite construir rápido.                                                                          |
| Evolución futura           | ALTA         | Las fronteras de dominio permiten extraer servicios posteriormente.                                                              |
| Integración del ecosistema | ALTA         | API-first + contratos + staging desacoplan soluciones.                                                                           |
| Gobierno del dato          | ALTA         | Ownership y schemas evitan acoplamiento por tablas.                                                                              |
| Costo-beneficio            | ALTA         | No introduce infraestructura distribuida sin necesidad.                                                                          |
| Mantenibilidad             | ALTA         | Hexagonal/Clean separa dominio de tecnología.                                                                                    |
| Operación en campo         | ALTA         | La arquitectura contempla offline, sincronización e idempotencia.                                                                |
| Riesgo técnico             | CONTROLADO   | Los riesgos principales se trasladan a contratos, seguridad, migraciones y sincronización, que quedan explícitamente gobernados. |

# 1. Qué decide este documento

Este documento no vuelve a definir el alcance funcional de GANUS. Establece las condiciones bajo las cuales ese alcance debe ser diseñado y construido. Es, por tanto, la referencia arquitectónica entre la visión conceptual y los diseños funcionales/técnicos posteriores.

- Define el estilo arquitectónico.

- Define la estructura interna del software.

- Define las fronteras de dominio que el código debe respetar.

- Define la estrategia de datos.

- Define la estrategia de integración.

- Define principios de seguridad, pruebas, observabilidad y despliegue.

- Define qué complejidad NO se debe introducir en el MVP.

- Define cómo debe evolucionar la solución sin rehacer el núcleo.

# 2. Decisión arquitectónica central

**GANUS OPERATIVO se construirá como:** MODULAR MONOLITH + HEXAGONAL/CLEAN ARCHITECTURE + API-FIRST + EVENT-DRIVEN INTEGRATION.

La aplicación se despliega inicialmente como una unidad operativa coherente, pero su código se organiza por dominios con fronteras estrictas. Las interacciones con la aplicación móvil y soluciones externas se realizan mediante contratos explícitos y una frontera de intercambio. Esta decisión minimiza complejidad en el MVP sin cerrar la posibilidad de evolución hacia servicios independientes.

# 3. Evaluación: por qué esta arquitectura es la adecuada

| **Criterio**          | **Alternativa descartada**          | **Decisión**                      | **Evaluación**                                                    |
|-----------------------|-------------------------------------|-----------------------------------|-------------------------------------------------------------------|
| Velocidad de MVP      | Microservicios desde día 1          | Modular Monolith                  | Menor carga operacional y mayor velocidad de iteración.           |
| Separación de negocio | Backend en capas técnicas genéricas | Hexagonal/Clean                   | Las reglas permanecen independientes del framework.               |
| Integración           | Acceso directo a tablas             | Contratos + STAGING               | Reduce acoplamiento y facilita evolución.                         |
| Escalabilidad         | Distribuir todo desde el inicio     | Escalar donde sea necesario       | Se paga complejidad solo cuando existe necesidad.                 |
| Datos                 | BD separadas físicamente por módulo | BD común + schemas                | Alinea la visión del ecosistema con una implementación eficiente. |
| Frontend              | Microfrontends                      | Angular modular                   | Evita complejidad de despliegue sin perder separación.            |
| Automatización        | n8n como backend                    | n8n como orquestador              | Protege la consistencia transaccional.                            |
| Eventos               | Streaming complejo                  | Eventos/colas donde aporten valor | Adecuado al volumen y madurez del MVP.                            |
| Infraestructura       | Kubernetes desde inicio             | Docker + infraestructura simple   | Mejor relación costo-beneficio.                                   |

Conclusión: la arquitectura no busca ser la más sofisticada técnicamente. Busca ser la más adecuada para el problema actual y conservar una ruta de evolución clara. Esa es la decisión arquitectónica correcta para GANUS en esta fase.

# 4. Estilo de arquitectura de software

## 4.1 Modular Monolith

Un único backend desplegable contiene varios módulos de negocio. Cada módulo tiene sus propios casos de uso, dominio, persistencia y contratos internos. La comunicación entre módulos debe ocurrir mediante interfaces/casos de uso y no mediante acceso indiscriminado a tablas o repositorios.

### Ventajas para GANUS

- Desarrollo y despliegue simples.

- Menor costo de infraestructura.

- Transacciones locales más sencillas.

- Debugging y observabilidad más simples.

- Menor latencia entre dominios internos.

- Permite consolidar el modelo funcional durante el MVP.

- Puede evolucionar a servicios cuando exista una razón objetiva.

## 4.2 Hexagonal / Clean Architecture

El negocio debe estar protegido de las decisiones tecnológicas. NestJS, PostgreSQL, APIs, almacenamiento, n8n y proveedores externos son adaptadores, no el centro del modelo.

| **Capa**       | **Regla**                                                        |
|----------------|------------------------------------------------------------------|
| Domain         | No depende de framework, ORM o proveedor.                        |
| Application    | Implementa casos de uso y coordina el dominio.                   |
| Adapters       | Traducen HTTP, eventos, staging y otras entradas/salidas.        |
| Infrastructure | Implementa persistencia, servicios externos y detalles técnicos. |

# 5. Principio de arquitectura: fronteras antes que servicios

**Regla obligatoria:** primero se diseñan correctamente los límites del dominio; después se decide si algún límite merece convertirse en un servicio independiente.

Un módulo puede convertirse en servicio cuando exista una razón objetiva:

- necesidad de escalamiento independiente;

- ciclo de despliegue independiente;

- aislamiento de fallos;

- requisitos de seguridad o infraestructura diferenciados;

- equipo/ownership independiente;

- carga de trabajo significativamente diferente.

Nunca separar físicamente un módulo solamente porque técnicamente sea posible.

# 6. Arquitectura lógica

**CLIENTES / INTEGRACIONES  
Web · Móvil · ODOO · TRACK · POTREROS  
↓  
API / EVENTOS / STAGING  
↓  
ADAPTERS / CONSUMERS  
↓  
APPLICATION USE CASES  
↓  
DOMAIN  
↓  
PORTS  
↓  
INFRASTRUCTURE ADAPTERS  
↓  
POSTGRESQL / STORAGE / EXTERNAL SYSTEMS**

# 7. Fronteras internas de GANUS OPERATIVO

| **Módulo**            | **Regla arquitectónica**                                     |
|-----------------------|--------------------------------------------------------------|
| Organization          | Dueño de contexto, gobierno y seguridad funcional.           |
| Inventory             | Dueño de identidad y estado/historial de activos.            |
| Forms                 | Dueño de definición y versionamiento de captura.             |
| Planning              | Dueño del trabajo planificado.                               |
| Staging               | Dueño del intercambio técnico y trazabilidad de integración. |
| Execution / Follow-up | Dueño del control operacional de ejecución.                  |
| Reporting             | Modelos de lectura; no fuente maestra.                       |
| Advisory              | Interpretación y recomendación sobre información autorizada. |

Las fronteras anteriores deben reflejarse en código, dependencias, schemas y pruebas.

# 8. Arquitectura de datos

PostgreSQL será la base transaccional del núcleo. La base común del ecosistema no elimina la separación lógica de ownership.

| **Schema**   | **Responsabilidad**                                 |
|--------------|-----------------------------------------------------|
| organization | Contexto organizacional y configuración.            |
| inventory    | Activos y datos propios de inventario.              |
| forms        | Definiciones, versiones y catálogos de formularios. |
| planning     | Trabajos, programación y asignaciones.              |
| staging      | Mensajes, intercambio, estados y trazabilidad.      |
| audit        | Auditoría cuando corresponda.                       |
| reporting    | Modelos de lectura derivados; nunca fuente maestra. |

Las entidades maestras pertenecen a un solo dominio. Los demás dominios trabajan con referencias y contratos. Las dependencias físicas entre schemas deben mantenerse al mínimo y justificarse.

# 9. Code First + migraciones

Se adopta Code First como mecanismo de modelado de aplicación, acompañado obligatoriamente de migraciones versionadas. No se permite sincronización automática del ORM en producción.

- Cada cambio de estructura debe producir una migración revisable.

- Las migraciones deben probarse antes de producción.

- Los cambios destructivos requieren plan de migración de datos.

- El despliegue ejecuta migraciones controladas.

- Debe existir estrategia de rollback o recuperación.

- El esquema de producción no se modifica manualmente como práctica normal.

# 10. STAGING: decisión arquitectónica crítica

STAGING es la frontera de integración del ecosistema. No es un depósito genérico ni un bypass de los dominios.

| **Componente**         | **Función**                                                       |
|------------------------|-------------------------------------------------------------------|
| Inbox                  | Entrada confiable e idempotente.                                  |
| Outbox                 | Salida registrada de forma consistente con la transacción origen. |
| Processor              | Validación y procesamiento.                                       |
| Retry                  | Recuperación de fallos transitorios.                              |
| Rejected / Dead Letter | Conservar información que no puede procesarse.                    |
| Audit Metadata         | Origen, destino, versión, timestamps, correlation_id.             |

La implementación física puede evolucionar desde tablas en PostgreSQL hacia mecanismos de mensajería si el volumen o criticidad futura lo justifican. El contrato funcional no debe depender del mecanismo físico.

# 11. API-first y contratos

- La API representa capacidades, no tablas.

- No exponer entidades ORM directamente.

- Usar DTOs explícitos.

- Validar en el borde.

- Versionar cambios incompatibles.

- Usar correlation_id.

- Definir idempotencia para operaciones repetibles.

- Documentar errores y estados.

- Los contratos deben existir antes de integrar sistemas externos.

# 12. n8n: frontera correcta

| **Sí**                           | **No**                                   |
|----------------------------------|------------------------------------------|
| Workflows externos               | Transacciones críticas                   |
| Notificaciones                   | Fuente de verdad                         |
| Conectores SaaS                  | Autorización central                     |
| Procesos programados no críticos | Estado maestro del inventario            |
| Transformaciones simples         | Reglas que requieran consistencia fuerte |

Si n8n está indisponible, GANUS debe continuar operando en todo aquello que no dependa funcionalmente de ese workflow. La integración debe ser recuperable.

# 13. Frontend Angular

Angular se organiza por features/dominios. No se recomienda microfrontend en el MVP.

- Feature-first.

- Componentes reutilizables.

- Estado global solo cuando exista necesidad transversal.

- Clientes API centralizados y basados en contratos.

- La UI refleja permisos, pero la seguridad real vive en backend.

- El diseño visual debe utilizar un sistema de componentes consistente.

# 14. Aplicación móvil

La App Móvil es un consumidor independiente de contratos. La arquitectura debe permitir operación offline sin modificar la lógica central del backend.

| **Capacidad técnica** | **Regla**                                                                         |
|-----------------------|-----------------------------------------------------------------------------------|
| Work package          | Versionado y contextualizado.                                                     |
| Persistencia local    | Debe soportar ejecución sin conexión.                                             |
| Sync queue            | Toda operación pendiente debe ser identificable.                                  |
| Idempotencia          | Una misma captura no debe procesarse dos veces.                                   |
| Conflictos            | Deben tener una política explícita.                                               |
| Confirmación          | El móvil debe distinguir guardado local de procesamiento confirmado por servidor. |

# 15. Seguridad por diseño

- Aislamiento por grupo/finca en backend.

- Mínimo privilegio.

- Autorización por caso de uso.

- Secrets fuera del repositorio.

- TLS para comunicaciones.

- PostgreSQL de producción no expuesto públicamente.

- Backups protegidos y restauración probada.

- Auditoría de operaciones administrativas y críticas.

- Protección contra abuso en endpoints públicos.

- No confiar en controles únicamente de frontend.

# 16. Despliegue e infraestructura

| **Ambiente** | **Criterio**                                                                   |
|--------------|--------------------------------------------------------------------------------|
| Local        | Simple y reproducible.                                                         |
| Development  | VPS de bajo costo es adecuado para iniciar.                                    |
| Staging      | Debe aproximarse a producción para validar integraciones y migraciones.        |
| Production   | AWS/Azure cuando se requiera; PostgreSQL privado; aplicaciones contenerizadas. |

Docker es estándar de empaquetado. La infraestructura no debe introducir dependencia cloud específica en el dominio.

# 17. Código, GitHub y CI/CD

- Repositorio privado bajo organización empresarial.

- main protegida.

- Pull Request obligatorio.

- Checks automáticos.

- Ramas cortas por feature/fix.

- CODEOWNERS para áreas críticas.

- Lint + typecheck + unit tests + integration tests en CI.

- Build de imágenes reproducibles.

- Migraciones ejecutadas de forma controlada.

- No secretos en repositorio.

# 18. Monorepo recomendado

**ganus-operativo/  
├── apps/api  
├── apps/web  
├── packages/contracts  
├── packages/ui  
├── packages/shared  
├── database  
├── docs  
└── infra**

El monorepo es una recomendación para el MVP porque facilita versionamiento conjunto, contratos compartidos, CI/CD y coordinación. No elimina las fronteras de dominio.

# 19. Observabilidad

| **Elemento**   | **Mínimo obligatorio**                                   |
|----------------|----------------------------------------------------------|
| Logs           | Estructurados.                                           |
| Correlation ID | Transversal API → staging → procesos.                    |
| Health checks  | Liveness + readiness.                                    |
| Métricas       | Latencia, errores, throughput, sync y procesos críticos. |
| Auditoría      | Cambios administrativos y operaciones críticas.          |
| Errores        | Clasificación y trazabilidad sin secretos.               |

# 20. Calidad y estrategia de pruebas

| **Tipo**     | **Objetivo**                                       |
|--------------|----------------------------------------------------|
| Unit         | Reglas de dominio.                                 |
| Application  | Casos de uso.                                      |
| Integration  | Persistencia, staging y adapters.                  |
| API          | Contratos, validación y autorización.              |
| E2E          | Flujos críticos.                                   |
| Contract     | Compatibilidad entre productores/consumidores.     |
| Migration    | Evolución de BD.                                   |
| Offline/Sync | Duplicados, reintentos, conflictos y recuperación. |

Definition of Done debe exigir prueba apropiada para cada cambio funcional.

# 21. Principios de desarrollo obligatorios

1.  Primero el dominio; después el framework.

2.  Las fronteras de módulo deben existir en el código.

3.  Una entidad maestra tiene un solo owner.

4.  Los módulos se comunican mediante contratos, no mediante acceso directo a repositories ajenos.

5.  API representa capacidades, no tablas.

6.  STAGING representa intercambio, no negocio.

7.  Eventos deben ser idempotentes.

8.  Las transacciones críticas permanecen dentro del dominio dueño.

9.  Eventual consistency solo cuando el negocio la tolere.

10. Offline-first para campo.

11. Seguridad y auditoría desde el primer release.

12. Observabilidad desde el primer release.

13. Automatización CI/CD desde el primer release.

14. Complejidad distribuida solo cuando aporte valor medible.

15. Reutilizar soluciones existentes cuando no exista diferenciación GANUS.

16. No construir funcionalidad futura de manera especulativa.

17. No colocar lógica crítica en n8n.

18. No usar ORM auto-sync en producción.

19. No utilizar reporting como fuente transaccional.

20. Los cambios arquitectónicos deben registrarse como ADR.

# 22. Decisiones que quedan congeladas

| **ADR** | **Decisión congelada**                                                            |
|---------|-----------------------------------------------------------------------------------|
| ADR-001 | Modular Monolith para MVP.                                                        |
| ADR-002 | Hexagonal/Clean Architecture.                                                     |
| ADR-003 | NestJS + TypeScript para backend.                                                 |
| ADR-004 | Angular modular para web.                                                         |
| ADR-005 | PostgreSQL como base transaccional.                                               |
| ADR-006 | Schemas lógicos por dominio.                                                      |
| ADR-007 | Code First + migraciones versionadas.                                             |
| ADR-008 | API-first.                                                                        |
| ADR-009 | STAGING con Inbox/Outbox e idempotencia.                                          |
| ADR-010 | n8n como orquestador externo, no como core transaccional.                         |
| ADR-011 | Docker como estándar de empaquetado/despliegue.                                   |
| ADR-012 | GitHub privado + PR + CI/CD.                                                      |
| ADR-013 | Monorepo para el MVP.                                                             |
| ADR-014 | No microservicios/microfrontends en el primer release salvo decisión justificada. |

# 23. Decisiones que NO deben congelarse todavía

Estas decisiones deben derivarse del diseño funcional y de las pruebas, no anticiparse:

- Tecnología definitiva de la App Móvil.

- Mecanismo físico final de mensajería/event streaming.

- Escalamiento horizontal del backend.

- Separación física de servicios.

- Proveedor definitivo de observabilidad.

- Motor específico de búsqueda si llegara a ser necesario.

- Estrategia avanzada de data lake/analytics.

- Infraestructura definitiva de producción a gran escala.

# 24. Riesgos arquitectónicos controlados

| **Riesgo**                        | **Control**                                      |
|-----------------------------------|--------------------------------------------------|
| Acoplamiento entre módulos        | Fronteras + ports/adapters + ownership.          |
| Duplicación de maestros           | Fuente única + IDs transversales.                |
| Corrupción de datos por sync      | Idempotencia + estados + outbox/inbox.           |
| Cambios de BD inseguros           | Migrations versionadas.                          |
| Dependencia de n8n                | Lógica crítica dentro del core.                  |
| Complejidad prematura             | No microservicios/microfrontends iniciales.      |
| Fallo de conectividad de campo    | Offline-first.                                   |
| Integraciones externas inestables | Contratos + retry + dead-letter.                 |
| Dificultad de diagnóstico         | Logs estructurados + correlation ID.             |
| Crecimiento futuro                | Fronteras de dominio preparadas para extracción. |

# 25. Qué debe recibir el equipo antes de programar cada módulo

- Diseño funcional aprobado.

- Entidades y ownership.

- Casos de uso.

- Estados y transiciones.

- Reglas de negocio.

- Entradas y salidas.

- Contrato de integración si aplica.

- Criterios de aceptación.

- Impacto en schemas.

- Pruebas esperadas.

# 26. Relación con el diseño funcional

La arquitectura no sustituye el documento funcional. El diseño funcional debe decidir qué hace el producto; esta arquitectura decide cómo debe construirse para soportarlo.

**VISIÓN → ARQUITECTURA → DISEÑO FUNCIONAL → DISEÑO TÉCNICO → DESARROLLO**

# 27. Criterio de aprobación arquitectónica

Una propuesta de diseño o implementación se considera compatible con esta arquitectura si mantiene las fronteras de dominio, el ownership, los contratos, la seguridad, la trazabilidad y los principios de evolución definidos aquí, y si no introduce complejidad distribuida sin una necesidad demostrable.

# 28. Dictamen final

**APROBACIÓN:** Esta arquitectura puede ser entregada al equipo como referencia oficial para el MVP de GANUS OPERATIVO.

No se recomienda realizar una nueva ronda general de arquitectura antes de pasar al diseño funcional. Los detalles que todavía no están definidos deben resolverse en los diseños funcionales y técnicos de cada módulo, manteniendo estas reglas como marco obligatorio.

**Principio rector final:** GANUS debe tener una arquitectura suficientemente rigurosa para impedir deuda estructural, pero suficientemente simple para llegar al campo rápido. La sofisticación debe incorporarse solo cuando resuelva una necesidad real del negocio o de escala.
