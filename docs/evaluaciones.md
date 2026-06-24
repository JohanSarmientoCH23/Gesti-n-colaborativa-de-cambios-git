# Módulo de Evaluaciones — EduCampus LMS

## Descripción General

El módulo de Evaluaciones es el componente central del proceso formativo en EduCampus LMS. Permite a docentes diseñar, aplicar, calificar y analizar instrumentos de evaluación en múltiples modalidades, asegurando trazabilidad completa del proceso evaluativo y alineación con los resultados de aprendizaje definidos en los planes de estudio.

---

## Arquitectura del Módulo

### Componentes Principales

```
┌─────────────────────────────────────────────────┐
│              Módulo de Evaluaciones              │
├─────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐              │
│  │   Banco de   │  │  Diseñador  │              │
│  │  Preguntas   │  │  de Eval.   │              │
│  └──────┬──────┘  └──────┬──────┘              │
│         │                 │                      │
│         ▼                 ▼                      │
│  ┌─────────────────────────────┐               │
│  │     Motor de Calificación   │               │
│  │  (automática / semiautomática) │               │
│  └──────────────┬──────────────┘               │
│                  │                               │
│         ┌───────┴───────┐                      │
│         ▼               ▼                      │
│  ┌─────────────┐ ┌─────────────┐              │
│  │  Analítica  │ │  Reportes   │              │
│  │Psicométrica │ │  de Desempeño│              │
│  └─────────────┘ └─────────────┘              │
└─────────────────────────────────────────────────┘
```

---

## Tipos de Evaluación Soportados

| Tipo | Descripción | Modalidad | Calificación |
|------|-------------|-----------|--------------|
| **Formativa** | Seguimiento continuo del aprendizaje | Presencial, Virtual, Híbrida | Automática/Semiautomática |
| **Sumativa** | Verificación de competencias al cierre de unidad | Presencial, Online proctoreado | Manual con rúbrica |
| **Diagnóstica** | Identificación de conocimientos previos | Online asincrónica | Automática |
| **Autoevaluación** | Reflexión del estudiante sobre su progreso | Online asincrónica | Sin calificación (formativa) |
| **Heteroevaluación** | Evaluación entre pares | Online sincrónica/asincrónica | Mixta |
| **Co-evaluación** | Evaluación grupal coordinada por docente | Presencial/Virtual | Semiautomática |

---

## Banco de Preguntas

### Tipos de Preguntas

#### Opción Múltiple
```json
{
  "tipo": "opcion_multiple",
  "enunciado": "¿Cuál es la complejidad temporal de quicksort en el caso promedio?",
  "opciones": [
    {"id": "A", "texto": "O(n)", "es_correcta": false},
    {"id": "B", "texto": "O(n log n)", "es_correcta": true},
    {"id": "C", "texto": "O(n²)", "es_correcta": false},
    {"id": "D", "texto": "O(log n)", "es_correcta": false}
  ],
  "retroalimentacion": {
    "A": "Complejidad de algoritmos de búsqueda lineal",
    "B": "Correcto. Quicksort divide y conquista con partición promedio O(log n) y combinación O(n)",
    "C": "Complejidad del peor caso de quicksort",
    "D": "Complejidad de búsqueda binaria"
  },
  "dificultad": "medio",
  "competencias": ["análisis_algorítmico", "complejidad_computacional"],
  "tiempo_estimado_segundos": 45
}
```

#### Pregunta de Desarrollo
```json
{
  "tipo": "desarrollo",
  "enunciado": "Explique el patrón de diseño Strategy y proporcione un caso de uso en un sistema de e-learning.",
  "criterios_evaluacion": [
    {"criterio": "Definición correcta del patrón", "peso": 0.3},
    {"criterio": "Identificación de ventajas y desventajas", "peso": 0.2},
    {"criterio": "Caso de uso contextualizado en e-learning", "peso": 0.3},
    {"criterio": "Calidad de la redacción y coherencia", "peso": 0.2}
  ],
  "limite_caracteres": 2000,
  "retroalimentacion_modelo": "Strategy permite definir una familia de algoritmos y hacerlos intercambiables..."
}
```

