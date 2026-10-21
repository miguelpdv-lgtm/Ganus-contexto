# GANUS — MASTER CONTEXT FOR CLAUDE CODE
## Cerebro funcional y arquitectónico para desarrollo

**Versión:** 1.0  
**Fecha:** octubre 2026  
**Propósito:** servir como contexto maestro para Claude Code durante el diseño y construcción de GANUS.

---

# 0. INSTRUCCIÓN PRINCIPAL PARA CLAUDE CODE

GANUS no debe ser interpretado ni implementado como una aplicación monolítica.

GANUS es un **ecosistema de soluciones especializadas** que comparten identidad e información y que intercambian datos mediante una frontera controlada de integración.

El desarrollo debe comenzar por **GANUS OPERATIVO + APP MÓVIL + STAGING**, manteniendo preparados los contratos de integración con ODOO, TRACK y POTREROS.

Claude Code debe:

1. respetar las responsabilidades de cada solución;
2. no duplicar entidades maestras;
3. no crear dependencias directas entre tablas internas de soluciones diferentes;
4. utilizar identificadores comunes;
5. utilizar staging/contratos para el intercambio;
6. implementar primero el MVP definido en este documento;
7. no convertir capacidades futuras del ecosistema en requisitos del MVP;
8. no inventar decisiones funcionales faltantes;
9. cuando falte un detalle técnico, proponerlo como decisión técnica, sin alterar el comportamiento funcional;
10. mantener trazabilidad entre requerimiento, modelo, implementación y prueba.

Este documento constituye el **contexto rector para desarrollo**. Los documentos funcionales detallados de cada módulo deberán derivarse de él.

---

# 1. QUÉ ES GANUS

GANUS es una solución estratégica para llevar las fincas ganaderas a otro nivel bajo el concepto de empresa organizada, controlada y con toma de decisiones basadas en datos, aplicando tecnología con propósito para el control de animales, actividades, procesos y tracking de ubicación.

La visión de GANUS es la de un **ecosistema inteligente agroindustrial** que conecta:

- empresa;
- activos;
- operación;
- campo;
- territorio;
- automatización;
- información;
- analítica;
- inteligencia;
- decisión.

El objetivo no es desarrollar todos los componentes desde cero.

El objetivo es construir la **infraestructura digital agroindustrial GANUS**, aprovechando soluciones existentes cuando generan valor y concentrando la propiedad intelectual donde está la diferenciación.

---

# 2. VISIÓN DEL ECOSISTEMA

GANUS está compuesto por soluciones especializadas.

| Solución | Responsabilidad |
|---|---|
| **ODOO / GANUS CORE** | Gestión empresarial y ERP agroindustrial. |
| **GANUS OPERATIVO** | Organización de la finca, activos, captura, planificación y control del trabajo. |
| **APP MÓVIL** | Ejecución del trabajo y captura de información en campo. |
| **GANUS TRACK** | Territorio, ubicación, movimiento, geocercas y eventos territoriales. |
| **GANUS POTREROS** | Automatización de eventos físicos mediante RFID, portales, cámaras, pesaje y dispositivos. |
| **INDICA** | Indicadores, información y visualización para comprender el negocio. |
| **ADVISORY** | Interpretación, análisis y recomendaciones para apoyar decisiones. |

Cada solución tiene un propósito propio.

Ninguna solución debe absorber las responsabilidades de otra.

---

# 3. ARQUITECTURA FUNDAMENTAL

La arquitectura debe entenderse conceptualmente así:

```text
                    GANUS ECOSYSTEM
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
      ODOO           OPERATIVO           TRACK
   Empresa             │              Territorio
                       │
                  APP MÓVIL
                  Ejecución
                       │
                    POTREROS
               Eventos físicos
       └─────────────────┼─────────────────┘
                         │
                STAGING / EXCHANGE
                         │
              INFORMACIÓN COMPARTIDA
                         │
                  INDICA / ADVISORY
```

La implementación puede tener una base de datos común al ecosistema, pero cada solución debe mantener **esquema, ownership y lógica propios**.

---

# 4. BASE DE DATOS COMÚN Y ESQUEMAS SEPARADOS

