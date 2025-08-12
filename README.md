Proyecto_Final_Arq_Nube
Descripción
API de Festivos desarrollada con Spring Boot y PostgreSQL, contenida en Docker. Desplegada en AWS ECS con pipeline CI/CD automatizado (CodeBuild, CodePipeline) e integración de monitoreo (CloudWatch, SNS, Grafana).

Arquitectura
Backend: Spring Boot (arquitectura hexagonal).

Base de datos: PostgreSQL 15.

Contenedores: Docker & Docker Compose.

Análisis: SonarQube + JaCoCo (cobertura).

Infraestructura: AWS CloudFormation (IaC).

Requisitos previos
Docker & Docker Compose instalados.

Maven (para pruebas locales).

AWS CLI configurado (aws configure).

Cuenta AWS con permisos CodeBuild, ECR, ECS.

Clonar proyecto
bash
Copiar
Editar
git clone https://github.com/rendonrincon/proyecto-Final.git
cd proyecto-Final
Levantar servicios con Docker Compose (local)
bash
Copiar
Editar
docker-compose up -d postgres sonarqube
# Esperar inicialización (30-60s)
docker-compose logs -f postgres
docker-compose logs -f sonarqube

docker-compose up -d api-festivos
Verificar salud de la API:

bash
Copiar
Editar
curl http://localhost:8080/actuator/health
Acceder a SonarQube:

http://localhost:9000 (admin/admin)

Comandos útiles para desarrollo
Ejecutar pruebas unitarias y cobertura:

bash
Copiar
Editar
cd apiFestivos
mvn clean verify
Generar reportes de cobertura agregada:

bash
Copiar
Editar
mvn jacoco:report-aggregate
Ejecutar análisis SonarQube:

bash
Copiar
Editar
mvn sonar:sonar \
  -Dsonar.projectKey=festivos-api \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=TU_TOKEN
Limpiar y reconstruir imágenes Docker:

bash
Copiar
Editar
docker-compose down -v
docker-compose build --no-cache
docker-compose up -d
Pipeline CI/CD en AWS
Variables de entorno en CodeBuild
AWS_ACCOUNT_ID

AWS_DEFAULT_REGION

IMAGE_REPO_NAME=festivos-api

SONAR_HOST_URL (opcional)

SONAR_TOKEN (para análisis SonarQube)

Fases del pipeline (definidas en buildspec-backend.yml)
Install: Configuración de entorno (Java 17, Docker).

Pre-build: Login a ECR, generación de tag.

Build: Ejecutar pruebas, generar artefactos, construir imagen Docker.

Post-build: Push de imagen a ECR, generar archivo imagedefinitions.json.

Despliegue en ECS
Configuración de Task Definition con variables de entorno para conexión a RDS.

Deploy automatizado desde CodePipeline usando imagen en ECR.

Smoke tests ejecutados en ambiente Staging.

Approval manual para producción.

Monitoreo con CloudWatch, SNS y dashboards Grafana.

Monitoreo y Observabilidad
Health endpoint: /actuator/health

Logs enviados a CloudWatch Logs.

Métricas y alarmas en CloudWatch y SNS.

Dashboard Grafana configurado para visualización en tiempo real.

Dashboard Grafana:
https://rendonrincon.grafana.net/a/grafana-setupguide-app/getting-started

Repositorios Relacionados
Backend API Festivos: TT_ProyectoFinal_Backend

Infraestructura AWS IaC: udeabootcamp-infra

Infraestructura Azure (alternativa): udeabootcamp-infraaznc

Cómo contribuir
Fork y clona el repo.

Crear una rama nueva para tu feature o fix.

Ejecutar pruebas localmente:

bash
Copiar
Editar
make test-coverage
Hacer commit y push.

Crear pull request para revisión.

