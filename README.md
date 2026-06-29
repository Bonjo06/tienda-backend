# Backend y Base de Datos - Tienda de Perritos

## Arquitectura y Tecnologías
* **Orquestador:** AWS EKS (Elastic Kubernetes Service).
* **Contenedores:** Imágenes Docker almacenadas en Amazon ECR (`tienda-backend` y `tienda-db`).
* **Base de Datos:** MySQL (Desplegado como un Pod dentro de EKS).
* **Autoscaling:** Configurado con Horizontal Pod Autoscaler (HPA) 
* **Seguridad:** Gestión de credenciales de la base de datos mediante Kubernetes Secrets (`mysql-secret.yaml`).

## Estructura del Repositorio
* `/backend`: Código fuente y Dockerfile de la API (Node.js).
* `/db`: Configuración y Dockerfile de la base de datos MySQL.
* `/k8s`: Archivos de manifiesto YAML para el despliegue en Kubernetes (Deployment, Service, HPA, Secret).
* `.github/workflows/deploy-backend.yml`: Pipeline de CI/CD.

## Pipeline CI/CD (GitHub Actions)
El despliegue está 100% automatizado mediante GitHub Actions. Cada vez que se realiza un `push` a la rama `main`, el pipeline ejecuta los siguientes pasos:
1. Autenticación segura con AWS mediante IAM y Secrets de GitHub.
2. Construcción (`build`) de las imágenes Docker del Backend y la DB.
3. Subida (`push`) de las imágenes a los repositorios de Amazon ECR.
4. Actualización del Kubeconfig y despliegue automático en el clúster EKS (`devopseks`) en el namespace `tienda`.

## Comandos para interactuar con el cluster
Si necesitas verificar el estado del backend manualmente a través de CloudShell:

**Ver los pods activos:**
`kubectl get pods -n tienda`

**Ver el Autoscaling (HPA):**
`kubectl get hpa -n tienda`

**Ver los logs del backend (filtrando telemetría):**
`kubectl logs -l app=tienda-backend -n tienda | grep -v -E "OTLPExporterError|xray|stack"`
