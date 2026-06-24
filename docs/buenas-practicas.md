# Buenas Prácticas — EduCampus LMS

## Buenas Prácticas de Desarrollo de Software

Este documento establece las mejores prácticas adoptadas por el equipo de desarrollo de EduCampus LMS, basadas en estándares de la industria y experiencia acumulada.

---

## 1. Arquitectura y Diseño

### 1.1 Principios SOLID

| Principio | Descripción | Aplicación en EduCampus |
|-----------|-------------|------------------------|
| **S** — Responsabilidad Única | Cada clase/módulo tiene una única razón para cambiar | Un servicio maneja un único dominio de negocio |
| **O** — Abierto/Cerrado | Abierto para extensión, cerrado para modificación | Plugins para nuevos tipos de evaluación |
| **L** — Sustitución de Liskov | Subtipos deben ser sustituibles por sus tipos base | Interfaz `IEvaluationStrategy` con implementaciones concretas |
| **I** — Segregación de Interfaz | Interfaces pequeñas y específicas | Separar `IReadService` de `IWriteService` |
| **D** — Inversión de Dependencia | Depender de abstracciones, no concreciones | Repositorios inyectados vía IoC |

### 1.2 Principios de Diseño

```yaml
# Configuración de arquitectura
principios:
  acoplamiento_bajo:
    - Comunicación entre módulos vía eventos de dominio
    - Contratos de API versionados e inmutables
    - Inyección de dependencias en todos los servicios
    
  cohesión_alta:
    - Cada módulo encapsula un único dominio de negocio
    - Servicios internos privados al módulo
    - Utilidades compartidas en capa `shared/`
    
  separacion_responsabilidades:
    - Controllers: solo orquestación HTTP
    - Services: lógica de negocio
    - Repositories: acceso a datos
    - DTOs: contrato de entrada/salida
    - Entities: representación de dominio
```

### 1.3 Patrones de Diseño Aplicados

| Patrón | Uso en EduCampus |
|--------|------------------|
| **Repository** | Acceso a datos abstraído de la infraestructura |
| **Strategy** | Estrategias de calificación intercambiables |
| **Observer** | Publicación de eventos de dominio |
| **Factory** | Creación de evaluaciones según tipo |
| **Decorator** | Añadir funcionalidades a servicios (logging, cache, validación) |
| **CQRS** | Separación de lecturas y escrituras en módulos de alto tráfico |
| **Saga** | Orquestación de transacciones distribuidas (matrícula + pago) |

---

## 2. Control de Versiones (Git)

### 2.1 Flujo de Trabajo con Git

```
main ─────────────────────────────────────────────────▶ Producción estable
  │
  ▼
develop ──────────────────────────────────────────────▶ Integración continua
  │         │         │         │         │
  ▼         ▼         ▼         ▼         ▼
feature/ bugfix/  feature/  hotfix/  release/
 auth     login    eval      sec      v1.2
```

### 2.2 Convenciones de Commits

```bash
# Formato: <emoji> <tipo>(<alcance>): <descripción>

# Nuevas funcionalidades
✨ feat(evaluaciones): implementar banco de preguntas con búsqueda

# Corrección de bugs
🐛 fix(auth): corregir validación de token expirado

# Documentación
📚 docs(instalación): actualizar guía de Docker

# Refactorización
🔨 refactor(user): separar lógica de autenticación y autorización

# Tests
🚨 test(evaluations): agregar tests para cálculo de percentil

# Rendimiento
🐎 perf(query): optimizar consulta de reportes con índices

# Seguridad
🔒 security(auth): implementar rate limiting en endpoint de login

# Dependencias
⬆️ deps(backend): actualizar NestJS a v10.3
```

### 2.3 Ramas

```bash
# Crear rama desde develop
git checkout develop
git pull origin develop
git checkout -b feature/nombre-descriptivo

# Nunca trabajar directamente en main o develop
# Siempre crear PR para integrar cambios
# Usar squash merge para mantener historial limpio
```

### 2.4 Protecciones de Rama

| Rama | Protección | Requisitos para merge |
|------|-----------|----------------------|
| `main` | Protegida, no se puede push directo | 2 aprobaciones, CI passing, sin conflictos |
| `develop` | Protegida, no se puede push directo | 1 aprobación, CI passing |
| `feature/*` | Sin protección | PR a develop |
| `hotfix/*` | Sin protección | PR a main + develop |

---

## 3. Commits y Mensajes

### 3.1 Principios de un Buen Commit

