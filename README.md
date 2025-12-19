# Demo GitOps | ArgoCD + Kustomize 

# Descripción

Este repositorio implementa un modelo de **despliegue GitOps** utilizando **ArgoCD** y **Kustomize**.
Define de forma declarativa el estado deseado del cluster de **Kubernetes**.

## Requisitos previos

Para ejecutar este proyecto localmente se requiere:

- Docker
- Kubernetes (Minikube)
- ArgoCD instalado en el cluster

Los namespaces utilizados por el proyecto son:
- `argocd`
- `dev`
- `test`

## Responsabilidad del repositorio

- Definir qué aplicaciones se despliegan
- Definir en qué ambiente se despliegan (DEV / TEST)
- Controlar qué versión corre en cada ambiente

Este repositorio **no construye imágenes** ni ejecuta CI.

## Arquitectura

### Microservicios
- **Front**: aplicación web
- **Back**: API backend (FastApi)

Cada microservicio se despliega de forma independiente con su propio:
- Deployment
- Service

### Ambientes
- `dev` (namespace `dev`)
- `test` (namespace `test`)

Cada ambiente es gestionado mediante **overlays de Kustomize**.

## Estructura del repositorio
'''
demo-argo-platform/
├── applications/
│ ├── dev/
│ │ ├── demo-front.yaml
│ │ └── demo-back.yaml
│ └── test/
│ ├── demo-front.yaml
│ └── demo-back.yaml
└── kustomize/
├── base/
│ ├── front/
│ └── back/
├── dev/
│ ├── front/
│ └── back/
└── test/
├── front/
└── back/
'''

- **base**: recursos comunes (sin datos de ambiente)
- **dev / test**: definición de diferencias por ambiente, TAGs de imagen

## Modelo de despliegue

1. El pipeline de CI (demo-app) construye y publica imágenes
2. La versión deseada se define aquí mediante un cambio de TAG
3. ArgoCD detecta el cambio y sincroniza el cluster


Ejemplo de definición de versión en DEV:

```yaml
images:
  - name: ghcr.io/organization-demo-task/demo-back
    newTag: v0.1.1-dev

