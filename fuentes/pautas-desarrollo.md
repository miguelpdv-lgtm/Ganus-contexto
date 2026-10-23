# Pautas de desarrollo (borrador)

> **Fuente de solo lectura.** Convertido a Markdown desde `drft-Pautas DeSarrollo.docx`. No se modifica desde este repositorio salvo que el usuario lo pida explícitamente.

---

Herramientas de Desarrollo:

Backend: NestJS version 11.26. NODE typescript  
Frontend: Angular version 22.0.0

Flujos de trabajo: n8n

Python para gestión de analítica de datos.

Base de datos: Postgres.

Entorno de desarrollo: hosting BD en VPS o supabase: datos en ORM. DBfirst: BD y consultas SQL se crea modelo y sube data con qrys sql. /CodeFirst. Todas las tablas se crean en el nestjs y se hacen las migración en las BD al crear el modelo. Recomienda codefirst.

Un app diseño la BD crea modelos y migra BD y los otros la consumen modelando en su código. (scalefolding)

Contenedores en dockers. Usar por practica estándar. En desarrollo no pero en producción si.

IA: Claude code pro. Creación de Skill (\*md) para el cerebro de Ganus - Operativo  
  
VPS: para alojar la base de datos.... y también las aplicaciones cuando se quiera desplegar en desarrollo. En tiempo de desarrollo pueden usar una vps para alojar todos los componentes ( Back - Front y db) pero en producción se debe alojar la bd en una subred privada diferente con restricciones de seguridad, ya que la bd es siempre el bien más preciado.

Analisis de si para desarrollo usamos VERCEL de una para desarrollo web. EN producción AWS o AZURE.  
  
Herramienta de <span class="mark">versionado</span> de código: Github (Repo privado) Debe crearse en una cuenta de github de la empresa de Francis, ahí se crea una organización con el nombre del proyecto y se invita a las personas que harán parte del proyecto, estableciendo reglas de seguridad en las ramas

<span class="mark">Seguimiento</span> proyecto JIRA. Establecer alcance por módulos y fases. Acceso equipo de trabajo.