## 4.1 Principio

GANUS utiliza una infraestructura de datos común para facilitar integración, identidad y explotación.

Esto **NO significa** que todas las soluciones compartan las mismas tablas funcionales.

La regla es:

> **Base de datos común + esquemas separados + ownership definido + intercambio controlado.**

## 4.2 Ownership

Cada solución es responsable de sus entidades y de su lógica.

Una solución no debe consultar directamente tablas internas de otra solución para resolver lógica funcional.

Si necesita información de otra solución:

1. consume una entidad/contrato autorizado;
2. recibe información por staging o mecanismo de integración definido;
3. conserva solamente las referencias necesarias.

## 4.3 Fuente maestra

| Información | Fuente maestra |
|---|---|
| Procesos empresariales | ODOO |
| Identidad operacional de activos | GANUS OPERATIVO / INVENTARIO |
| Trabajo planificado | GANUS OPERATIVO / PLANIFICACIÓN |
| Resultado de ejecución | Operación de campo → GANUS OPERATIVO |
| Territorio y eventos territoriales | TRACK |
| Eventos físicos automáticos | POTREROS |
| Indicadores publicados | INDICA / capa de conocimiento autorizada |
| Interpretación y recomendación | ADVISORY |

La plataforma de datos y el staging no deben convertirse en una segunda fuente transaccional.

---

# 5. STAGING / ÁREA DE INTERCAMBIO

STAGING es una pieza estructural del ecosistema.

Su función es permitir que las soluciones:

- publiquen información;
- reciban información;
- validen técnicamente;
- controlen estado;
- procesen;
- reintenten;
- auditen;
- confirmen recepción.

STAGING **no es**:

- fuente maestra;
- módulo funcional;
- lugar para duplicar toda la lógica de negocio;
- sustituto de las tablas de cada solución.

## 5.1 Flujos mínimos

### Operativo → Móvil

```text
Tarea planificada
→ paquete de trabajo
→ STAGING
→ APP MÓVIL
```

### Móvil → Operativo

```text
Resultado de ejecución
→ STAGING
→ validación
→ OPERATIVO
```

### TRACK → Ecosistema

```text
Evento territorial
→ STAGING
→ consumidor autorizado
```

### POTREROS → Ecosistema

```text
Evento físico
→ STAGING
→ consumidor autorizado
```

### ODOO → Ecosistema

```text
Información empresarial autorizada
→ integración/STAGING
→ consumidor autorizado
```

## 5.2 Estado mínimo del intercambio

```text
PENDIENTE
   ↓
RECIBIDO
   ↓
VALIDADO
   ↓
PROCESADO
   ↓
CONFIRMADO
```

Con estados complementarios para:

- ERROR;
- RECHAZADO;
- REINTENTO.

Todo registro de intercambio debe conservar como mínimo:

- identificador;
- origen;
- destino;
- versión;
- fecha/hora;
- estado;
- número de intento;
- referencia del objeto origen;
- mensaje/error cuando corresponda.

---

# 6. IDENTIDAD TRANSVERSAL

El ecosistema debe resolver una identidad común.

El concepto fundamental es:

> **ActivoID**

Un activo puede tener múltiples identificadores:

- RFID;
- código oficial;
- QR;
- código de barras;
- código anterior;
- identificador externo;
- identificador de dispositivo;
- otros mecanismos configurados.

Todos deben poder resolver al mismo ActivoID.

Nunca crear una identidad paralela del mismo activo por el hecho de que aparezca en otro módulo.

## Identificadores transversales

- GrupoID
- FincaID
- UsuarioID
- ActivoID
- IdentificadorExterno
- ActividadID
- FormularioID
- FormularioVersion
- TrabajoID
- EventoID
- EspacioTerritorialID

Los nombres físicos pueden adaptarse al estándar técnico elegido, pero el significado funcional no debe cambiar.

---

# 7. GANUS OPERATIVO — CENTRO DEL MVP

GANUS OPERATIVO es el núcleo de gestión del trabajo de la finca.

Su misión funcional es:

> **Convertir las necesidades de la finca en trabajo estructurado, entregarlo al campo y recibir sus resultados de manera trazable y controlada.**

