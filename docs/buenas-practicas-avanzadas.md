# Buenas Prácticas Avanzadas — EduCampus LMS

## Arquitectura Avanzada

### Domain-Driven Design (DDD)

El patrón DDD se aplica en EduCampus LMS para gestionar la complejidad del dominio educativo:

**Estrategias de Diseño (Actualizado):**

1. **Bounded Contexts**: Cada módulo define sus límites claros y responsabilidades exclusivas (nuevo contexto)
2. **Ubiquitous Language**: Lenguaje compartido entre negocio y tecnología, documentado en glosario
3. **Aggregates**: Consistencia transaccional dentro de un Aggregate Root con validación de invariantes
4. **Domain Events**: Comunicación desacoplada entre Bounded Contexts vía cola de mensajes
5. **Sagas**: Orquestación de transacciones distribuidas

```typescript
// Ejemplo de Aggregate Root
export class Course {
  private id: CourseId;
  private name: string;
  private modules: Module[];
  private prerequisites: CourseId[];

  // El Aggregate Root garantiza invariantes
  enroll(student: Student): Enrollment {
    this.validatePrerequisites(student);
    this.validateQuota();
    return Enrollment.create(this.id, student.id);
  }
}
```

### Event Sourcing

Para módulos críticos como Evaluaciones, implementamos Event Sourcing:

```typescript
// Los eventos son inmutables y constituyen la fuente de verdad
interface AssessmentEvent {
  type: 'Created' | 'QuestionAdded' | 'Graded' | 'Published';
  timestamp: Date;
  aggregateId: string;
  data: Record<string, unknown>;
}

// El estado se reconstruye replayando eventos
function rebuildAssessment(events: AssessmentEvent[]): Assessment {
  return events.reduce((state, event) => applyEvent(state, event), 
    Assessment.empty()
  );
}
```

### CQRS (Command Query Responsibility Segregation)

Separación de lecturas y escrituras para optimizar rendimiento:

```typescript
// Command Side (Escritura)
export class CreateAssessmentCommand {
  constructor(
    private assessmentRepo: IAssessmentRepository,
    private eventBus: IEventBus
  ) {}

  async execute(dto: CreateAssessmentDto): Promise<string> {
    const assessment = Assessment.create(dto);
    await this.assessmentRepo.save(assessment);
    await this.eventBus.publish(new AssessmentCreatedEvent(assessment));
    return assessment.id;
  }
}

// Query Side (Lectura)
export class GetAssessmentQuery {
  constructor(
    private assessmentReadModel: IAssessmentReadModel
  ) {}

  async execute(id: string): Promise<AssessmentView> {
    return this.assessmentReadModel.findById(id);
  }
}
```

---

## Patrones de Resiliencia

### Circuit Breaker

Protección contra fallos en cascada:

```typescript
import CircuitBreaker from 'opossum';

const breaker = new CircuitBreaker(async (studentId: string) => {
  return await externalService.getStudentData(studentId);
}, {
  timeout: 3000,
  errorThresholdPercentage: 50,
  resetTimeout: 30000,
});

breaker.on('open', () => {
  logger.warn('Circuit breaker abierto - servicio externo no disponible');
});

breaker.fallback(() => {
  return { status: 'offline', message: 'Servicio temporalmente no disponible' };
});
```

### Retry con Backoff Exponencial

```typescript
async function withRetry<T>(
  fn: () => Promise<T>,
  maxRetries: number = 3,
  baseDelay: number = 1000
): Promise<T> {
  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (attempt === maxRetries) throw error;
      
      const delay = baseDelay * Math.pow(2, attempt);
      await sleep(delay);
    }
  }
  throw new Error('Max retries exceeded');
}
```

### Bulkhead Pattern

Aislamiento de recursos para prevenir cascadas:

```typescript
// Colas separadas por tipo de operación
const queues = {
  critical: new Queue('critical', { concurrency: 10 }),
  normal: new Queue('normal', { concurrency: 20 }),
  background: new Queue('background', { concurrency: 5 }),
};

// Operaciones críticas (calificación) tienen prioridad
queues.critical.add(async () => {
  await gradeAssessment(studentId, assessmentId, answers);
});
```

