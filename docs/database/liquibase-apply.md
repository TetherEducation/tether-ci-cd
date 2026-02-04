# liquibase-apply

Aplicar migraciones de base de datos usando Liquibase (update). Requiere un preview previo.

## Uso

```yaml
# .github/workflows/liquibase-apply.yml
name: DB Migration - Apply

on:
  workflow_dispatch:
    inputs:
      preview_run_id:
        description: "Run ID del Preview (ver Slack o Actions)"
        required: true
        type: string
      database:
        description: "Target database"
        type: choice
        options:
          - my-db-main
          - my-db-keycloak

jobs:
  apply:
    uses: TetherEducation/tether-ci-cd/.github/workflows/liquibase-apply.yml@staging
    with:
      preview_run_id: ${{ inputs.preview_run_id }}
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

| Input               | Tipo   | Requerido | Default              | Descripcion                     |
| ------------------- | ------ | --------- | -------------------- | ------------------------------- |
| `preview_run_id`    | string | Si        | -                    | Run ID del preview (validacion) |
| `project_id`        | string | Si        | -                    | GCP Project ID                  |
| `region`            | string | No        | us-central1          | GCP region                      |
| `wif_provider`      | string | Si        | -                    | WIF provider path               |
| `service_account`   | string | Si        | -                    | Service account email           |
| `artifact_registry` | string | Si        | -                    | Artifact Registry URL           |
| `repository`        | string | Si        | -                    | Nombre del repositorio          |
| `image_name`        | string | Si        | -                    | Nombre de la imagen Docker      |
| `job_name`          | string | Si        | -                    | Nombre del Cloud Run Job        |
| `database_name`     | string | Si        | -                    | Nombre de la base de datos      |
| `changelog_file`    | string | No        | changelog.master.xml | Archivo changelog               |
| `db_secret_name`    | string | Si        | -                    | Secret con password DB          |
| `dockerfile`        | string | No        | Dockerfile.liquibase | Dockerfile a usar               |

## Secrets

| Secret              | Requerido | Descripcion                 |
| ------------------- | --------- | --------------------------- |
| `SLACK_WEBHOOK_URL` | No        | Webhook para notificaciones |

## Flujo

1. **validate-preview**: Verifica que existe artifact del preview
2. **apply**:
   - Build y push imagen
   - Ejecuta `update` (aplica cambios)
3. **notify-success/failure**: Notifica resultado

## Validacion de Preview

El workflow verifica que:

- Existe un artifact `liquibase-preview-{preview_run_id}`
- Esto previene aplicar sin haber revisado el SQL

## Notificaciones Slack

**Exito:**

- Header verde
- Database y usuario que aplico

**Error:**

- Header rojo
- Boton Ver Logs

## Flujo completo

```plaintext
1. Ejecutar liquibase-preview.yml
2. Revisar SQL en los logs
3. Copiar Preview Run ID del Slack
4. Ejecutar liquibase-apply.yml con el Run ID
5. Verificar notificacion de exito
```
