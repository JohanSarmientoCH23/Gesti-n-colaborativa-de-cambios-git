# Módulos de EduCampus LMS

## Descripción General

EduCampus LMS está compuesto por módulos funcionales independientes pero integrados, diseñados siguiendo principios de arquitectura modular y dominio impulsado por diseño (DDD). Cada módulo encapsula una capacidad de negocio específica y expone interfaces bien definidas para la comunicación inter-modular.

---

## Arquitectura Modular

La arquitectura de EduCampus LMS sigue el patrón de **Dominio Impulsado por Diseño (DDD)** con **Microservicios** para garantizar escalabilidad, mantenibilidad y independencia de despliegue.

### Principios de Diseño

| Principio | Descripción | Aplicación |
|-----------|-------------|------------|
| **Alta Cohesión** | Cada módulo agrupa funcionalidades relacionadas | Módulo de Evaluaciones incluye banco de preguntas, calificación y reportes |
| **Bajo Acoplamiento** | Comunicación vía eventos de dominio | Los módulos no dependen directamente unos de otros |
| **Encapsulamiento** | Lógica de negocio privada | Solo se exponen interfaces públicas |
| **Autonomía** | Despliegue independiente | Cada módulo puede escalarse por separado |

### Flujo de Comunicación

```
┌─────────────┐    Eventos     ┌─────────────┐
│  Módulo A   │───────────────▶│  Módulo B   │
│ (Usuarios)  │◀───────────────│(Evaluaciones)│
└─────────────┘    Respuestas  └─────────────┘
        │                           │
        │   ┌─────────────┐        │
        └──▶│  Módulo C   │◀───────┘
            │  (Reportes) │
            └─────────────┘
```

---

## Módulos Principales

### 1. Módulo de Gestión de Usuarios (`user-management`)

**Responsabilidad**: Administración completa del ciclo de vida de usuarios en la plataforma.

**Funcionalidades**:
- Registro, autenticación y autorización (OAuth 2.0 / OpenID Connect)
- Gestión de roles y permisos basados en RBAC (Role-Based Access Control)
- Perfiles de usuario: estudiantes, docentes, administradores, tutores
- Recuperación de contraseña y verificación de correo electrónico
- Auditoría de accesos y sesiones activas

**Interfaces expuestas**:
- `UserService`: CRUD de usuarios, asignación de roles
- `AuthService`: Login, logout, refresh tokens, validación de permisos
- `ProfileService`: Gestión de datos personales, preferencias, avatar

**Eventos de dominio publicados**:
- `UserRegisteredEvent`
- `UserRoleChangedEvent`
- `UserDeactivatedEvent`

---

### 2. Módulo de Catálogo de Cursos (`course-catalog`)

**Responsabilidad**: Gestión del catálogo académico y estructura curricular.

**Funcionalidades**:
- Creación y versionado de cursos y programas de estudio
- Definición de prerrequisitos, correquisitos y equivalencias
- Gestión de planes de estudio por cohorte y modalidad
- Metadatos de cursos: créditos, carga horaria, competencias, resultados de aprendizaje
- Búsqueda y filtrado avanzado (facetado, texto completo, por competencias)

**Interfaces expuestas**:
- `CourseCatalogService`: Consulta y administración de cursos/programas
- `CurriculumService`: Gestión de planes de estudio y secuenciación
- `SearchService`: Búsqueda unificada con filtros dinámicos

**Eventos de dominio publicados**:
- `CourseCreatedEvent`
- `CurriculumPublishedEvent`
- `CourseArchivedEvent`

---

### 3. Módulo de Inscripciones y Matrícula (`enrollment`)

**Responsabilidad**: Procesos de matrícula, inscripción a cursos y gestión de cupos.

**Funcionalidades**:
- Períodos de matrícula configurables por programa y cohorte
- Gestión de cupos, listas de espera y sobrecupos autorizados
- Validación de prerrequisitos y correquisitos en tiempo real
- Inscripción masiva y por plan de estudios
- Generación de certificados de matrícula y constancias de estudio
- Integración con módulo de pagos y facturación