---

## Seguridad Avanzada

### OWASP Top 10 Mitigaciones

| Vulnerabilidad | Mitigación en EduCampus |
|---------------|------------------------|
| **A01: Broken Access Control** | RBAC + ABAC, validación server-side |
| **A02: Cryptographic Failures** | AES-256-GCM en reposo, TLS 1.3 en tránsito |
| **A03: Injection** | Parameterized queries, input validation con Zod |
| **A04: Insecure Design** | Threat modeling, security by design |
| **A05: Security Misconfiguration** | Infrastructure as Code, secrets management |
| **A06: Vulnerable Components** | Dependabot, Snyk, auditorías regulares |
| **A07: Auth Failures** | MFA, rate limiting, account lockout |
| **A08: Data Integrity Failures** | Signed commits, artifact verification |
| **A09: Logging Failures** | Audit logging centralizado, alertas |
| **A10: SSRF** | Allowlist de URLs, network segmentation |

### Zero Trust Architecture

```typescript
// Nunca confiar, siempre verificar
const zeroTrustMiddleware = async (req: Request, res: Response, next: NextFunction) => {
  // 1. Verificar identidad (JWT válido)
  const token = extractToken(req);
  const user = await verifyToken(token);
  
  // 2. Verificar autorización (permisos específicos)
  const hasPermission = await checkPermission(user, req.path, req.method);
  
  // 3. Verificar contexto (IP, dispositivo, ubicación)
  const contextValid = await validateContext(req, user);
  
  // 4. Verificar integridad (no se ha manipulado la request)
  const integrityValid = await verifyIntegrity(req);
  
  if (!user || !hasPermission || !contextValid || !integrityValid) {
    throw new UnauthorizedError('Verificación de seguridad fallida');
  }
  
  next();
};
```

---

## Observabilidad Avanzada

### Distributed Tracing con OpenTelemetry

```typescript
import { trace, context, SpanKind } from '@opentelemetry/api';

const tracer = trace.getTracer('educampus-api');

export async function processEnrollment(
  studentId: string, 
  courseId: string
): Promise<Enrollment> {
  return tracer.startActiveSpan(
    'processEnrollment',
    { kind: SpanKind.INTERNAL, attributes: { studentId, courseId } },
    async (span) => {
      try {
        // Paso 1: Validar prerrequisitos
        await tracer.startActiveSpan('validatePrerequisites', async (span) => {
          await validatePrerequisites(studentId, courseId);
          span.end();
        });
        
        // Paso 2: Verificar cupos
        await tracer.startActiveSpan('checkQuota', async (span) => {
          await checkQuota(courseId);
          span.end();
        });
        
        // Paso 3: Crear inscripción
        const enrollment = await tracer.startActiveSpan(
          'createEnrollment',
          async (span) => {
            const result = await createEnrollment(studentId, courseId);
            span.setAttribute('enrollmentId', result.id);
            span.end();
            return result;
          }
        );
        
        span.setAttribute('enrollment.status', enrollment.status);
        return enrollment;
      } catch (error) {
        span.recordException(error);
        span.setStatus({ code: SpanStatusCode.ERROR });
        throw error;
      } finally {
        span.end();
      }
    }
  );
}
```

### Métricas Personalizadas

```typescript
import { Counter, Histogram, Gauge } from 'prom-client';

// Métricas de negocio
const enrollmentsCounter = new Counter({
  name: 'educampus_enrollments_total',
  help: 'Total de inscripciones procesadas',
  labelNames: ['status', 'course_type', 'modality'],
});

const gradingDuration = new Histogram({
  name: 'educampus_grading_duration_seconds',
  help: 'Duración del proceso de calificación',
  labelNames: ['assessment_type', 'auto_graded'],
  buckets: [0.1, 0.5, 1, 2, 5, 10, 30],
});

const activeStudents = new Gauge({
  name: 'educampus_active_students',
  help: 'Número de estudiantes activos en la plataforma',
  labelNames: ['tenant', 'course'],
});

// Uso
enrollmentsCounter.inc({ status: 'confirmed', course_type: 'online', modality: 'async' });
gradingDuration.observe({ assessment_type: 'multiple_choice', auto_graded: true }, 0.5);
activeStudents.set({ tenant: 'university-1', course: 'cs-101' }, 45);
```

