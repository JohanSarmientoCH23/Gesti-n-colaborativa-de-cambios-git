# Guía de Instalación — EduCampus LMS

## Requisitos Previos

### Requisitos de Hardware

| Recurso | Mínimo | Recomendado |
|---------|--------|-------------|
| CPU | 2 núcleos | 4+ núcleos |
| RAM | 4 GB | 8+ GB |
| Disco | 20 GB libres | 50+ GB SSD |
| Red | 10 Mbps | 100+ Mbps |

### Requisitos de Software

| Componente | Versión mínima | Versión recomendada |
|------------|----------------|---------------------|
| Node.js | 18.x LTS | 20.x LTS |
| npm | 9.x | 10.x |
| Docker | 24.x | 25.x |
| Docker Compose | 2.x | 2.20+ |
| PostgreSQL | 15.x | 16.x |
| Redis | 7.x | 7.2+ |
| Git | 2.40+ | 2.44+ |

---

## Instalación con Docker (Recomendada)

### 1. Clonar el Repositorio

```bash
git clone https://github.com/JohanSarmientoCH23/Gesti-n-colaborativa-de-cambios-git.git
cd Gesti-n-colaborativa-de-cambios-git
```

### 2. Configurar Variables de Entorno

```bash
cp .env.example .env
```

Editar el archivo `.env` con los valores del entorno:

```env
# Base de datos
POSTGRES_HOST=postgres
POSTGRES_PORT=5432
POSTGRES_DB=educampus
POSTGRES_USER=educampus_admin
POSTGRES_PASSWORD=tu_password_seguro_aqui

# Redis
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=tu_password_redis_aqui

# JWT
JWT_SECRET=tu_jwt_secret_aqui_minimo_32_caracteres
JWT_EXPIRATION=3600

# API
API_PORT=3000
API_VERSION=v1
NODE_ENV=development

# CORS
CORS_ORIGIN=http://localhost:5173

# Archivos
STORAGE_PATH=./storage
MAX_UPLOAD_SIZE=50MB
```

### 3. Levantar Servicios con Docker Compose

```bash
docker compose up -d
```

Verificar que todos los contenedores estén ejecutándose:

```bash
docker compose ps
```

Resultado esperado:

```
NAME                    STATUS          PORTS
educampus-postgres      Up (healthy)    0.0.0.0:5432->5432/tcp
educampus-redis         Up (healthy)    0.0.0.0:6379->6379/tcp
educampus-api           Up (healthy)    0.0.0.0:3000->3000/tcp
educampus-web           Up              0.0.0.0:5173->5173/tcp
educampus-worker        Up              (sin puertos expuestos)
```

### 4. Ejecutar Migraciones y Semilla

```bash
docker compose exec api npm run db:migrate
docker compose exec api npm run db:seed
```

### 5. Verificar Instalación

```bash
curl http://localhost:3000/v1/health
```

Respuesta esperada:

```json
{
  "status": "healthy",
  "version": "1.0.0",
  "timestamp": "2026-06-23T00:00:00Z",
  "services": {
    "database": "connected",
    "cache": "connected",
    "storage": "available"
  }
}
```

### 6. Acceder a la Aplicación

| Servicio | URL |
|----------|-----|
| Frontend | http://localhost:5173 |
| API | http://localhost:3000/v1 |
| Documentación API | http://localhost:3000/docs |
| PgAdmin (dev) | http://localhost:5050 |
| Redis Commander (dev) | http://localhost:8081 |

---

## Instalación Manual (Desarrollo)

### 1. Instalar Dependencias

```bash
# Backend
cd backend
npm install

# Frontend
cd ../frontend
npm install
```

### 2. Configurar Base de Datos

```sql
-- Conectar como superusuario y crear base de datos
CREATE DATABASE educampus;
CREATE USER educampus_admin WITH PASSWORD 'tu_password';
GRANT ALL PRIVILEGES ON DATABASE educampus TO educampus_admin;
ALTER USER educampus_admin WITH SUPERUSER;
```

### 3. Ejecutar Migraciones

```bash
cd backend
npm run db:migrate
npm run db:seed
```

### 4. Iniciar Servicios

```bash
# Terminal 1: Backend
cd backend
npm run dev

# Terminal 2: Frontend
cd frontend
npm run dev
```

---

## Estructura del Repositorio

```
Gesti-n-colaborativa-de-cambios-git/
├── backend/
│   ├── src/
│   │   ├── config/          # Configuración de la aplicación
│   │   ├── modules/         # Módulos de negocio
│   │   │   ├── user/
│   │   │   ├── course/
│   │   │   ├── enrollment/
│   │   │   ├── evaluation/
│   │   │   └── ...
│   │   ├── shared/          # Utilidades compartidas
│   │   ├── middleware/      # Middleware HTTP
│   │   ├── events/          # Cola de eventos
│   │   └── main.ts          # Punto de entrada
│   ├── migrations/          # Migraciones de base de datos
│   ├── seeds/               # Datos de semilla
│   ├── tests/               # Pruebas unitarias y de integración
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── components/      # Componentes React
│   │   ├── pages/           # Páginas/rutas
│   │   ├── hooks/           # Custom hooks
│   │   ├── services/        # Servicios API
│   │   ├── stores/          # Estado global (Zustand)
│   │   └── App.tsx
│   └── package.json
├── docker/
│   ├── Dockerfile.api
│   ├── Dockerfile.web
│   └── nginx.conf
├── docs/                    # Documentación
├── evidencias/              # Evidencias del taller
├── docker-compose.yml
├── .env.example
├── .gitignore
├── README.md
└── CHANGELOG.md
```

---

## Comandos Útiles

### Desarrollo

```bash
# Ejecutar pruebas
npm test

# Ejecutar pruebas con cobertura
npm run test:coverage

# Linting
npm run lint

# Formateo
npm run format

# Type check
npm run typecheck
```

### Docker

```bash
# Reconstruir contenedores
docker compose up -d --build

# Ver logs
docker compose logs -f api

# Acceder a un contenedor
docker compose exec api sh

# Detener todo
docker compose down

# Detener y eliminar volúmenes
docker compose down -v
```

### Base de Datos

```bash
# Crear migración
npm run db:migration:create --name=AddNewField

# Ejecutar migraciones
npm run db:migrate

# Revertir última migración
npm run db:migrate:undo

# Ejecutar semilla
npm run db:seed

# Reset completo
npm run db:reset
```

---

## Actualizaciones

Para actualizar EduCampus LMS a la última versión:

```bash
# Detener servicios actuales
docker compose down

# Actualizar código
git pull origin main

# Reconstruir contenedores
docker compose up -d --build

# Ejecutar migraciones pendientes
docker compose exec api npm run db:migrate

# Verificar versión
curl http://localhost:3000/v1/version
```

---

## Solución de Problemas

### Puerto en uso

```bash
# Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F

# macOS/Linux
lsof -ti:3000 | xargs kill -9
```

### Permisos de volumen Docker

```bash
# Linux
sudo chown -R $USER:$USER ./storage
chmod -R 755 ./storage
```

### Error de conexión a base de datos

```bash
# Verificar que PostgreSQL esté corriendo
docker compose exec postgres pg_isready -U educampus_admin

# Verificar credenciales
docker compose exec postgres psql -U educampus_admin -d educampus -c "\conninfo"
```

### Limpiar caché de npm

```bash
rm -rf node_modules package-lock.json
npm install
```