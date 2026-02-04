# deploy-explorer-docker-django

Deploy Django a GCP Cloud Run con migraciones automaticas y soporte multi-region.

## Uso

```yaml
jobs:
  deploy:
    uses: TetherEducation/tether-ci-cd/.github/workflows/deploy-explorer-docker-django.yml@staging
    with:
      environment: staging
      bootstrap_project_id: my-bootstrap-project
      bootstrap_project_number: "123456789"
      bootstrap_workload_identity_provider: projects/123/locations/global/workloadIdentityPools/github/providers/github
      bootstrap_service_account: github-actions@my-project.iam.gserviceaccount.com
      deployment_config_secret_name: deployment-config-staging
      service_name: core
      migration_job_key: core-run-migrations
    secrets: inherit
```

## Inputs

| Input                                  | Tipo    | Requerido | Default | Descripcion                |
| -------------------------------------- | ------- | --------- | ------- | -------------------------- |
| `environment`                          | string  | Si        | -       | Ambiente                   |
| `bootstrap_project_id`                 | string  | Si        | -       | Project ID bootstrap       |
| `bootstrap_project_number`             | string  | Si        | -       | Project number             |
| `bootstrap_workload_identity_provider` | string  | Si        | -       | WIF provider path          |
| `bootstrap_service_account`            | string  | Si        | -       | SA email                   |
| `deployment_config_secret_name`        | string  | Si        | -       | Secret con config          |
| `service_name`                         | string  | Si        | -       | Key del servicio           |
| `migration_job_key`                    | string  | Si        | -       | Key del job de migraciones |
| `force_migrations`                     | boolean | No        | false   | Forzar migraciones         |
| `docker_build_args`                    | string  | No        | -       | Build args custom          |

## Configuracion en Secret Manager

Similar a `deploy-explorer-docker`, pero incluye `jobs`:

```json
{
  "infrastructure": {
    "us": {
      "region": "us-central1",
      "services": {
        "core": {
          "service_name": "core-us",
          "image_name": "core"
        }
      },
      "jobs": {
        "core-run-migrations": {
          "job_name": "core-migrations-us"
        }
      }
    }
  }
}
```

## Jobs

1. **prepare**: Lee config, genera matrices
2. **build**: Build Docker con cache pip
3. **check-migrations**: Detecta archivos de migracion cambiados
4. **migrate**: Ejecuta Cloud Run Job (si hay migraciones)
5. **deploy**: Deploy a Cloud Run

## Deteccion de migraciones

Busca cambios en archivos `migrations/*.py` (excluye `__init__.py`).

Si `force_migrations: true`, ejecuta siempre.

## Cache Python

Usa `buildkit-cache-dance` para cachear pip entre builds.

## Caracteristicas

- Migraciones automaticas o manuales
- Deteccion inteligente de cambios
- Multi-region
- Cache pip optimizado