1. **Atómico**: Un commit = un cambio lógico
2. **Descriptivo**: El mensaje explica QUÉ y POR QUÉ (no CÓMO)
3. **Sin código roto**: El código debe compilar y pasar tests
4. **Sin archivos temporales**: No incluir `.env`, `node_modules`, etc.

### 3.2 Estructura del Mensaje

```
<tipo>(<alcance>): <asunto en imperativo, max 50 caracteres>

[opcional: cuerpo del mensaje, max 72 caracteres por línea]

[opcional: footer con referencias]
```

### 3.3 Ejemplos Correctos vs. Incorrectos

```bash
# ❌ MAL
git commit -m "update"
git commit -m "fix"
git commit -m "cambios varios"
git commit -m "prueba"
git commit -m "arreglo del bug"

# ✅ BIEN
git commit -m "🐛 fix(auth): corregir validación de refresh token"
git commit -m "✨ feat(courses): agregar filtros avanzados de búsqueda"
git commit -m "📚 docs(readme): actualizar sección de instalación"
git commit -m "♻️ refactor(enrollment): simplificar lógica de inscripción"
git commit -m "🔥 remove(deprecated): eliminar módulo legacy de asistencia"
git commit -m "⚡ perf(reports): optimizar generación de PDF con cache"
```

---

## 4. Código Limpio

### 4.1 Nombres Significativos

```typescript
// ❌ MAL
const d = new Date();
const x = users.filter(u => u.a === 1);
function proc(d) { return d * 2; }

// ✅ BIEN
const currentDate = new Date();
const activeUsers = users.filter(user => user.isActive === true);
function processEnrollment(enrollment: Enrollment): ProcessedEnrollment { 
  return { ...enrollment, processedAt: new Date() };
}
```

### 4.2 Funciones Pequeñas y Enfocadas

```typescript
// ❌ MAL: función gigante que hace todo
async function processEverything(userId: string, courseId: string, data: any) {
  // 200 líneas de código mezclando validación, negocio y persistencia
}

// ✅ BIEN: funciones pequeñas y reutilizables
async function processEnrollment(userId: string, courseId: string): Promise<Enrollment> {
  const validatedData = await this.validateEnrollmentRequest(userId, courseId);
  const enrollment = await this.createEnrollmentRecord(validatedData);
  await this.notifyRelevantParties(enrollment);
  return enrollment;
}

private async validateEnrollmentRequest(userId: string, courseId: string): Promise<ValidatedEnrollment> {
  await this.checkPrerequisites(userId, courseId);
  await this.checkQuotaAvailability(courseId);
  await this.checkConflictingSchedules(userId, courseId);
  return { userId, courseId, validatedAt: new Date() };
}
```

### 4.3 Manejo de Errores

```typescript
// ✅ MANEJO CORRECTO DE ERRORES
try {
  const result = await this.enrollmentService.enroll(studentId, courseId);
  return { success: true, data: result };
} catch (error) {
  if (error instanceof PrerequisiteNotMetError) {
    throw new BadRequestException(`Prerrequisitos no cumplidos: ${error.message}`);
  }
  if (error instanceof QuotaExhaustedException) {
    throw new ConflictException('No hay cupos disponibles. Ingrese a la lista de espera.');
  }
  // Loggear errores inesperados para debugging
  this.logger.error('Unexpected error during enrollment', { 
    studentId, courseId, error: error.message, stack: error.stack 
  });
  throw new InternalServerErrorException('Error interno al procesar la inscripción');
}
```

---

## 5. Seguridad

### 5.1 Autenticación y Autorización

```typescript
// JWT con refresh tokens
// RBAC con permisos granulares
// Rate limiting por usuario y endpoint
// CORS configurado por ambiente

const authConfig = {
  jwt: {
    accessToken: { expiresIn: '15m', algorithm: 'RS256' },
    refreshToken: { expiresIn: '7d', algorithm: 'RS256' },
  },
  rbac: {
    roles: ['student', 'teacher', 'admin', 'superadmin'],
    permissions: [
      'assessment:create', 'assessment:read', 'assessment:update',
      'grade:create', 'grade:read', 'grade:update',
    ],
  },
  rateLimit: {
    global: { ttl: 60000, limit: 100 },
    auth: { ttl: 900000, limit: 5 },
  },
};
```

### 5.2 Protección de Datos

