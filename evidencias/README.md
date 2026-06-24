# Evidencias del Taller — Gestión Colaborativa de Cambios con Git

## Instrucciones Generales

Este archivo indica todas las capturas de pantalla que debes tomar durante la realización del taller. Cada evidencia debe ser una captura clara que muestre el resultado de la operación correspondiente.

---

## Evidencias Requeridas

### 1. Estado Inicial del Repositorio

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 1.1 | `git status` | Estado limpio después del commit inicial |
| 1.2 | `git branch` | Rama `main` como rama actual |
| 1.3 | `git log --oneline` | Primer commit con mensaje "first commit" |
| 1.4 | GitHub - Vista del repositorio | Mostrar el repositorio en GitHub con el README |

### 2. Estructura de Ramas

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 2.1 | `git branch` | Ramas `main` y `develop` |
| 2.2 | `git branch -a` | Todas las ramas incluyendo `origin/` |
| 2.3 | GitHub - Pestaña Branches | Lista de ramas en GitHub |
| 2.4 | `git branch feature/documentacion-general` | Creación de rama feature |
| 2.5 | `git branch feature/modulos` | Creación de rama feature |
| 2.6 | `git branch feature/evaluaciones` | Creación de rama feature |
| 2.7 | `git branch feature/instalacion` | Creación de rama feature |
| 2.8 | `git branch feature/contribucion` | Creación de rama feature |
| 2.9 | `git branch feature/buenas-practicas` | Creación de rama feature |
| 2.10 | `git branch experimental/buenas-practicas-avanzadas` | Creación de rama experimental |

### 3. Reto 1 — Push

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 3.1 | `git status` (en rama feature/documentacion-general) | Archivos a commitear |
| 3.2 | `git log --oneline` | Múltiples commits en la rama feature |
| 3.3 | `git push origin feature/documentacion-general` | Push exitoso a GitHub |
| 3.4 | GitHub - Commits de la rama | Commits visibles en GitHub |

### 4. Reto 2 — Pull

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 4.1 | `git fetch origin` | Obtener cambios del remoto |
| 4.2 | `git log --oneline --all --graph` | Historial antes del pull |
| 4.3 | `git pull origin develop` | Pull exitoso |
| 4.4 | `git log --oneline` | Historial después del pull |

### 5. Reto 3 — Rebase

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 5.1 | `git log --oneline --graph feature/modulos` | Historial antes del rebase |
| 5.2 | `git rebase develop` | Comando de rebase ejecutado |
| 5.3 | `git log --oneline --graph feature/modulos` | Historial después del rebase (lineal) |

### 6. Reto 4 — Conflicto de Merge

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 6.1 | `git merge feature/documentacion-general` (conflicto) | Mensaje de conflicto en terminal |
| 6.2 | `git status` | Archivos con conflicto marcados |
| 6.3 | README.md con marcadores de conflicto | `<<<<<<<`, `=======`, `>>>>>>>` visibles |
| 6.4 | README.md después de resolver | Contenido combinado sin marcadores |
| 6.5 | `git add README.md && git commit` | Commit de resolución |
| 6.6 | `git status` | Estado limpio después de resolver |

### 7. Reto 5 — Squash

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 7.1 | `git log --oneline feature/evaluaciones` | Múltiples commits pequeños (ANTES) |
| 7.2 | `git rebase -i HEAD~N` | Editor interactivo con commits a squash |
| 7.3 | `git log --oneline feature/evaluaciones` | Un solo commit consolidado (DESPUÉS) |

### 8. Reto 6 — Cherry-pick

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 8.1 | `git log --oneline experimental/buenas-practicas-avanzadas` | Commits en rama experimental |
| 8.2 | `git cherry-pick <commit-hash>` | Cherry-pick del commit específico |
| 8.3 | `git log --oneline` | Commit trasladado en la rama destino |

### 9. Reto 7 — Conflicto durante Cherry-pick

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 9.1 | `git cherry-pick <commit-hash>` (conflicto) | Mensaje de conflicto |
| 9.2 | `git status` | Archivos con conflicto |
| 9.3 | Archivo con marcadores de conflicto | Contenido conflictivo visible |
| 9.4 | Resolución del conflicto | Contenido coherente sin marcadores |
| 9.5 | `git add . && git cherry-pick --continue` | Cherry-pick completado |

### 10. Reto 8 — Pull Request Final

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 10.1 | GitHub - Página de creación de PR | Formulario de PR con título y descripción |
| 10.2 | PR - Archivos modificados | Lista de archivos cambiados en el PR |
| 10.3 | PR - Commits incluidos | Commits que forman parte del PR |
| 10.4 | PR - Conversación/Revisión | Comentarios y estado de revisión |
| 10.5 | PR - Merge exitoso | PR mergeado con estado "Merged" |

### 11. Estado Final del Repositorio

| # | Comando / Acción | Descripción de la captura |
|---|------------------|---------------------------|
| 11.1 | `git status` | Estado limpio final |
| 11.2 | `git branch -a` | Todas las ramas finales |
| 11.3 | `git log --oneline --graph --all` | Historial completo y limpio |
| 11.4 | GitHub - Página principal | Repositorio en GitHub con estructura visible |
| 11.5 | `git log --oneline main` | Historial de main |

---

## Errores Encontrados y Soluciones

Documentar cualquier error encontrado durante el desarrollo del taller:

| # | Error | Causa | Solución |
|---|-------|-------|----------|
| 1 | | | |
| 2 | | | |
| 3 | | | |

> **Nota**: Agregar filas según se encuentren errores durante la ejecución del taller. Ejemplos comunes:
> - Conflicto de merge no resuelto
> - Push rechazado por cambios en el remoto
> - Cherry-pick con conflicto
> - Squash con merge commit

---

## Herramientas Utilizadas

| Herramienta | Versión | Uso |
|-------------|---------|-----|
| Git | 2.44+ | Control de versiones |
| GitHub | Web | Repositorio remoto |
| VS Code | 1.90+ | Editor de código |
| Terminal | PowerShell / Git Bash | Ejecución de comandos |

---

## Notas Adicionales

- Todas las capturas deben mostrar la **fecha y hora** del sistema
- Las capturas de terminal deben mostrar el **directorio de trabajo** actual
- Las capturas de GitHub deben mostrar la **URL del repositorio**
- Guardar las evidencias en formato **PNG** o **JPG**
- Nombrar los archivos de captura de forma descriptiva (ej: `01-git-status-inicial.png`)