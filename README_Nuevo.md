# Proyecto Final - API de Festivos en AWS

## 🛠️ Tecnologías Utilizadas
- **Backend**: Spring Boot 3.2 + Java 17
- **Infraestructura**: Docker + AWS ECS (Fargate)
- **CI/CD**: AWS CodePipeline + CodeBuild
- **Monitoreo**: CloudWatch + Grafana

## 📊 Arquitectura del Sistema
```mermaid
graph TD
    A[Developer] -->|Git Push| B[GitHub]
    B --> C[CodeBuild]
    C --> D[ECR]
    D --> E[ECS Cluster]
    E --> F[CloudWatch Metrics]
    F --> G[Grafana Dashboard]
🚀 Despliegue Local
bash
# 1. Clonar repositorio
git clone https://github.com/rendonrincon/proyecto-Final.git

# 2. Iniciar servicios
docker-compose up -d postgres prometheus grafana
🔄 Pipeline CI/CD
Etapa	Comando	Descripción
Build	mvn clean package	Empaquetado con Maven
Test	mvn test	Pruebas unitarias
Deploy	aws ecs update-service	Despliegue en ECS
📈 Métricas Clave
ini
# cloudwatch-alarms.ini
HighCPUUsage:
  Threshold: 70%
  Period: 5 minutes
🐛 Troubleshooting
bash
# Ver logs de la API:
docker-compose logs -f api

# Conectar a PostgreSQL:
PGPASSWORD=sa psql -h localhost -U postgres

