# liquibase-preview

Preview de migraciones de base de datos usando Liquibase (update-sql). Genera el SQL sin ejecutar.

## Uso

```yaml
# .github/workflows/liquibase-preview.yml
name: DB Migration - Preview

on:
  workflow_dispatch:
    inputs:
      database:
        description: 'Target database'
        type: choice
        options:
          - my-db-main
          - my-db-keycloak

jobs:
  preview:
    uses: TetherEducation/tether-ci-cd/.github/workflows/liquibase-preview.yml@staging
    with:
      project_id: my-gcp-project
      region: us-central1
      wif_provider: projects/123/locations/global/workloadIdentityPools/github/providers/github
      service_account: my-sa@my-project.iam.gserviceaccount.com
      artifact_registry: us-central1-docker.pkg.dev
      repository: my-repo
      image_name: db-migration
      job_name: my-db-migration-job
      database_name: ${{ inputs.database }}
      db_secret_name: my-db-postgres-password
    secrets:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

## Inputs

| Input | Tipo | Requerido | Default | Descripcion |
|-------|------|-----------|---------|-------------|
| `project_id` | string | Si | - | GCP Project ID |
| `region` | string | No | us-central1 | GCP region |
| `wif_provider` | string | Si | - | WIF provider path |
| `service_account` | string | Si | - | Service account email |
| `artifact_registry` | string | Si | - | Artifact Registry URL |
| `repository` | string | Si | - | Nombre del repositorio |
| `image_name` | string | Si | - | Nombre de la imagen Docker |
| `job_name` | string | Si | - | Nombre del Cloud Run Job |
| `database_name` | string | Si | - | Nombre de la base de datos |
| `changelog_file` | string | No | changelog.master.xml | Archivo changelog |
| `db_secret_name` | string | Si | - | Secret con password DB |
| `dockerfile` | string | No | Dockerfile.liquibase | Dockerfile a usar |
| `apply_workflow_name` | string | No | liquibase-apply.yml | Workflow de apply para link |

## Secrets

| Secret | Requerido | Descripcion |
|--------|-----------|-------------|
| `SLACK_WEBHOOK_URL` | No | Webhook para notificaciones |

## Flujo

1. Build y push imagen Docker con changelogs
2. Obtiene DB_HOST del Cloud Run Job existente
3. Ejecuta `update-sql` (preview sin cambios)
4. Sube artifact con output SQL
5. Notifica a Slack con Preview Run ID

## Notificaciones Slack

**Con cambios detectados:**

- Header amarillo
- Preview Run ID para copiar
- Botones: Ver SQL | Aplicar Migracion

**Sin cambios:**

- Header gris
- Mensaje "No hay migraciones pendientes"

**Error:**

- Header rojo
- Boton Ver Logs

## Siguiente paso

Despues del preview, usa [liquibase-apply](liquibase-apply.md) con el `Preview Run ID`.