```typescript
// Encriptación en reposo
const encryptionConfig = {
  algorithm: 'AES-256-GCM',
  keyRotation: '90 days',
  sensitiveFields: ['password', 'creditCard', 'documentNumber'],
};

// Hashing de contraseñas
const passwordConfig = {
  algorithm: 'argon2id',
  memoryCost: 65536,
  timeCost: 3,
  parallelism: 4,
};

// Auditoría de accesos
interface AuditLog {
  userId: string;
  action: string;
  resource: string;
  resourceId: string;
  ipAddress: string;
  userAgent: string;
  timestamp: Date;
  changes?: { before: any; after: any };
}
```

### 5.3 Validación de Entrada

```typescript
// Usar schemas de validación (Zod)
const CreateAssessmentSchema = z.object({
  title: z.string().min(5).max(200),
  type: z.enum(['formativa', 'sumativa', 'diagnostica']),
  courseId: z.string().uuid(),
  questions: z.array(QuestionSchema).min(1).max(100),
  weight: z.number().min(0).max(100),
  startDate: z.string().datetime(),
  endDate: z.string().datetime(),
}).refine(data => new Date(data.endDate) > new Date(data.startDate), {
  message: 'La fecha de fin debe ser posterior a la fecha de inicio',
});
```

---

## 6. Rendimiento

### 6.1 Estrategias de Caché

```typescript
// Caché multinivel
const cacheStrategy = {
  L1: { // Caché en memoria (in-process)
    ttl: 30, // segundos
    maxSize: 1000, // entradas
    use: ['configuraciones', 'sesiones_activas'],
  },
  L2: { // Redis (distributed)
    ttl: 300, // segundos
    use: ['resultados_consulta', 'tokens_usuario'],
  },
  L3: { // CDN
    ttl: 86400, // 24 horas
    use: ['assets_estaticos', 'archivos_scorm'],
  },
};
```

### 6.2 Paginación y Limitación

```typescript
// Paginación cursor-based (preferida para APIs públicas)
const paginationSchema = {
  first: z.number().min(1).max(100).default(20),
  after: z.string().optional(), // cursor
  sort: z.enum(['created_at', 'title', 'relevance']).default('created_at'),
  order: z.enum(['asc', 'desc']).default('desc'),
};

// Paginación offset-based (para dashboards admin)
const adminPaginationSchema = {
  page: z.number().min(1).default(1),
  limit: z.number().min(1).max(100).default(25),
  search: z.string().optional(),
  filters: z.record(z.string()).optional(),
};
```

### 6.3 Consultas Optimizadas

```typescript
// ✅ Usar índices y seleccionar solo columnas necesarias
const students = await this.studentRepository
  .createQueryBuilder('student')
  .select(['student.id', 'student.firstName', 'student.lastName', 'student.email'])
  .where('student.isActive = :isActive', { isActive: true })
  .orderBy('student.lastName', 'ASC')
  .limit(20)
  .getMany();

// ❌ Evitar SELECT * y consultas N+1
const students = await Student.findAll(); // Trae todo, incluyendo campos innecesarios
for (const student of students) {
  student.courses = await Course.findByStudentId(student.id); // N+1!
}
```

---

## 7. Testing

### 7.1 Pirámide de Testing

```
          /  E2E Tests  \          5%
         /────────────────\
        / Integration Tests \      15%
       /──────────────────────\
      /     Unit Tests         \    80%
     /──────────────────────────────\
```

### 7.2 Estrategia por Tipo

| Tipo | Herramienta | Cantidad | Ejecución |
|------|------------|----------|-----------|
| Unit Tests | Jest | ~80% | Cada commit |
| Integration Tests | Jest + Testcontainers | ~15% | Cada PR |
| E2E Tests | Playwright | ~5% | Semanal / antes de release |

### 7.3 Buenas Prácticas de Testing

```typescript
// ✅ Tests legibles y mantenibles
describe('EnrollmentService', () => {
  describe('enrollStudent', () => {
    it('should enroll student when prerequisites are met and quota is available', async () => {
      // Arrange
      const student = createTestStudent({ completedCourses: ['PREREQ-001'] });
      const course = createTestCourse({ availableQuota: 30 });
      mockPrerequisiteService.checkPrerequisites.mockResolvedValue(true);
      mockQuotaService.checkAvailability.mockResolvedValue(true);

      // Act
      const enrollment = await service.enrollStudent(student.id, course.id);

      // Assert
      expect(enrollment).toBeDefined();
      expect(enrollment.status).toBe('CONFIRMED');
      expect(mockNotificationService.notifyStudent).toHaveBeenCalledWith(
        student.id,
        'ENROLLMENT_CONFIRMED',
        expect.objectContaining({ courseId: course.id })
      );
    });

    it('should reject enrollment when prerequisites are not met', async () => {
      // Arrange
      const student = createTestStudent({ completedCourses: [] });
      const course = createTestCourse({ prerequisites: ['PREREQ-001'] });
      mockPrerequisiteService.checkPrerequisites.mockResolvedValue(false);

      // Act & Assert
      await expect(
        service.enrollStudent(student.id, course.id)
      ).rejects.toThrow(PrerequisiteNotMetError);
    });
  });
});
```

