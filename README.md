# proyecto-Final
## Diagrama del Pipeline CI/CD + Observabilidad

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
📌 Este pipeline asegura que:

Todo cambio pase por pruebas automáticas y manual approval antes de producción.

Los smoke tests validen latencia y salud de la API.

La observabilidad esté integrada con CloudWatch, SNS y Grafana para detectar y notificar incidentes.

Vista Gráfica

markdown
Copiar
Editar

### Cómo usarlo en tu repo
1. Guarda la imagen `pipeline_festivos_api.png` en la carpeta raíz del repo.
2. Copia este bloque al `README.md`.
3. En GitHub, se verá el diagrama Mermaid y también la imagen renderizada como backup