**Interfaces expuestas**:
- `EnrollmentService`: Procesos de inscripción, baja, modificación
- `QuotaService`: Consulta y reserva de cupos
- `CertificateService`: Generación de documentos académicos

**Eventos de dominio publicados**:
- `StudentEnrolledEvent`
- `EnrollmentCancelledEvent`
- `QuotaExhaustedEvent`
- `CertificateGeneratedEvent`

---

### 4. Módulo de Gestión Académica (`academic-management`)

**Responsabilidad**: Seguimiento del proceso de enseñanza-aprendizaje.

**Funcionalidades**:
- Planificación de sesiones de clase y cronogramas
- Gestión de asistencias (presencial, virtual, híbrida)
- Calendario académico multi-institucional
- Asignación de docentes a grupos y cursos
- Gestión de aulas y recursos físicos/virtuales
- Actas de clase y bitácoras docentes

**Interfaces expuestas**:
- `SessionService`: Planificación y registro de sesiones
- `AttendanceService`: Registro y consulta de asistencias
- `ScheduleService`: Gestión de horarios y conflictos
- `FacultyAssignmentService`: Asignación docente-grupo

**Eventos de dominio publicados**:
- `SessionScheduledEvent`
- `AttendanceRecordedEvent`
- `FacultyAssignedEvent`

---

### 5. Módulo de Evaluaciones (`evaluations`)

**Responsabilidad**: Diseño, aplicación y calificación de instrumentos de evaluación.

**Funcionalidades**:
- Banco de preguntas tipificadas (opción múltiple, desarrollo, rúbricas, portafolios)
- Diseño de evaluaciones formativas y sumativas
- Aplicación en modalidades: presencial, online, take-home, sincrónica/asincrónica
- Calificación automática, semiautomática y manual con rúbricas
- Análisis psicométrico de items (dificultad, discriminación, fiabilidad)
- Reportes de desempeño individual y grupal
- Integración con proctoring y anti-plagio

**Interfaces expuestas**:
- `AssessmentService`: Ciclo de vida de evaluaciones
- `QuestionBankService`: Gestión de repositorio de preguntas
- `GradingService`: Calificación y retroalimentación
- `AnalyticsService`: Estadísticas y análisis psicométrico

**Ver documentación detallada**: [`docs/evaluaciones.md`](evaluaciones.md)

---

### 6. Módulo de Comunicación y Colaboración (`communication`)

**Responsabilidad**: Canales de interacción entre actores educativos.

**Funcionalidades**:
- Foros de discusión por curso, grupo y temática
- Mensajería directa y grupal con hilos
- Anuncios institucionales y por curso con notificaciones push/email
- Videoconferencia integrada (BigBlueButton, Zoom, Teams)
- Espacios de trabajo colaborativo (wikis, documentos compartidos)
- Calendario de eventos y recordatorios automáticos

**Interfaces expuestas**:
- `ForumService`: Gestión de foros e hilos
- `MessagingService`: Mensajería unificada
- `AnnouncementService`: Difusión de comunicados
- `VideoConferenceService`: Integración con proveedores de videollamada

---

### 7. Módulo de Recursos y Materiales (`resources`)

**Responsabilidad**: Gestión del repositorio de objetos de aprendizaje.

**Funcionalidades**:
- Almacenamiento versionado de recursos (documentos, videos, SCORM, H5P)
- Metadatos Dublin Core / LOM para descubrimiento
- Control de acceso por rol, curso, grupo y licencia
- Streaming adaptativo de video y audio
- Gestión de derechos de autor y licencias Creative Commons
- CDN integrado para entrega global de contenidos

**Interfaces expuestas**:
- `ResourceRepositoryService`: CRUD y versionado de recursos
- `MetadataService`: Gestión de metadatos normalizados
- `StreamingService`: Entrega optimizada de multimedia
- `LicenseService`: Validación de permisos de uso

---

### 8. Módulo de Analítica y Reportes (`analytics`)

**Responsabilidad**: Inteligencia educativa y toma de decisiones basada en datos.