## Cadena principal

```text
INGRESO
   ↓
ORGANIZACIÓN
   ↓
INVENTARIO
   ↓
FORMULARIOS
   ↓
PLANIFICACIÓN
   ↓
STAGING
   ↓
APP MÓVIL
   ↓
EJECUCIÓN
   ↓
STAGING
   ↓
SEGUIMIENTO
   ↓
INFORMACIÓN
   ↓
ADVISORY
```

---

# 8. DOMINIOS DEL MVP OPERATIVO

## 8.1 Ingreso

Responsabilidades:

- autenticación;
- identificación del usuario;
- contexto de finca;
- autorización.

No implementar aquí lógica funcional de los módulos.

---

## 8.2 Organización

Responsabilidades:

- grupo empresarial opcional;
- finca;
- usuarios;
- roles;
- permisos;
- contexto operacional.

Regla:

Una finca puede existir independientemente o asociada a un grupo.

---

## 8.3 Inventario

Inventario representa la identidad operacional de los activos.

Responsabilidades:

- crear activos;
- importar activos;
- clasificar activos;
- identificar activos;
- mantener atributos;
- relaciones;
- documentos;
- historial;
- identificadores múltiples;
- ciclo de vida del activo.

El activo es una identidad transversal del ecosistema.

Inventario no planifica el trabajo.

---

# 9. FORMULARIOS DINÁMICOS

Los formularios son el mecanismo universal configurable de captura.

Su responsabilidad es:

> **Definir cómo se captura información.**

Un formulario puede contener:

- campos;
- tipos de dato;
- catálogos;
- validaciones;
- obligatoriedad;
- evidencias;
- claves de búsqueda;
- reglas de presentación;
- versión;
- métodos de captura.

Métodos de captura pueden incluir:

- selección;
- texto;
- número;
- fecha;
- fotografía;
- video;
- documento;
- RFID;
- QR;
- código;
- voz;
- dispositivos;
- integraciones.

La disponibilidad de cada método se define funcionalmente en el módulo correspondiente.

## Regla crítica

Formulario **NO** administra:

- agenda;
- periodicidad;
- población;
- asignación;
- ejecución.

Eso corresponde a Planificación.

---

# 10. PLANIFICACIÓN

Planificación es la responsable de convertir una necesidad en trabajo.

Debe responder:

> **¿Qué hacer? ¿Sobre quién? ¿Cuándo? ¿Quién lo ejecuta? ¿Con qué contexto?**

Una tarea puede ser:

- unitaria;
- masiva;
- recurrente.

Debe soportar:

- selección de población;
- filtros;
- previsualización;
- asignación;
- fechas;
- prioridad;
- descripción;
- periodicidad;
- calendario;
- reprogramación;
- estados;
- seguimiento.

## Estados funcionales

### Planificación

```text
BORRADOR
→ VALIDADA
→ PROGRAMADA
→ DISPONIBLE PARA CAMPO
```

### Ejecución

```text
EN EJECUCIÓN
→ PAUSADA
→ FINALIZADA
→ POR VALIDAR
→ CERRADA
```

Alternativa:

```text
CANCELADA
```

---

# 11. APP MÓVIL

La aplicación móvil es el centro de ejecución.

Principio:

> **El operador no administra GANUS. GANUS le entrega el trabajo, le facilita capturarlo y le permite informar lo extraordinario.**

El móvil debe:

1. recibir tareas;
2. identificar el activo;
3. cargar contexto;
4. ejecutar el formulario;
5. capturar evidencias;
6. guardar;
7. informar novedades;
8. trabajar offline;
9. sincronizar.

El móvil NO debe:

- planificar;
- segmentar poblaciones;
- administrar formularios;
- modificar la configuración maestra;
- construir dashboards estratégicos.

## Filosofía UX

```text
IDENTIFICAR
→ CONTEXTO
→ EJECUTAR
→ CAPTURAR
→ CONFIRMAR
→ CONTINUAR
```

---

# 12. OFFLINE Y SINCRONIZACIÓN

El trabajo de campo debe poder ejecutarse sin conectividad.

El dispositivo debe conservar localmente lo necesario para:

