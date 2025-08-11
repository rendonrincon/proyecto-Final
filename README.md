# Proyecto Final – API de Festivos con CI/CD y Observabilidad

## Descripción
Este proyecto forma parte de un conjunto de repositorios del estudio de despliegue de aplicaciones en AWS.  
Aquí implementamos la **API de Festivos** en Spring Boot con Docker, desplegada mediante **AWS ECS**, **CodePipeline** y **CodeBuild**, incluyendo observabilidad y monitoreo en **CloudWatch**, **SNS** y **Grafana**.

---

## Pipeline y Observabilidad

El flujo completo incluye:
- Pruebas unitarias e integración.
- Pruebas de carga y smoke tests.
- Deploy automatizado a Staging y Producción.
- Monitoreo en tiempo real.

### Diagrama del Pipeline CI/CD

```mermaid
graph LR
    A[Desarrollador\nPush a GitHub] --> B[AWS CodeBuild\n(Build & Test)]
    B --> C[Amazon ECR\nPush Imagen Docker]
    C --> D[AWS ECS (Staging)]
    D --> E[Smoke Tests\n(/api/health, latencia)]
    E --> F[Manual Approval]
    F --> G[AWS ECS (Producción)]
    G --> H[CloudWatch + SNS\nMonitoreo & Alertas]
    H --> I[Dashboard Grafana]
Vista Gráfica

Repositorios relacionados
Este proyecto está asociado con otros repositorios del mismo estudio:

API de Festivos – Código fuente

Frontend del Proyecto

Infraestructura como código (IaC)

Proyecto Final – Documentación
