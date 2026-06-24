# Guía de Contribución — EduCampus LMS

## Bienvenido

Gracias por tu interés en contribuir a EduCampus LMS. Este documento establece las directrices y estándares para contribuir al proyecto de manera efectiva y consistente.

---

## Código de Conducta

Este proyecto se adhiere al [Código de Conducta de Contribuidores](https://www.contributor-covenant.org/es/version/2/0/code_of_conduct/). Al participar, te comprometes a:

- Tratar a todos los participantes con respeto y consideración
- Aceptar la construcción crítica de ideas y conceptos
- Enfocarse en lo que es mejor para la comunidad y el proyecto
- Mostrar empatía hacia otros miembros de la comunidad

---

## Flujo de Trabajo

### 1. Obtener el Código Fuente

```bash
# Clonar el repositorio
git clone https://github.com/JohanSarmientoCH23/Gesti-n-colaborativa-de-cambios-git.git

# Configar upstream
cd Gesti-n-colaborativa-de-cambios-git
git remote add upstream https://github.com/JohanSarmientoCH23/Gesti-n-colaborativa-de-cambios-git.git
```

### 2. Crear una Rama de Trabajo

Siempre trabajar desde `develop` y crear una rama descriptiva:

```bash
# Actualizar develop
git fetch origin
git checkout develop
git pull origin develop

# Crear rama de feature
git checkout -b feature/nombre-descriptivo-del-cambio
```

**Convención de nombres de ramas:**

| Tipo | Prefijo | Ejemplo |
|------|---------|---------|
| Nueva funcionalidad | `feature/` | `feature/agregar-filtros-curso` |
| Corrección de bug | `bugfix/` | `bugfix/corregir-validacion-email` |
| Hotfix crítico | `hotfix/` | `hotfix/cerrar-vulnerabilidad-sql` |
| Documentación | `docs/` | `docs/actualizar-guias-api` |
| Refactorización | `refactor/` | `refactor/separar-servicios-auth` |
| Tests | `test/` | `test/agregar-tests-modulo-evaluaciones` |
| Mejora de rendimiento | `perf/` | `optimizar-consultas-banco-preguntas` |

### 3. Realizar Cambios

#### Commits

Cada commit debe ser atómico y representar un cambio coherente:

```bash
git add <archivos>
git commit -m "✨ feat: descripción clara del cambio implementado"
```

**Formato de commit (Conventional Commits):**

```
<tipo>(<alcance>): <descripción corta>

[opcional: cuerpo del commit]

[opcional: footer con referencias a issues]
```

**Tipos de commit:**

| Tipo | Emoji | Descripción |
|------|-------|-------------|
| `feat` | ✨ | Nueva funcionalidad |
| `fix` | 🐛 | Corrección de bug |
| `docs` | 📚 | Documentación |
| `style` | 💄 | Formato/cosmético (sin cambio de lógica) |
| `refactor` | 🔨 | Refactorización de código |
| `test` | 🚨 | Añadir o modificar tests |
| `chore` | 🔧 | Tareas de mantenimiento, configuración |
| `perf` | 🐎 | Mejora de rendimiento |
| `ci` | 💚 | Cambios en integración continua |
| `build` | 🚀 | Cambios en sistema de build |
| `revert` | ⏪ | Revertir un commit anterior |

**Ejemplo de commit completo:**

```
✨ feat(evaluaciones): implementar banco de preguntas con búsqueda avanzada

- Agregar endpoints CRUD para gestión de preguntas
- Implementar búsqueda por competencia, dificultad y tipo
- Agregar validación de integridad de datos de entrada
- Incluir tests unitarios para todos los servicios

Closes #127
```

#### Push

```bash
git push origin feature/nombre-descriptivo-del-cambio
```

### 4. Crear Pull Request

1. Navegar al repositorio en GitHub
2. Hacer clic en "New Pull Request"
3. Seleccionar `develop` como rama base
4. Completar el template del PR con:

**Título descriptivo:**
```
feat(evaluaciones): implementar banco de preguntas con búsqueda avanzada
```

**Descripción:**
```markdown
## Descripción del cambio
Implementación completa del módulo de banco de preguntas para el sistema 
de evaluaciones de EduCampus LMS.

## Tipo de cambio
- [x] Nueva funcionalidad (feat)
- [ ] Corrección de bug (fix)
- [ ] Documentación (docs)
- [ ] Refactorización (refactor)
- [ ] Otro (especificar)

## Cambios principales
- Endpoints CRUD: GET, POST, PUT, DELETE para preguntas
- Búsqueda avanzada con filtros: competencia, dificultad, tipo
- Validación de integridad con schema Zod
- Tests unitarios: 15 pruebas pasando
- Tests de integración: 8 pruebas pasando

## Testing
- [x] Tests unitarios existentes pasan
- [x] Tests de integración pasan
- [x] Cobertura de código > 80%
- [x] Linting sin errores
- [x] Type check sin errores

## Issues relacionadas
Closes #127
```

### 5. Revisión del PR

- Mínimo **2 aprobaciones** requeridas
- Todos los checks CI deben pasar
- Cobertura de código no debe disminuir
- Resolver todos los comentarios antes de merge

### 6. Merge

Después de la aprobación, el PR será mergeado con `develop` usando **squash merge** para mantener un historial limpio.

---

## Estándares de Código

### TypeScript/JavaScript

```typescript
// ✅ Correcto: naming convenciones
interface UserProfile {
  id: string;
  firstName: string;
  lastName: string;
  email: string;
  role: UserRole;
}

// ✅ Correcto: funciones descriptivas
async function enrollStudentInCourse(
  studentId: string,
  courseId: string,
  options?: EnrollmentOptions
): Promise<EnrollmentResult> {
  // Implementación
}

// ❌ Incorrecto: nombres genéricos o ambiguos
function doStuff(data: any) {
  // ...
}
```

### Convenciones de Nomenclatura

| Elemento | Convención | Ejemplo |
|----------|-----------|---------|
| Variables | camelCase | `studentName` |
| Funciones | camelCase | `getStudentById` |
| Clases | PascalCase | `StudentService` |
| Interfaces | PascalCase | `IStudentRepository` |
| Enums | PascalCase | `UserRole` |
| Constantes | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| Archivos | kebab-case | `student.service.ts` |
| Carpetas | kebab-case | `user-management/` |

### Estructura de un Módulo

```
src/modules/evaluacion/
├── controllers/
│   ├── assessment.controller.ts
│   └── question.controller.ts
├── services/
│   ├── assessment.service.ts
│   ├── question.service.ts
│   └── grading.service.ts
├── repositories/
│   ├── assessment.repository.ts
│   └── question.repository.ts
├── entities/
│   ├── assessment.entity.ts
│   └── question.entity.ts
├── dto/
│   ├── create-assessment.dto.ts
│   └── update-assessment.dto.ts
├── validators/
│   └── assessment.validator.ts
├── events/
│   ├── assessment-created.event.ts
│   └── assessment-graded.event.ts
├── tests/
│   ├── assessment.service.test.ts
│   └── question.service.test.ts
└── evaluacion.module.ts
```

---

## Guías de Estilo

### Formateo Automático

El proyecto usa Prettier y ESLint para mantener consistencia:

```bash
# Formatear todo el código
npm run format

# Linting con corrección automática
npm run lint:fix

# Verificar sin corregir
npm run lint
npm run format:check
```

### Configuración de Prettier

```json
{
  "semi": true,
  "trailingComma": "all",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "bracketSpacing": true,
  "arrowParens": "always",
  "endOfLine": "lf"
}
```

### Reglas ESLint Importantes

- No usar `any` tipado explícito; preferir `unknown` o tipos específicos
- Siempre usar `async/await` sobre `.then()` en código nuevo
- Prefijar variables privadas con `_`
- No usar `console.log` en producción; usar logger estructurado
- Exportar interfaces cuando sea posible; usar `interface` sobre `type` para objetos

---

## Testing

### Ejecutar Pruebas

```bash
# Todas las pruebas
npm test

# Pruebas específicas
npm test -- --testPathPattern=evaluacion

# Cobertura
npm run test:coverage

# Pruebas en modo watch (desarrollo)
npm run test:watch
```

### Estructura de Pruebas

```typescript
// ✅ Correcto: estructura AAA (Arrange, Act, Assert)
describe('QuestionService', () => {
  let service: QuestionService;
  let repository: MockQuestionRepository;

  beforeEach(() => {
    repository = new MockQuestionRepository();
    service = new QuestionService(repository);
  });

  describe('createQuestion', () => {
    it('should create a question with valid data', async () => {
      // Arrange
      const dto: CreateQuestionDto = {
        type: 'multiple_choice',
        statement: '¿Cuál es la capital de Francia?',
        options: [
          { id: 'A', text: 'Londres', isCorrect: false },
          { id: 'B', text: 'París', isCorrect: true },
        ],
        difficulty: 'easy',
      };

      // Act
      const result = await service.createQuestion(dto);

      // Assert
      expect(result).toBeDefined();
      expect(result.id).toBeDefined();
      expect(result.statement).toBe(dto.statement);
      expect(repository.save).toHaveBeenCalledWith(expect.objectContaining(dto));
    });

    it('should throw error when options are missing for multiple choice', async () => {
      // Arrange
      const dto = { type: 'multiple_choice', statement: 'Test', options: [] };

      // Act & Assert
      await expect(service.createQuestion(dto)).rejects.toThrow(
        'Multiple choice questions require at least 2 options'
      );
    });
  });
});
```

### Cobertura de Código

| Módulo | Cobertura Mínima |
|--------|------------------|
| Controllers | 90% |
| Services | 95% |
| Repositories | 85% |
| Utilidades | 100% |

---

## Documentación

### Documentar Código

```typescript
/**
 * Servicio de gestión de evaluaciones educativas.
 * 
 * Proporciona operaciones CRUD para evaluaciones, así como
 * funcionalidades avanzadas de calificación, análisis psicométrico
 * y generación de reportes de desempeño.
 * 
 * @example
 * ```typescript
 * const assessment = await assessmentService.create({
 *   title: 'Parcial 1 - Patrones de Diseño',
 *   type: 'sumativa',
 *   courseId: 'course-123',
 * });
 * ```
 * 
 * @see {@link docs/evaluaciones.md | Documentación completa del módulo}
 */
export class AssessmentService {
  /**
   * Crea una nueva evaluación en el sistema.
   * 
   * @param dto - Datos de la evaluación a crear
   * @returns La evaluación creada con ID generado
   * @throws {ValidationError} Si los datos de entrada son inválidos
   * @throws {ConflictError} Si ya existe una evaluación con el mismo título en el curso
   */
  async create(dto: CreateAssessmentDto): Promise<Assessment> {
    // ...
  }
}
```

### Archivos de Cambio (CHANGELOG)

Todo cambio significativo debe documentarse en `CHANGELOG.md`:

```markdown
## [1.2.0] - 2026-06-23

### Added
- Banco de preguntas con búsqueda avanzada
- Integración con LTI Advantage 1.3

### Changed
- Mejorado el rendimiento de generación de reportes

### Fixed
- Corregido cálculo de percentil en reportes individuales

### Deprecated
- Endpoint `/api/v1/questions` será reemplazado en v2.0
```

---

## Issues y Bugs

### Reportar un Bug

Usar el template de GitHub Issues:

```markdown
## Descripción del Bug
[Descripción clara y concisa del problema]

## Pasos para Reproducir
1. Ir a '...'
2. Hacer clic en '...'
3. Desplazarse hacia '...'
4. Ver error

## Comportamiento Esperado
[Descripción de lo que debería ocurrir]

## Screenshots
[Si aplica, agregar capturas]

## Entorno
- OS: [ej. Windows 11]
- Navegador: [ej. Chrome 120]
- Versión de EduCampus: [ej. 1.2.0]
```

### Solicitar Nueva Funcionalidad

```markdown
## Descripción de la Funcionalidad
[Descripción clara de la funcionalidad solicitada]

## Caso de Uso
[¿Por qué es necesaria esta funcionalidad?]

## Solución Propuesta
[Cómo imaginas que debería funcionar]

## Alternativas Consideradas
[Cualquier otra solución que hayas considerado]
```

---

## Revisión de Código

### Checklist para el Autor

- [ ] Código sigue las guías de estilo
- [ ] Tests unitarios escritos y pasando
- [ ] Cobertura de código no disminuyó
- [ ] Documentación actualizada si aplica
- [ ] CHANGELOG actualizado si aplica
- [ ] No hay `console.log` o código de debug
- [ ] No hay secrets hardcodeados
- [ ] Commits son atómicos y descriptivos

### Checklist para el Revisor

- [ ] Código es claro y mantenible
- [ ] Lógica de negocio correcta
- [ ] Manejo de errores apropiado
- [ ] Tests cubren casos edge
- [ ] Rendimiento aceptable
- [ ] Seguridad considerada
- [ ] Accesibilidad verificada

---

## Reconocimiento

Las contribuciones serán reconocidas en:

- Sección de CONTRIBUTORS en `README.md`
- Créditos en release notes de nuevas versiones
- Mención en comunicaciones institucionales para contribuciones significativas