# UdeA API Festivos - Proyecto Final

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![AWS](https://img.shields.io/badge/AWS-Pipeline-orange.svg)
![Node.js](https://img.shields.io/badge/Node.js-18.x-green.svg)
![Status](https://img.shields.io/badge/build-passing-brightgreen.svg)

## Descripción del Proyecto

API RESTful desarrollada para la Universidad de Antioquia (UdeA) que permite consultar y gestionar información sobre días festivos en Colombia. Este proyecto implementa una arquitectura de CI/CD completa utilizando AWS Pipeline para automatizar el proceso de desarrollo, construcción y despliegue.

### Funcionalidades Principales

- 🗓️ **Consulta de Festivos**: API para obtener días festivos por año, mes o rango de fechas
- 🔍 **Validación de Fechas**: Verificar si una fecha específica es día festivo
- 📅 **Tipos de Festivos**: Clasificación entre festivos fijos, móviles y puentes
- 🌐 **Documentación API**: Swagger/OpenAPI para documentación interactiva
- 🔒 **Autenticación**: Sistema de autenticación para acceso controlado
- 📊 **Logging y Monitoreo**: Sistema completo de trazabilidad y métricas

## Arquitectura del Proyecto

Esta aplicación implementa una arquitectura de CI/CD robusta utilizando **AWS Pipeline** con tres etapas principales que garantizan un proceso de desarrollo automatizado y confiable.

### Componentes de la Arquitectura

#### 1. **Source Stage (Etapa de Código Fuente)**
- **GitHub Repository**: Repositorio principal del código fuente
- **Webhook Integration**: Configuración de webhooks para disparar automáticamente el pipeline
- **Branch Strategy**: 
  - `main`: Rama de producción
  - `develop`: Rama de desarrollo
  - `feature/*`: Ramas para nuevas funcionalidades
- **Code Review**: Pull requests obligatorios antes de merge a main

#### 2. **CodeBuild Stage (Etapa de Construcción)**
- **AWS CodeBuild**: Servicio que ejecuta la construcción y pruebas
- **Multi-Phase Build Process**:
  - **Install**: Instalación de dependencias Node.js
  - **Pre-build**: Linting, análisis de código y configuración
  - **Build**: Ejecución de pruebas unitarias e integración
  - **Post-build**: Empaquetado y preparación de artefactos
- **Quality Gates**: Umbrales de calidad que debe pasar el código
- **Artifact Generation**: Creación de paquetes optimizados para despliegue

#### 3. **Deploy Stage (Etapa de Despliegue)**
- **AWS CodeDeploy**: Automatización del despliegue
- **Environment Strategy**:
  - **Development**: Despliegue automático desde develop
  - **Staging**: Despliegue automático desde main para QA
  - **Production**: Despliegue manual con aprobación
- **Blue/Green Deployment**: Estrategia para minimizar downtime
- **Health Checks**: Verificación automática post-despliegue
- **Rollback Capability**: Capacidad de rollback automático

### Diagrama de Arquitectura

```mermaid
graph TB
    subgraph "Development Environment"
        DEV[Desarrollador]
        IDE[VS Code / IntelliJ]
        GIT[Git Local]
        
        DEV --> IDE
        IDE --> GIT
    end
    
    subgraph "Source Control"
        GITHUB[GitHub Repository<br/>racso534/UdeA_Api_Festivos]
        PR[Pull Request]
        WEBHOOK[Webhook]
        
        GIT -->|Push| GITHUB
        GITHUB --> PR
        PR -->|Approved| WEBHOOK
    end
    
    subgraph "AWS Pipeline - 3 Stages"
        direction TB
        
        subgraph "1. Source Stage"
            SRC_DETECT[Detect Changes]
            SRC_CHECKOUT[Checkout Code]
            SRC_ARTIFACT[Source Artifacts]
            
            SRC_DETECT --> SRC_CHECKOUT
            SRC_CHECKOUT --> SRC_ARTIFACT
        end
        
        subgraph "2. CodeBuild Stage"
            BUILD_ENV[Build Environment<br/>Node.js 18]
            INSTALL[npm install]
            LINT[ESLint & Prettier]
            TEST[Unit & Integration Tests]
            BUILD[npm run build]
            PACKAGE[Create Deployment Package]
            
            BUILD_ENV --> INSTALL
            INSTALL --> LINT
            LINT --> TEST
            TEST --> BUILD
            BUILD --> PACKAGE
        end
        
        subgraph "3. Deploy Stage"
            DEPLOY_DEV[Deploy to Development]
            DEPLOY_STG[Deploy to Staging]
            APPROVAL[Manual Approval]
            DEPLOY_PROD[Deploy to Production]
            HEALTH[Health Checks]
            
            DEPLOY_DEV --> DEPLOY_STG
            DEPLOY_STG --> APPROVAL
            APPROVAL --> DEPLOY_PROD
            DEPLOY_PROD --> HEALTH
        end
        
        SRC_ARTIFACT --> BUILD_ENV
        PACKAGE --> DEPLOY_DEV
    end
    
    WEBHOOK -->|Triggers| SRC_DETECT
    
    subgraph "AWS Infrastructure"
        S3[S3 Bucket<br/>Build Artifacts]
        EC2[EC2 Instances<br/>Auto Scaling Group]
        ALB[Application Load Balancer]
        RDS[RDS Database<br/>MySQL/PostgreSQL]
        LAMBDA[Lambda Functions<br/>API Gateway]
        
        ALB --> EC2
        EC2 --> RDS
        LAMBDA --> RDS
    end
    
    PACKAGE -.->|Store| S3
    S3 -.->|Deploy From| DEPLOY_DEV
    HEALTH --> ALB
    
    subgraph "Monitoring & Notifications"
        CW[CloudWatch<br/>Logs & Metrics]
        SNS[SNS Notifications<br/>Email/Slack]
        CT[CloudTrail<br/>Audit Logs]
        
        BUILD_ENV -.->|Logs| CW
        DEPLOY_PROD -.->|Notifications| SNS
        SRC_DETECT -.->|Audit| CT
    end
    
    subgraph "External Services"
        SWAGGER[API Documentation<br/>Swagger UI]
        POSTMAN[Postman Collection]
        
        DEPLOY_PROD --> SWAGGER
        SWAGGER -.-> POSTMAN
    end
```

### Flujo de CI/CD Detallado

1. **Code Commit** → Desarrollador realiza push a GitHub
2. **Webhook Trigger** → GitHub notifica a AWS Pipeline automáticamente
3. **Source Stage** → Pipeline obtiene el código fuente y crea artefactos iniciales
4. **Build Stage** → CodeBuild ejecuta:
   - Instalación de dependencias (`npm install`)
   - Análisis de código (`eslint`, `prettier`)
   - Ejecución de pruebas (`jest`, `supertest`)
   - Construcción optimizada (`npm run build`)
   - Empaquetado para despliegue
5. **Deploy Stage** → Despliegue escalonado:
   - **Development**: Automático para pruebas rápidas
   - **Staging**: Automático para QA y pruebas de integración
   - **Production**: Manual con aprobación obligatoria

## Estructura del Proyecto

```
UdeA_Api_Festivos/
├── src/
│   ├── controllers/          # Controladores de la API
│   │   ├── festivoController.js
│   │   └── healthController.js
│   ├── models/              # Modelos de datos
│   │   ├── Festivo.js
│   │   └── Usuario.js
│   ├── routes/              # Definición de rutas
│   │   ├── festivos.js
│   │   └── auth.js
│   ├── middlewares/         # Middlewares personalizados
│   │   ├── auth.js
│   │   ├── validation.js
│   │   └── errorHandler.js
│   ├── services/            # Lógica de negocio
│   │   ├── festivoService.js
│   │   └── calculadoraFestivos.js
│   ├── config/              # Configuración
│   │   ├── database.js
│   │   ├── aws.js
│   │   └── environment.js
│   └── utils/               # Utilidades
│       ├── dateHelper.js
│       └── logger.js
├── tests/                   # Pruebas automatizadas
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docs/                    # Documentación
│   ├── api/                 # Documentación API
│   └── deployment/          # Guías de despliegue
├── infrastructure/          # Infraestructura como código
│   ├── cloudformation/
│   └── terraform/
├── .github/                 # GitHub Actions (opcional)
│   └── workflows/
├── buildspec.yml           # AWS CodeBuild configuration
├── appspec.yml            # AWS CodeDeploy configuration
├── docker-compose.yml     # Desarrollo local
├── Dockerfile             # Contenedor de aplicación
└── package.json           # Dependencias y scripts
```

## Tecnologías y Servicios Utilizados

### Backend Technologies
- **Node.js 18.x**: Runtime de JavaScript
- **Express.js**: Framework web para API REST
- **MySQL/PostgreSQL**: Base de datos relacional
- **Sequelize ORM**: Mapeador objeto-relacional
- **JWT**: Autenticación basada en tokens
- **Joi**: Validación de esquemas
- **Winston**: Sistema de logging

### AWS Services
- **AWS CodePipeline**: Orquestación del CI/CD
- **AWS CodeBuild**: Servicio de construcción
- **AWS CodeDeploy**: Automatización de despliegues
- **Amazon EC2**: Infraestructura de computación
- **Application Load Balancer**: Balanceador de carga
- **Amazon RDS**: Base de datos gestionada
- **Amazon S3**: Almacenamiento de artefactos
- **Amazon CloudWatch**: Monitoreo y logging
- **Amazon SNS**: Sistema de notificaciones
- **AWS Systems Manager**: Gestión de parámetros

### Development & Testing
- **Jest**: Framework de pruebas
- **Supertest**: Pruebas de APIs
- **ESLint**: Linter de código
- **Prettier**: Formateador de código
- **Swagger/OpenAPI**: Documentación de API
- **Postman**: Colección de pruebas de API

## Configuración de Pipeline

### buildspec.yml
```yaml
version: 0.2

env:
  variables:
    NODE_ENV: "test"
  parameter-store:
    DATABASE_URL: "/udea-api/database-url"
    JWT_SECRET: "/udea-api/jwt-secret"

phases:
  install:
    runtime-versions:
      nodejs: 18
    commands:
      - echo "Installing dependencies..."
      - npm ci --only=production

  pre_build:
    commands:
      - echo "Running pre-build tasks..."
      - npm run lint
      - echo "Running unit tests..."
      - npm run test:unit
      - echo "Running integration tests..."
      - npm run test:integration

  build:
    commands:
      - echo "Building the application..."
      - npm run build
      - echo "Running security audit..."
      - npm audit --audit-level moderate

  post_build:
    commands:
      - echo "Build completed successfully"
      - echo "Generating API documentation..."
      - npm run docs:generate

artifacts:
  files:
    - '**/*'
  base-directory: '.'
  exclude-paths:
    - 'node_modules/**/*'
    - 'tests/**/*'
    - '.git/**/*'

reports:
  jest_reports:
    files:
      - 'coverage/clover.xml'
    base-directory: '.'
    file-format: 'CLOVERXML'
```

### appspec.yml
```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /opt/udea-api-festivos
    overwrite: yes

permissions:
  - object: /opt/udea-api-festivos
    pattern: "**"
    owner: ec2-user
    group: ec2-user
    mode: 755

hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: root

  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 300
      runas: ec2-user

  ApplicationStop:
    - location: scripts/stop_server.sh
      timeout: 300
      runas: ec2-user

  ValidateService:
    - location: scripts/validate_service.sh
      timeout: 300
      runas: ec2-user
```

## APIs Disponibles

### Endpoints Principales

| Método | Endpoint | Descripción | Autenticación |
|--------|----------|-------------|---------------|
| `GET` | `/api/v1/festivos` | Lista todos los festivos del año actual | No |
| `GET` | `/api/v1/festivos/{año}` | Festivos de un año específico | No |
| `GET` | `/api/v1/festivos/{año}/{mes}` | Festivos de un mes específico | No |
| `GET` | `/api/v1/festivos/verificar/{fecha}` | Verifica si una fecha es festivo | No |
| `GET` | `/api/v1/festivos/proximos` | Próximos 5 festivos | No |
| `GET` | `/api/v1/festivos/tipos` | Tipos de festivos disponibles | No |
| `POST` | `/api/v1/auth/login` | Autenticación de usuario | No |
| `GET` | `/api/v1/health` | Health check del servicio | No |

### Ejemplos de Respuesta

```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "nombre": "Año Nuevo",
      "fecha": "2024-01-01",
      "tipo": "fijo",
      "descripcion": "Celebración del primer día del año"
    },
    {
      "id": 2,
      "nombre": "Día de los Reyes Magos",
      "fecha": "2024-01-08",
      "tipo": "movil",
      "descripcion": "Epifanía del Señor"
    }
  ],
  "meta": {
    "total": 18,
    "año": 2024
  }
}
```

## Monitoreo y Alertas

### CloudWatch Dashboards
- **Application Metrics**: Latencia, throughput, errores
- **Infrastructure Metrics**: CPU, memoria, conexiones de BD
- **Business Metrics**: Consultas por endpoint, usuarios activos

### Alertas Configuradas
- **High Error Rate**: > 5% de errores en 5 minutos
- **High Latency**: Latencia > 2 segundos
- **Database Issues**: Conexiones fallidas a BD
- **Deployment Status**: Éxito/fallo de despliegues

### Notifications
- **Slack Integration**: Notificaciones en tiempo real
- **Email Alerts**: Resumen diario y alertas críticas
- **PagerDuty**: Escalamiento para incidentes críticos

## Guía de Desarrollo

### Configuración Local

```bash
# Clonar el repositorio
git clone https://github.com/racso534/UdeA_Api_Festivos.git
cd UdeA_Api_Festivos

# Instalar dependencias
npm install

# Configurar variables de entorno
cp .env.example .env
# Editar .env con tus configuraciones locales

# Ejecutar migraciones de BD
npm run db:migrate

# Iniciar en modo desarrollo
npm run dev

# Ejecutar pruebas
npm test

# Construir para producción
npm run build
```

### Scripts Disponibles

```json
{
  "scripts": {
    "dev": "nodemon src/index.js",
    "start": "node src/index.js",
    "build": "npm run lint && npm test",
    "test": "jest --coverage",
    "test:unit": "jest --testPathPattern=unit",
    "test:integration": "jest --testPathPattern=integration",
    "test:e2e": "jest --testPathPattern=e2e",
    "lint": "eslint src/ --fix",
    "format": "prettier --write src/",
    "docs:generate": "swagger-jsdoc -d swaggerDef.js src/routes/*.js -o swagger.json",
    "db:migrate": "sequelize-cli db:migrate",
    "db:seed": "sequelize-cli db:seed:all"
  }
}
```

## Seguridad y Mejores Prácticas

### Implementadas
- ✅ **Autenticación JWT**: Tokens seguros para APIs protegidas
- ✅ **Rate Limiting**: Límites de peticiones por IP
- ✅ **Input Validation**: Validación exhaustiva de entrada
- ✅ **SQL Injection Protection**: Uso de ORM y prepared statements
- ✅ **CORS Configuration**: Configuración segura de CORS
- ✅ **Security Headers**: Helmet.js para headers de seguridad
- ✅ **Environment Variables**: Configuración sensible en variables de entorno
- ✅ **Audit Logging**: Registro de todas las acciones importantes

### Variables de Entorno Requeridas

```bash
# Application
NODE_ENV=production
PORT=3000
API_VERSION=v1

# Database
DATABASE_URL=mysql://user:password@localhost:3306/udea_festivos
DB_SSL=true

# Authentication
JWT_SECRET=your-super-secret-jwt-key
JWT_EXPIRES_IN=24h

# AWS
AWS_REGION=us-east-1
AWS_ACCOUNT_ID=123456789012

# Monitoring
LOG_LEVEL=info
ENABLE_METRICS=true
```

## Contribución

### Workflow de Desarrollo
1. **Fork** del repositorio principal
2. **Clone** tu fork localmente
3. **Branch** para nueva funcionalidad: `git checkout -b feature/nueva-funcionalidad`
4. **Commit** con mensajes descriptivos
5. **Push** a tu fork: `git push origin feature/nueva-funcionalidad`
6. **Pull Request** al repositorio principal

### Estándares de Código
- **ESLint**: Configuración estricta para calidad de código
- **Prettier**: Formato consistente
- **Commit Messages**: Formato convencional (feat, fix, docs, etc.)
- **Test Coverage**: Mínimo 80% de cobertura