- identificar;
- ejecutar;
- capturar;
- guardar;
- evidenciar.

Las capturas confirmadas localmente no deben perderse por ausencia de conexión.

Debe existir una cola de sincronización con:

- estado;
- reintentos;
- confirmación;
- errores;
- conflictos.

La especificación física de sincronización se deriva posteriormente como artefacto técnico.

---

# 13. TRACK

TRACK es la solución territorial.

Responsabilidades del ecosistema:

- Gemelo Digital;
- Espacios Territoriales;
- dispositivos;
- posiciones;
- geocercas;
- movimientos;
- entrada/salida;
- permanencia;
- eventos territoriales.

Un Espacio Territorial puede representar:

- finca;
- sector;
- lote;
- potrero;
- corral;
- zona;
- instalación;
- otra unidad definida por la finca.

Una geocerca es una condición geométrica; no es equivalente al Espacio Territorial.

TRACK no es maestro de identidad de activos ni de trabajo.

---

# 14. POTREROS / AUTOMATIZACIÓN

POTREROS se especializa en captura física automática.

Puede incorporar:

- RFID;
- portal inteligente;
- cámara;
- pesaje;
- sensores;
- puntos de paso;
- dispositivos.

Produce eventos normalizados.

Ejemplos:

- RFID detectado;
- identificación de activo;
- entrada;
- salida;
- pesaje;
- paso por punto controlado;
- evidencia visual.

Regla:

> **El dispositivo detecta; la solución de negocio determina el significado empresarial.**

POTREROS no debe contener reglas empresariales complejas que correspondan al ecosistema de conocimiento.

---

# 15. ODOO / GANUS CORE

ODOO representa la dimensión empresarial.

Puede cubrir:

- compras;
- ventas;
- costos;
- contabilidad;
- tesorería;
- recursos humanos;
- inventarios empresariales;
- procesos administrativos;
- otros procesos ERP.

GANUS aporta:

- activos de campo;
- actividades;
- resultados;
- información productiva;
- territorio;
- eventos.

ODOO aporta:

- dimensión financiera;
- administrativa;
- comercial;
- empresarial.

ODOO no reemplaza la captura especializada de campo.

---

# 16. INDICA Y ADVISORY

## INDICA

Transforma información autorizada en vistas e indicadores.

No debe inventar indicadores sin una definición o fuente autorizada.

## ADVISORY

Interpreta:

- datos;
- indicadores;
- alertas;
- resultados;
- conocimiento.

Puede:

- responder preguntas;
- explicar resultados;
- explicar alertas;
- encontrar activos;
- identificar situaciones;
- recomendar acciones.

No debe:

- inventar datos;
- inventar reglas;
- modificar transacciones por iniciativa propia;
- publicar conocimiento productivo no validado.

La IA es un mecanismo de interpretación, no la fuente maestra.

---

# 17. CADENA DE VALOR DE INFORMACIÓN

La cadena conceptual del ecosistema es:

```text
REALIDAD
   ↓
EVENTO
   ↓
DATO
   ↓
INFORMACIÓN
   ↓
CONOCIMIENTO
   ↓
INDICADOR / ALERTA / RECOMENDACIÓN
   ↓
DECISIÓN
   ↓
ACCIÓN
   ↓
NUEVO EVENTO
```

El sistema debe poder reconstruir el vínculo entre:

- activo;
- actividad;
- evento;
- dato;
- usuario;
- fecha/hora;
- ubicación cuando aplique;
- formulario/version;
- trabajo;
- regla cuando aplique;
- resultado.

---

# 18. ALCANCE REAL DEL MVP

El MVP debe demostrar el núcleo operacional.

## Se implementa

### GANUS OPERATIVO

- ingreso;
- organización;
- inventario;
- formularios dinámicos;
- planificación;
- staging;
- seguimiento;
- informes;
- dashboard inicial;
- advisory inicial.

### APP MÓVIL

- recepción de trabajo;
- identificación;
- ejecución;
- captura;
- evidencias;
- alertas/novedades;
- offline;
- sincronización.

## Se prepara para integración

### ODOO

Contrato de integración inicial.

### TRACK

Contrato de eventos e integración preparada.

