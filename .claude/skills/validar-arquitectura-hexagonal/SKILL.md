---
name: validar-arquitectura-hexagonal
description: Guía y revisa la estructura de código de GANUS según la arquitectura hexagonal/clean oficial (Domain, Application, Adapters, Infrastructure) en un monolito modular NestJS con frontend Angular. Usar al crear un módulo, un caso de uso, un controlador, un repositorio o una pantalla, y al revisar dónde quedó la lógica de negocio.
---

# Validar arquitectura hexagonal

## Propósito
Mantener la lógica de negocio independiente de NestJS, del ORM, de PostgreSQL y de los proveedores, sin caer en
capas vacías. La arquitectura oficial es: **monolito modular + hexagonal/clean + API-first + integración por eventos**.

## Fuentes
- ARQ §4.2 (capas), §5 (fronteras antes que servicios), §6 (arquitectura lógica), §9 (Code First + migraciones), ADR-002.
- CONST §5 (arquitectura obligatoria), §11 (frontend), §12 (backend), §19 (prohibiciones de autonomía).
- `fuentes/pautas-desarrollo.md`: NestJS + TypeScript, Angular, PostgreSQL, Code First, Docker en producción.

## Cuándo usarla
- Al crear un módulo de dominio o un caso de uso.
- Al decidir dónde va una validación o una regla.
- Al crear controladores, consumidores de eventos o repositorios.
- Al revisar un PR de backend o frontend.

## Capas (ARQ §4.2)

| Capa | Contiene | Depende de | No puede |
| --- | --- | --- | --- |
| Domain | Entidades, objetos de valor, invariantes, máquinas de estado, errores de dominio tipados, interfaces de repositorio (puertos) | Nada externo | Importar NestJS, el ORM, HTTP o librerías de proveedores |
| Application | Casos de uso, orquestación, transacciones por caso de uso, puertos hacia otros dominios, publicación de eventos vía Outbox | Domain | Contener SQL o detalles HTTP |
| Adapters | Controladores HTTP (delgados), consumidores de eventos y de STAGING, mapeo de DTOs | Application | Contener reglas de negocio |
| Infrastructure | Implementación de repositorios con el ORM, entidades de persistencia, clientes externos, almacenamiento de archivos | Domain (implementa sus puertos) | Ser llamada directamente por el dominio |

Flujo (ARQ §6): Clientes → API/Eventos/STAGING → Adapters → Application → Domain → Ports → Infrastructure → PostgreSQL.

## Estructura de carpetas propuesta (decisión técnica, documentar en ADR-002)
```
src/modules/<dominio>/
  domain/          entidades, value objects, errores, puertos (interfaces)
  application/     casos de uso, DTOs de entrada/salida, puertos externos
  adapters/
    http/          controladores
    messaging/     consumidores de eventos y STAGING
  infrastructure/  repositorios ORM, entidades de persistencia, mappers
  <dominio>.module.ts
```

## Reglas de backend (CONST §12)
1. Controladores delgados: validan el DTO, llaman un caso de uso y devuelven la respuesta.
2. Un caso de uso explícito por intención (`PublicarVersionFormulario`, `CalcularDataset`, `ConfirmarYPublicarTarea`).
3. El dominio no depende del framework **cuando sea razonable** (CONST §12). Criterio práctico:
   - Los decoradores de NestJS (`@Injectable`, `@Controller`) van en Application y Adapters, no en entidades de dominio.
   - Las entidades de dominio no son entidades del ORM; se mapean en Infrastructure.
   - No se crea una capa o interfaz que solo reenvíe llamadas sin agregar nada.
4. Los DTOs no se convierten automáticamente en entidades.
5. Validación de formato en el borde (DTO); invariantes de negocio en el dominio.
6. Errores de dominio tipados, traducidos a las clases de error del 09C §4.2 (VALIDATION_ERROR, FORBIDDEN, CONFLICT…).
7. Los eventos se publican después de confirmar el hecho, mediante Outbox en la misma transacción (09C §7).
8. Logs estructurados con `correlationId`.
9. Una transacción por caso de uso.
10. Tenant y finca se validan en el backend; nunca se confía en lo que envía el cliente (CONST §13).

## Persistencia (ARQ §9, CONST §10)
- Code First con migraciones versionadas y documentadas.
- Nunca sincronización automática del ORM en producción.
- El ORM concreto no está definido en los documentos: es una decisión técnica (ADR).

## Reglas de frontend Angular (CONST §11)
- La interfaz consume casos de uso por API; no implementa reglas de negocio críticas.
- Las validaciones del frontend son de experiencia de usuario; la validación que cuenta es la del backend.
- No hardcodear catálogos parametrizables (procesos, tipos de actividad, espacios, tipos de campo).
- Mostrar los estados reales que devuelve el dominio; no inventar estados en la interfaz.
- No ocultar errores de API ni de sincronización.
- El renderer de formularios interpreta la definición publicada; no contiene formularios codificados a mano (04 §29).

## Monolito modular (ARQ §5)
- No se crean microservicios ni microfrontends por anticipación.
- Un módulo se separa en servicio solo con una razón objetiva y un ADR.

## Procedimiento de revisión
1. ¿Dónde está la regla de negocio? Debe estar en Domain o Application.
2. ¿El dominio importa algo de NestJS, del ORM o de HTTP? Si es así, moverlo.
3. ¿El controlador hace algo más que validar, llamar y responder?
4. ¿Hay SQL fuera de Infrastructure?
5. ¿Los eventos salen por Outbox dentro de la transacción?
6. ¿Hay capas o interfaces que no agregan nada? Eliminarlas.

## Prohibido
- Lógica de negocio en controladores, en el frontend, en STAGING o en n8n.
- Entidades del ORM usadas como modelo de dominio en otros módulos.
- Introducir un framework nuevo sin aprobación técnica (CONST §19).
- Renombrar entidades o contratos oficiales por estética (CONST §19).

## Verificación
- Pruebas unitarias del dominio sin levantar NestJS ni la base de datos.
- Prueba de arquitectura que falle si `domain/` importa `@nestjs/*` o el ORM.