#### Rúbrica Analítica
```json
{
  "tipo": "rubrica_analitica",
  "criterios": [
    {
      "nombre": "Resolución del problema",
      "niveles": [
        {"nivel": "Excepcional", "puntaje": 4, "descriptor": "Resuelve el problema de forma óptima con justificación completa"},
        {"nivel": "Competente", "puntaje": 3, "descriptor": "Resuelve correctamente con mínimos errores"},
        {"nivel": "En desarrollo", "puntaje": 2, "descriptor": "Resuelve parcialmente con errores significativos"},
        {"nivel": "Inicial", "puntaje": 1, "descriptor": "No logra resolver o resolución incorrecta"}
      ]
    }
  ]
}
```

#### Portafolio
```json
{
  "tipo": "portafolio",
  "elementos_requeridos": [
    {"nombre": "Reflexión crítica", "peso": 0.25, "formato": "documento"},
    {"nombre": "Proyecto integrador", "peso": 0.35, "formato": "repositorio_github"},
    {"nombre": "Autoevaluación", "peso": 0.15, "formato": "formulario"},
    {"nombre": "Evidencia de aprendizaje", "peso": 0.25, "formato": "multimedia"}
  ],
  "fecha_limite": "2026-07-15T23:59:59Z"
}
```

---

## Motor de Calificación

### Calificación Automática

El motor de calificación automática procesa evaluaciones de opción múltiple, verdadero/falso, completar espacios y emparejamiento en tiempo real:

1. **Recepción de respuesta**: Valida formato y completeness
2. **Comparación con patrón**: Evalúa contra respuesta(s) correcta(s)
3. **Aplicación de reglas**: Ponderación, penalización por intentos, bonificación
4. **Cálculo de nota parcial**: Aplica lógica de deducción parcial si está configurada
5. **Generación de retroalimentación**: Envía retroalimentación inmediata al estudiante
6. **Registro en bitácora**: Almacena evidencia de la calificación

### Calificación Semiautomática

Para evaluaciones de desarrollo o proyectos, el motor asiste al docente:

1. **Análisis de similitud**: Detecta coincidencia textual con banco de respuestas modelo
2. **Extracción de palabras clave**: Identifica conceptos críticos en la respuesta
3. **Sugerencia de nota**: Propone una calificación basada en cobertura de criterios
4. **Revisión manual**: El docente ajusta, aprueba o rechaza la sugerencia
5. **Retroalimentación combinada**: Genera retroalimentación del motor + observaciones del docente

### Calificación Manual con Rúbrica

El docente califica directamente usando rúbricas predefinidas:

1. **Visualización de trabajo**: Muestra la respuesta/proyecto del estudiante
2. **Presentación de rúbrica**: Muestra criterios y niveles de la rúbrica asociada
3. **Selección de nivel**: El docente selecciona el nivel alcanzado por cada criterio
4. **Observaciones**: Espacio para comentarios cualitativos
5. **Publicación**: Envía calificación y retroalimentación al estudiante

---

## Ciclo de Vida de una Evaluación

```
┌─────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ DISEÑO  │───▶│PUBLICACIÓN│───▶│ APLICA-  │───▶│ CALIFI-  │
│         │    │          │    │  CIÓN    │    │  CACIÓN  │
└─────────┘    └──────────┘    └──────────┘    └──────────┘
     │                                              │
     │         ┌──────────┐    ┌──────────┐         │
     └────────▶│ARCHIVADO │◀───│ANÁLISIS  │◀────────┘
               │          │    │DE RESULT.│
               └──────────┘    └──────────┘
```

### Estados de una Evaluación