### POTREROS

Contrato de eventos e integración preparada.

El hardware o implementación avanzada de estos componentes no debe bloquear la validación del MVP Operativo.

---

# 19. FUERA DEL PRIMER RELEASE

No asumir como requisitos del MVP:

- tracking avanzado;
- hardware IoT avanzado;
- automatización compleja;
- modelos predictivos;
- IA autónoma avanzada;
- optimización avanzada de recursos;
- balanceador avanzado;
- dashboards completamente configurables;
- canvas avanzado;
- marketplace;
- integraciones no definidas.

Estas capacidades pertenecen al ecosistema y pueden entrar en fases posteriores.

---

# 20. CASO MAESTRO DE VALIDACIÓN DEL MVP

Claude Code debe poder construir y probar este escenario:

```text
1. Crear finca
2. Crear usuario
3. Crear/importar activos
4. Asociar identificadores
5. Crear formulario de captura
6. Publicar formulario
7. Seleccionar población
8. Crear actividad
9. Crear tarea
10. Programar
11. Asignar operador
12. Publicar
13. Generar paquete de trabajo
14. Enviar mediante staging
15. Recibir en móvil
16. Ejecutar
17. Capturar datos
18. Capturar evidencia
19. Guardar offline si es necesario
20. Sincronizar
21. Recibir resultado en staging
22. Validar
23. Procesar
24. Actualizar estado
25. Registrar trazabilidad
26. Mostrar seguimiento
27. Mostrar indicador
28. Permitir interpretación/advisory
```

Si este flujo funciona correctamente, existe un MVP funcional real.

---

# 21. REGLAS ABSOLUTAS PARA CLAUDE CODE

Estas reglas no deben ser reinterpretadas:

1. GANUS es un ecosistema, no un monolito.
2. No duplicar entidades maestras.
3. No duplicar ActivoID.
4. No crear identidades diferentes para el mismo activo por módulo.
5. Cada solución tiene ownership propio.
6. No consultar directamente tablas internas de otra solución para resolver lógica funcional.
7. Utilizar contratos y staging para intercambio.
8. Staging no es fuente maestra.
9. Inventario es dueño de identidad operacional del activo.
10. Planificación es dueña del trabajo programado.
11. Móvil ejecuta; no planifica.
12. Track es dueño de eventos territoriales.
13. Potreros es productor de eventos físicos.
14. ODOO es dueño de procesos ERP.
15. Formularios define captura; no agenda.
16. Recurrencia pertenece a Planificación.
17. Segmentación pertenece a Planificación.
18. Dashboard no crea reglas paralelas.
19. Advisory no inventa información.
20. IA no sustituye fuentes maestras.
21. No implementar capacidades futuras como parte del MVP sin instrucción explícita.
22. No introducir una nueva solución para resolver algo que ya pertenece a un dominio definido.
23. No hardcodear reglas de negocio que deban ser configurables.
24. No asumir bovinos como única estructura del modelo de activo si el modelo funcional define otros activos.
25. Mantener trazabilidad completa.
26. Priorizar simplicidad operacional.
27. Priorizar costo-beneficio y reutilización de componentes existentes.
28. Si existe una solución existente que resuelve adecuadamente una capacidad no diferenciadora, integrarla antes que reconstruirla.
29. La propiedad intelectual GANUS debe concentrarse en orquestación, modelo operacional, integración, reglas, experiencia y conocimiento donde exista diferenciación.
30. Si falta una decisión funcional, detener la inferencia y registrar la decisión pendiente.

---

# 22. REGLAS DE DISEÑO

Todo diseño funcional posterior debe poder responder:

### Para cada módulo

- ¿Cuál es su responsabilidad?
- ¿Qué entidades administra?
- ¿Qué información consume?
- ¿Qué información produce?
- ¿Quién es dueño de esa información?
- ¿Qué eventos genera?
- ¿Qué eventos recibe?
- ¿Qué estados maneja?
- ¿Qué reglas aplica?
- ¿Qué no hace?
- ¿Cómo se integra?
- ¿Qué parte pertenece al MVP?
- ¿Qué parte queda para evolución?

