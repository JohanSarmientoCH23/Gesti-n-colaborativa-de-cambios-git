# Gestión Colaborativa de Cambios con Git

## Descripción del Proyecto

EduCampus LMS es una plataforma de gestión de aprendizaje diseñada para instituciones educativas que buscan modernizar sus procesos académicos mediante tecnología de vanguardia. Este proyecto documenta la implementación de buenas prácticas de control de versiones y flujos de trabajo colaborativos utilizando Git y GitHub.

### Misión

Transformar la experiencia educativa mediante una plataforma digital integral que potencie el aprendizaje colaborativo, facilite la gestión académica y promueva la excelencia en la enseñanza-aprendizaje.

---

## Objetivo

Demostrar de forma práctica el uso profesional de Git y GitHub en un entorno de desarrollo de software real, aplicando flujos de trabajo colaborativos que incluyen:

- Creación y gestión de ramas (GitFlow)
- Commits con mensajes profesionales (Conventional Commits)
- Pull Requests y code review
- Resolución de conflictos de merge
- Rebase y squash de commits
- Cherry-pick de cambios específicos
- Integración continua y despliegue continuo

---

## Estructura del Proyecto

```
├── README.md
├── docs/
│   ├── instalacion.md
│   ├── modulos.md
│   ├── evaluaciones.md
│   ├── contribucion.md
│   ├── buenas-practicas.md
│   └── respuestas-taller.md
├── evidencias/
│   └── README.md
```

---

## Módulos de EduCampus LMS

El sistema está compuesto por los siguientes módulos principales:

| Módulo | Descripción |
|--------|-------------|
| **Gestión de Usuarios** | Administración completa del ciclo de vida de usuarios |
| **Catálogo de Cursos** | Gestión del catálogo académico y estructura curricular |
| **Inscripciones y Matrícula** | Procesos de matrícula e inscripción a cursos |
| **Gestión Académica** | Seguimiento del proceso de enseñanza-aprendizaje |
| **Evaluaciones** | Diseño, aplicación y calificación de instrumentos de evaluación |
| **Comunicación y Colaboración** | Canales de interacción entre actores educativos |
| **Recursos y Materiales** | Gestión del repositorio de objetos de aprendizaje |
| **Analítica y Reportes** | Inteligencia educativa y toma de decisiones |
| **Administración Institucional** | Configuración y gobernanza multi-tenant |
| **Notificaciones** | Motor de notificaciones multi-canal |

---

## Equipo de Desarrollo

Este proyecto es desarrollado como parte del taller práctico de Git y GitHub, simulando un entorno colaborativo con múltiples "equipos de trabajo" representados por ramas de Git.

### Simulación de Equipos

| Rama | Equipo Simulado | Responsabilidad |
|------|-----------------|-----------------|
| `feature/documentacion-general` | Documentación | Creación de la documentación principal |
| `feature/modulos` | Módulos | Documentación de los módulos del sistema |
| `feature/evaluaciones` | Evaluaciones | Documentación del módulo de evaluaciones |
| `feature/instalacion` | Instalación | Guía de instalación del proyecto |
| `feature/contribucion` | Contribución | Guía de contribución para desarrolladores |
| `feature/buenas-practicas` | Buenas Prácticas | Documentación de estándares de desarrollo |

---

## Tecnologías Utilizadas

| Categoría | Tecnología |
|-----------|------------|
| Backend | TypeScript, NestJS, PostgreSQL |
| Frontend | React, TypeScript, Vite |
| Infraestructura | Docker, Docker Compose |
| CI/CD | GitHub Actions |
| Testing | Jest, Playwright |
| Documentación | Markdown, OpenAPI 3.1 |

---

## Inicio Rápido

```bash
# Clonar el repositorio
git clone https://github.com/JohanSarmientoCH23/Gesti-n-colaborativa-de-cambios-git.git

# Navegar al directorio
cd Gesti-n-colaborativa-de-cambios-git

# Levantar servicios
docker compose up -d

# Verificar instalación
curl http://localhost:3000/v1/health
```

Para más detalles, consultar la [Guía de Instalación](docs/instalacion.md).

---

## Documentación

- [Instalación](docs/instalacion.md) - Guía completa de instalación y configuración
- [Módulos](docs/modulos.md) - Documentación detallada de cada módulo
- [Evaluaciones](docs/evaluaciones.md) - Sistema de evaluaciones del LMS
- [Contribución](docs/contribucion.md) - Guía para contribuidores
- [Buenas Prácticas](docs/buenas-practicas.md) - Estándares de desarrollo
- [Respuestas del Taller](docs/respuestas-taller.md) - Análisis y reflexiones del taller

---

## Licencia

Este proyecto es parte del taller práctico de Gestión Colaborativa de Cambios con Git.

---

## Autor

**Johan Sarmiento** - Estudiante de Ingeniería de Sistemas

GitHub: [@JohanSarmientoCH23](https://github.com/JohanSarmientoCH23)