| Estado | Descripción | Acciones permitidas |
|--------|-------------|---------------------|
| `Borrador` | En diseño, no visible para estudiantes | Editar, eliminar, duplicar |
| `Publicada` | Visible para estudiantes, aún no abierta | Iniciar aplicación, cancelar |
| `Activa` | En proceso de aplicación | Recibir respuestas, extender tiempo |
| `Cerrada` | Plazo finalizado | Calificar, extender plazo excepcional |
| `En Calificación` | Docente revisando respuestas | Asignar notas, retroalimentar |
| `Publicada (notas)` | Resultados visibles para estudiantes | Ver estadísticas, apelaciones |
| `Archivada` | Evaluación finalizada e histórica | Solo lectura |

---

## Análisis Psicométrico

### Métricas Calculadas

#### Índice de Dificultad (p)
```
p = Σ Xᵢ / N

Donde:
  Xᵢ = 1 si el estudiante i respondió correctamente
  N  = número total de estudiantes que intentaron la pregunta

Interpretación:
  p > 0.70  →  Fácil
  0.30 ≤ p ≤ 0.70  →  Medio
  p < 0.30  →  Difícil
```

#### Índice de Discriminación (D)
```
D = (Pᵤ - Pₗ) / (N/2)

Donde:
  Pᵤ = propución de aciertos en el grupo de mayor rendimiento (27% superior)
  Pₗ = propución de aciertos en el grupo de menor rendimiento (27% inferior)
  N  = número total de evaluados

Interpretación:
  D > 0.40  →  Excelente discriminación
  0.30 ≤ D ≤ 0.40  →  Buena discriminación
  0.20 ≤ D < 0.30  →  Discriminación aceptable
  D < 0.20  →  Pobre discriminación (revisar o eliminar)
```

#### Fiabilidad (Cronbach's Alpha)
```
α = (k / (k-1)) * (1 - Σ σᵢ² / σₜ²)

Donde:
  k    = número de ítems
  σᵢ²  = varianza de cada ítem
  σₜ²  = varianza total del examen

Interpretación:
  α > 0.90  →  Excelente fiabilidad
  0.80 ≤ α ≤ 0.90  →  Buena fiabilidad
  0.70 ≤ α < 0.80  →  Aceptable
  α < 0.70  →  Pobre fiabilidad
```

---

## Integración con Estándares

### xAPI (Experience API)

Cada acción evaluativa genera un statement xAPI:

```json
{
  "actor": {
    "mbox": "mailto:estudiante@educampus.edu",
    "name": "María García López"
  },
  "verb": {
    "id": "http://adlnet.gov/expapi/verbs/attempted",
    "display": {"es": "intentó"}
  },
  "object": {
    "id": "http://educampus.edu/assessments/exam-modulo-3-2026",
    "definition": {
      "name": {"es": "Evaluación Módulo 3: Patrones de Diseño"},
      "type": "http://adlnet.gov/expapi/activities/assessment"
    }
  },
  "result": {
    "score": {"scaled": 0.85, "raw": 17, "min": 0, "max": 20},
    "success": true,
    "duration": "PT25M30S",
    "response": "B"
  },
  "context": {
    "contextActivities": {
      "parent": [{"id": "http://educampus.edu/courses/arquitectura-software"}]
    },
    "revision": "1"
  },
  "timestamp": "2026-06-23T14:30:00Z"
}
```

### LTI Advantage (1.3)

Integración con herramientas externas de evaluación:

```json
{
  "iss": "http://educampus.edu",
  "sub": "estudiante-12345",
  "aud": "https://herramienta-externa.com",
  "exp": 1719200000,
  "iat": 1719196400,
  "nonce": "unique-nonce-value",
  "https://purl.imsglobal.org/spec/lti/claim/message_type": "LtiResourceLinkRequest",
  "https://purl.imsglobal.org/spec/lti/claim/version": "1.3.0",
  "https://purl.imsglobal.org/spec/lti/claim/deployment_id": "deploy-educampus-001",
  "https://purl.imsglobal.org/spec/lti/claim/target_link_uri": "https://herramienta-externa.com/assessment/123",
  "https://purl.imsglobal.org/spec/lti/claim/resource_link": {
    "id": "resource-123",
    "title": "Evaluación Proctorada: Parcial Final"
  },
  "https://purl.imsglobal.org/spec/lti/claim/roles": [
    "http://purl.imsglobal.org/vocab/lis/v2/membership#Learner"
  ],
  "https://purl.imsglobal.org/spec/lti-claim/assignmentandgrades": {
    "scope": ["https://purl.imsglobal.org/spec/lti-ags/scope/lineitem"],
    "claim": "https://purl.imsglobal.org/spec/lti-claim/assignmentandgrades"
  }
}
```

---

## Reportes de Desempeño

### Reporte Individual por Estudiante

| Campo | Descripción |
|-------|-------------|
| Nota promedio ponderada | Media de todas las evaluaciones del curso |
| Desviación estándar | Dispersión respecto a la media del grupo |
| Percentil | Posición relativa frente al grupo |
| Tendencia | Evolución a lo largo del tiempo (mejora/estancamiento/retroceso) |
| Fortalezas | Competencias con mejor desempeño |
| Áreas de mejora | Competencias con desempeño inferior |
| Tasa de finalización | Porcentaje de evaluaciones entregadas vs. programadas |

### Reporte Grupal por Evaluación

| Métrica | Descripción |
|---------|-------------|
| Media, mediana, moda | Estadísticos descriptivos de la distribución de notas |
| Índice de dificultad promedio | Dificultad promedio de los ítems |
| Índice de discriminación promedio | Capacidad discriminativa del instrumento |
| Fiabilidad (Cronbach) | Consistencia interna del instrumento |
| Análisis de ítems | Identificación de ítems problemáticos |
| Distribución de notas | Histograma y curva normal ajustada |

### Reporte Institucional

| Indicador | Período | Comparación |
|-----------|---------|-------------|
| Tasa de aprobación por programa | Semestral | vs. semestre anterior |
| Retención vs. rendimiento | Anual | Correlación con deserción |
| Brechas de rendimiento | Anual | Por género, modalidad, cohorte |
| Calidad del instrumento | Semestral | Promedio de fiabilidad |

---

## Seguridad y Ética en Evaluaciones

### Medidas Anti-Plagio

- **Análisis de similitud textual**: Comparación contra base de documentos y repositorios públicos
- **Detección de patrones sospechosos**: Cambios abruptos de estilo, inconsistencias temporales
- **Integración con herramientas especializadas**: Turnitin, Compilatio, Ouriginal

### Proctoring en Línea

- **Identificación biométrica**: Reconocimiento facial pre y durante la evaluación
- **Monitoreo de navegador**: Detección de cambio de pestaña, copiar/pegar, clic derecho
- **Grabación de pantalla y audio**: Evidencia para auditoría
- **Análisis de comportamiento**: Movimientos sospechosos, tiempos anómalos

### Cumplimiento Normativo

- **LOPD / RGPD**: Consentimiento informado para recopilación de datos biométricos
- **Accesibilidad WCAG 2.1 AA**: Evaluaciones accesibles para personas con discapacidad
- **Integridad académica**: Políticas institucionales de honestidad académica

---

## Métricas de Rendimiento del Módulo

| Métrica | Objetivo | Medición |
|---------|----------|----------|
| Tiempo de carga de evaluación | < 2s | P95 del tiempo de carga |
| Disponibilidad | 99.9% uptime | Monitoring continuo |
| Throughput de calificación | > 500 evaluaciones/min | Carga pico durante exámenes |
| Latencia de notificación de nota | < 5s | Tiempo entre calificación y notificación |
| Consistencia de datos | 100% | Verificación de integridad referencial |