**Funcionalidades**:
- Dashboards operativos para docentes, directivos y estudiantes
- Modelos predictivos de deserción, rendimiento y engagement
- Reportes institucionales personalizables (PDF, Excel, PowerBI)
- Tracking de eventos xAPI / Caliper Analytics
- Exportación de datos para data warehouse institucional
- Alertas tempranas basadas en umbrales configurables

**Interfaces expuestas**:
- `DashboardService`: Vistas personalizadas por rol
- `PredictiveService`: Modelos ML para alertas tempranas
- `ReportingService`: Generación y programación de reportes
- `EventCollectorService`: Ingesta de eventos de aprendizaje

---

### 9. Módulo de Administración Institucional (`institutional-admin`)

**Responsabilidad**: Configuración y gobernanza multi-tenant de la plataforma.

**Funcionalidades**:
- Multi-tenancy: sedes, facultades, departamentos, carreras
- Configuración de parámetros globales y por tenant
- Gestión de períodos académicos, calendarios y festivos
- Auditoría transversal de acciones administrativas
- Integración con sistemas externos (SIS, ERP, SSO, LDAP/AD)
- Gestión de proveedores LTI Advantage (1.3)

**Interfaces expuestas**:
- `TenantService`: Gestión de tenants y jerarquía organizacional
- `ConfigurationService`: Parámetros de plataforma
- `IntegrationService`: Conectores y adaptadores externos
- `AuditService`: Trazabilidad completa de operaciones

---

### 10. Módulo de Notificaciones (`notifications`)

**Responsabilidad**: Motor de notificaciones multi-canal y en tiempo real.

**Funcionalidades**:
- Plantillas de notificación con variables dinámicas (Handlebars/Mustache)
- Canales: email, SMS, push móvil, in-app, webhook, Slack, Teams
- Preferencias de usuario por canal, frecuencia y tipo de evento
- Cola de prioridades y reintentos con backoff exponencial
- Métricas de entregabilidad, apertura y clics
- Programación y envío masivo (newsletters, recordatorios)

**Interfaces expuestas**:
- `NotificationService`: Envío unificado multi-canal
- `TemplateService`: Gestión de plantillas versionadas
- `PreferenceService`: Centro de preferencias de usuario
- `DeliveryTrackingService`: Métricas y seguimiento

---

## Principios de Diseño Modular

| Principio | Aplicación en EduCampus |
|-----------|------------------------|
| **Alta cohesión** | Cada módulo agrupa funcionalidades relacionadas a un único dominio de negocio |
| **Bajo acoplamiento** | Comunicación vía eventos de dominio y contratos de API versionados |
| **Encapsulamiento** | Lógica de negocio privada; exposición solo a través de servicios públicos |
| **Autonomía de despliegue** | Cada módulo es un servicio desplegable independientemente (microservicio) |
| **Observabilidad** | Logs estructurados, métricas Prometheus, trazas OpenTelemetry por módulo |
| **Resiliencia** | Circuit breakers, timeouts, bulkheads entre módulos |

---

## Versionado y Compatibilidad

- **API Versioning**: Semantic Versioning (MAJOR.MINOR.PATCH) en headers `Accept: application/vnd.educampus.v1+json`
- **Event Versioning**: Eventos versionados en `eventType` (ej: `UserRegisteredEvent.v2`)
- **Política de deprecación**: 12 meses de soporte para versiones anteriores con headers `Deprecation` y `Sunset`
- **Contratos**: Definidos en OpenAPI 3.1 y AsyncAPI 2.6, publicados en portal de desarrolladores

---

## Dependencias Entre Módulos

```
user-management ──────────────────┐
                                  ▼
course-catalog ◄── enrollment ◄── academic-management ◄── evaluations
                                  ▲                       ▲
                                  │                       │
                            communication ◄───────────── resources
                                  ▲                       │
                                  │                       ▼
                            notifications ◄─────── analytics
                                  ▲
                                  │
                        institutional-admin ──────────────┘
```

> **Nota**: Las flechas indican dependencia de consumo (el módulo origen consume servicios del módulo destino). No existen dependencias cíclicas.