No iniciar diseño de pantallas antes de resolver estas preguntas.

---

# 23. REGLA PARA LOS DISEÑOS FUNCIONALES POSTERIORES

Los módulos deben diseñarse siguiendo esta estructura:

1. Propósito
2. Alcance
3. Responsabilidad
4. Entidades
5. Procesos
6. Estados
7. Reglas de negocio
8. Eventos
9. Entradas
10. Salidas
11. Integraciones
12. UX
13. Casos de uso
14. Criterios de aceptación
15. MVP
16. Evolución

Cada diseño funcional debe ser consistente con este documento.

No repetir ni redefinir la arquitectura general.

---

# 24. ARTEFACTOS TÉCNICOS QUE SE DERIVARÁN DESPUÉS

Una vez cerrado el diseño funcional de cada dominio se podrán generar:

- modelo de dominio;
- ERD;
- diccionario de datos;
- schemas SQL;
- contratos API;
- contratos de eventos;
- contratos de staging;
- RBAC;
- offline/sync;
- migración;
- pruebas;
- backlog técnico;
- Definition of Done.

Estos artefactos **no deben inventar comportamiento funcional**.

Deben implementar lo definido funcionalmente.

---

# 25. ORDEN DE CONSTRUCCIÓN

El trabajo de diseño debe seguir este orden:

```text
1. GANUS OPERATIVO
      ↓
2. ORGANIZACIÓN
      ↓
3. INVENTARIO
      ↓
4. FORMULARIOS
      ↓
5. PLANIFICACIÓN
      ↓
6. STAGING
      ↓
7. APP MÓVIL
      ↓
8. SEGUIMIENTO
      ↓
9. INFORMES / DASHBOARD
      ↓
10. ADVISORY
      ↓
11. CONTRATOS ODOO
      ↓
12. CONTRATOS TRACK
      ↓
13. CONTRATOS POTREROS
```

No desarrollar primero integraciones periféricas.

Primero debe existir el núcleo operacional que les dará sentido.

---

# 26. CRITERIOS DE CALIDAD DEL PRODUCTO

GANUS debe priorizar:

- simplicidad;
- trazabilidad;
- configurabilidad;
- operación offline;
- bajo número de acciones;
- reutilización;
- integración;
- escalabilidad;
- seguridad;
- bajo costo operativo;
- tecnología con propósito.

El sistema debe ser poderoso para la empresa pero sencillo para el campo.

---

# 27. DEFINICIÓN FINAL

> **GANUS es un ecosistema B2B de soluciones especializadas que transforma la operación ganadera en información estructurada, trazable y útil para la toma de decisiones.**
>
> **GANUS OPERATIVO es el núcleo del primer MVP: organiza la finca, administra los activos, define cómo capturar información, transforma necesidades en trabajo planificado, entrega ese trabajo al campo mediante la aplicación móvil y recibe sus resultados mediante una capa controlada de intercambio.**
>
> **ODOO representa la empresa; TRACK representa el territorio; POTREROS representa la automatización física; la APP MÓVIL representa la ejecución; INDICA representa la información; ADVISORY representa la interpretación.**
>
> **Todos se integran mediante identidad común, ownership definido, esquemas separados y un área de intercambio controlada.**
>
> El objetivo del MVP no es construir todo GANUS. Es demostrar que este modelo funciona de extremo a extremo y construir el núcleo que permitirá incorporar progresivamente las demás soluciones.

---

# 28. MENSAJE OPERATIVO PARA CLAUDE CODE

Cuando exista una duda durante el desarrollo:

**NO INVENTAR.**

Aplicar este orden:

```text
1. Revisar este Master Context.
2. Revisar el diseño funcional específico del módulo.
3. Revisar las reglas de negocio definidas.
4. Revisar el contrato de integración correspondiente.
5. Si no existe definición:
      → registrar decisión pendiente.
6. No cambiar la arquitectura para resolver una ambigüedad.
```

La prioridad es:

```text
CONSISTENCIA
   >
TRAZABILIDAD
   >
SIMPLICIDAD
   >
VELOCIDAD DE DESARROLLO
```

GANUS debe crecer como ecosistema, no como acumulación de funcionalidades.