---

## Performance Optimization

### Database Query Optimization

```typescript
// ✅ Uso de índices compuestos
const result = await dataSource.query(`
  SELECT 
    e.id,
    e.status,
    s.first_name,
    s.last_name,
    c.name as course_name
  FROM enrollments e
  INNER JOIN students s ON e.student_id = s.id
  INNER JOIN courses c ON e.course_id = c.id
  WHERE e.course_id = $1 
    AND e.status IN ('active', 'pending')
    AND e.created_at > NOW() - INTERVAL '30 days'
  ORDER BY e.created_at DESC
  LIMIT 20
`, [courseId]);

// ✅ Uso de covering index
// CREATE INDEX idx_enrollments_course_status_date 
// ON enrollments(course_id, status, created_at DESC) 
// INCLUDE (student_id);
```

### Redis Caching Strategy

```typescript
// Multi-level caching
export class CachedAssessmentService {
  private localCache = new LRUCache<string, any>({ max: 1000, ttl: 30000 });
  
  constructor(
    private assessmentService: AssessmentService,
    private redis: Redis
  ) {}

  async getAssessment(id: string): Promise<Assessment> {
    // Level 1: Local in-memory cache
    let assessment = this.localCache.get(id);
    if (assessment) return assessment;
    
    // Level 2: Redis distributed cache
    const cached = await this.redis.get(`assessment:${id}`);
    if (cached) {
      assessment = JSON.parse(cached);
      this.localCache.set(id, assessment);
      return assessment;
    }
    
    // Level 3: Database
    assessment = await this.assessmentService.findById(id);
    
    // Populate caches
    await this.redis.setex(`assessment:${id}`, 300, JSON.stringify(assessment));
    this.localCache.set(id, assessment);
    
    return assessment;
  }
}
```

---

## Métricas de Calidad de Código

### SonarQube Configuration

```yaml
# sonar-project.properties
sonar.projectKey=educampus-lms
sonar.organization=educampus

sonar.sources=src
sonar.tests=src/**/*.spec.ts,src/**/*.test.ts
sonar.test.inclusions=**/*.spec.ts,**/*.test.ts

sonar.typescript.lcov.reportPaths=coverage/lcov.info

# Quality Gates
# - Coverage on New Code: >= 80%
# - Duplicated Lines: < 3%
# - Maintainability Rating: A
# - Reliability Rating: A
# - Security Rating: A
```

### Code Smells Detection

```typescript
// ❌ Long Method (> 30 líneas)
// ❌ God Class (> 300 líneas)
// ❌ Feature Envy (uso excesivo de métodos de otro objeto)
// ❌ Data Clumps (mismos 3+ parámetros juntos frecuentemente)
// ❌ Primitive Obsession (uso excesivo de primitivos en lugar de objetos)

// ✅ Refactorización a aplicar
// - Extract Method
// - Replace Parameter with Object
// - Introduce Parameter Object
// - Replace Conditional with Polymorphism
```

---

## Conclusiones Técnicas

Las prácticas avanzadas documentadas aquí representan el estado del arte en desarrollo de software empresarial. Su implementación gradual permite:

1. **Escabilidad horizontal** mediante microservicios y CQRS
2. **Resiliencia** con patrones de Circuit Breaker y Bulkhead
3. **Observabilidad completa** con tracing distribuido y métricas
4. **Seguridad en profundidad** con Zero Trust y OWASP
5. **Calidad sostenible** con métricas automatizadas

La adopción debe ser iterativa, comenzando por las áreas de mayor riesgo y valor.