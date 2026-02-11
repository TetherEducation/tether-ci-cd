# Tether CI/CD Workflows

Workflows reutilizables de GitHub Actions para builds, deploys, testing y utilidades.

## Quick Start

```yaml
jobs:
  deploy:
    uses: TetherEducation/tether-ci-cd/.github/workflows/deploy-eks.yml@staging
    with:
      ENVIRONMENT: staging
      ECR_REPOSITORY: my-app
      # ... otros inputs
    secrets:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      # ... otros secrets
```

> **Nota:** Los secrets comunes ya estan configurados a nivel de organizacion TetherEducation.

## Workflows Disponibles

### Builds (AWS ECR)

| Workflow           | Descripcion                   | Doc                                |
| ------------------ | ----------------------------- | ---------------------------------- |
| `build-django.yml` | Build Python/Django con cache | [Ver](docs/builds/build-django.md) |
| `build-go.yml`     | Build Go con GOPRIVATE        | [Ver](docs/builds/build-go.md)     |
| `build-image.yml`  | Build Docker generico         | [Ver](docs/builds/build-image.md)  |

### Deploys (AWS EKS)

| Workflow            | Descripcion                   | Doc                                  |
| ------------------- | ----------------------------- | ------------------------------------ |
| `deploy-django.yml` | Deploy Django a EKS con Slack | [Ver](docs/deploys/deploy-django.md) |
| `deploy-eks.yml`    | Deploy Go a EKS (limpio)      | [Ver](docs/deploys/deploy-eks.md)    |
| `deploy-go.yml`     | Deploy Go a EKS con Slack     | [Ver](docs/deploys/deploy-go.md)     |

### Deploys (GCP Cloud Run)

| Workflow                            | Descripcion                             | Doc                                                  |
| ----------------------------------- | --------------------------------------- | ---------------------------------------------------- |
| `deploy-explorer-docker.yml`        | Deploy multi-region a Cloud Run         | [Ver](docs/deploys/deploy-explorer-docker.md)        |
| `deploy-explorer-docker-django.yml` | Deploy Django a Cloud Run + migraciones | [Ver](docs/deploys/deploy-explorer-docker-django.md) |

### Actions (GCP GCS)

| Action           | Descripcion                                              | Doc                                  |
| ---------------- | -------------------------------------------------------- | ------------------------------------ |
| `spa-sync-gcs`   | Sync SPA a GCS con index no-cache y opcional CDN invalidation | [Ver](docs/deploys/spa-sync-gcs.md) |

### Database

| Workflow                | Descripcion                         | Doc                                       |
| ----------------------- | ----------------------------------- | ----------------------------------------- |
| `liquibase-preview.yml` | Preview de migraciones (update-sql) | [Ver](docs/database/liquibase-preview.md) |
| `liquibase-apply.yml`   | Aplicar migraciones (update)        | [Ver](docs/database/liquibase-apply.md)   |

### Testing

| Workflow          | Descripcion                         | Doc                                |
| ----------------- | ----------------------------------- | ---------------------------------- |
| `test-django.yml` | Tests Django con PostgreSQL/PostGIS | [Ver](docs/testing/test-django.md) |

### Utilities

| Workflow            | Descripcion                       | Doc                                    |
| ------------------- | --------------------------------- | -------------------------------------- |
| `notify-slack.yml`  | Notificaciones Slack para deploys | [Ver](docs/utilities/notify-slack.md)  |
| `check-version.yml` | Versionado y tags automaticos     | [Ver](docs/utilities/check-version.md) |

## Configuracion

| Archivo                                            | Descripcion                      |
| -------------------------------------------------- | -------------------------------- |
| [config/slack-users.json](config/slack-users.json) | Mapeo GitHub username → Slack ID |

## Guias

- [Getting Started](docs/getting-started.md) - Como integrar estos workflows en tu repo

## Ramas

| Rama      | Uso                    |
| --------- | ---------------------- |
| `staging` | Desarrollo y pruebas   |
| `master`  | Produccion (protegida) |

> Para cambios: crear PR a `staging`, probar, luego PR a `master`.