---

## 8. Despliegue y CI/CD

### 8.1 Pipeline de Integración Continua

```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck

  test:
    needs: lint
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_DB: educampus_test
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm run test:coverage
      - uses: codecov/codecov-action@v3

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: docker build -t educampus-api:${{ github.sha }} .
```

### 8.2 Estrategias de Despliegue

| Estrategia | Cuándo usarla | Rollback |
|-----------|---------------|----------|
| **Blue-Green** | Despliegues de API con cambios breaking | Redirigir tráfico al green anterior |
| **Canary** | Nuevas funcionalidades con riesgo | Ampliar/reducir porcentaje gradualmente |
| **Rolling** | Actualizaciones de dependencias | Detener rollout, revertir pods |
| **Feature Flags** | Funcionalidades experimentales | Desactivar flag en configuración |

### 8.3 Entornos

| Entorno | Propósito | Datos |
|---------|-----------|-------|
| `local` | Desarrollo individual | SQLite/PostgreSQL local |
| `development` | Integración continua | PostgreSQL de desarrollo, datos de prueba |
| `staging` | Validación pre-producción | Réplica de producción, datos sintéticos |
| `production` | Usuarios reales | Datos reales, backup automático |

---

## 9. Comunicación del Equipo

### 9.1 Estructura de Reuniones

| Reunión | Frecuencia | Duración | Participantes |
|---------|-----------|----------|---------------|
| Daily Standup | Diaria | 15 min | Todo el equipo |
| Sprint Planning | Quincenal | 2 horas | Equipo completo |
| Sprint Review | Quincenal | 1 hora | Equipo + stakeholders |
| Retrospective | Quincenal | 1 hora | Solo equipo técnico |
| Technical Review | Semanal | 1 hora | Tech leads |
| Architecture Review | Mensual | 2 horas | Senior engineers |

### 9.2 Documentación Requerida

- **ADR (Architecture Decision Records)**: Para decisiones técnicas significativas
- **RFC (Request for Comments)**: Para cambios de alto impacto
- **Runbooks**: Para operaciones de soporte y mantenimiento
- **Post-mortems**: Para incidentes de producción

---

## 10. Monitoreo y Observabilidad

### 10.1 Las Tres Pilares

| Pilar | Herramienta | Métricas Clave |
|-------|------------|----------------|
| **Métricas** | Prometheus + Grafana | Latencia P95, tasa de error, throughput |
| **Logs** | ELK Stack / Loki | Logs estructurados, correlación de requests |
| **Trazas** | Jaeger / Tempo | Tracing distribuido, latencia por servicio |

### 10.2 Alertas Configuradas

```yaml
alerts:
  - name: HighErrorRate
    condition: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
    severity: critical
    notify: [slack-incidents, pagerduty]
    
  - name: HighLatency
    condition: histogram_quantile(0.95, http_request_duration_seconds) > 2
    severity: warning
    notify: [slack-engineering]
    
  - name: DatabaseConnectionsExhausted
    condition: db_connections_active / db_connections_max > 0.9
    severity: critical
    notify: [slack-incidents, pagerduty]
```

---

## Checklist de Calidad

Antes de cada release, verificar:

### Código
- [ ] Todos los tests pasan
- [ ] Cobertura de código > 80%
- [ ] Linting sin errores
- [ ] Type check sin errores
- [ ] No hay código duplicado significativo
- [ ] No hay funciones con más de 50 líneas
- [ ] No hay archivos con más de 500 líneas

### Documentación
- [ ] README actualizado
- [ ] CHANGELOG actualizado
- [ ] API docs generados
- [ ] Comentarios en código crítico

### Seguridad
- [ ] No hay secrets en el código
- [ ] Dependencias actualizadas
- [ ] Auditoría de seguridad pasada
- [ ] Rate limiting configurado

### Rendimiento
- [ ] Pruebas de carga realizadas
- [ ] Consultas optimizadas
- [ ] Caché configurado correctamente
- [ ] Monitoreo activo