# deploy-explorer-docker

Deploy multi-region a GCP Cloud Run con configuracion dinamica desde Secret Manager.

## Uso

```yaml
jobs:
  deploy:
    uses: TetherEducation/tether-ci-cd/.github/workflows/deploy-explorer-docker.yml@staging
    with:
      environment: staging
      bootstrap_project_id: my-bootstrap-project
      bootstrap_project_number: "123456789"
      bootstrap_workload_identity_provider: projects/123/locations/global/workloadIdentityPools/github/providers/github
      bootstrap_service_account: github-actions@my-project.iam.gserviceaccount.com
      deployment_config_secret_name: deployment-config-staging
      service_name: my-service
    secrets: inherit
```

## Inputs

| Input                                  | Tipo   | Requerido | Descripcion                           |
| -------------------------------------- | ------ | --------- | ------------------------------------- |
| `environment`                          | string | Si        | Ambiente (staging, production)        |
| `bootstrap_project_id`                 | string | Si        | Project ID para autenticacion inicial |
| `bootstrap_project_number`             | string | Si        | Project number para WIF               |
| `bootstrap_workload_identity_provider` | string | Si        | Path del WIF provider                 |
| `bootstrap_service_account`            | string | Si        | Service account email                 |
| `deployment_config_secret_name`        | string | Si        | Nombre del secret con config          |
| `service_name`                         | string | Si        | Key del servicio en config            |
| `docker_build_args`                    | string | No        | Build args custom                     |

## Configuracion en Secret Manager

El secret debe contener JSON con estructura:

```json
{
  "project_id": "my-project",
  "project_number": "123456789",
  "artifact_registry_host": "us-central1-docker.pkg.dev",
  "artifact_registry_repository": "my-repo",
  "workload_identity_provider": "...",
  "github_actions_service_account": "...",
  "regions": ["us", "eu"],
  "infrastructure": {
    "us": {
      "region": "us-central1",
      "services": {
        "my-service": {
          "service_name": "my-service-us",
          "image_name": "my-service"
        }
      }
    },
    "eu": {
      "region": "europe-west1",
      "services": {
        "my-service": {
          "service_name": "my-service-eu",
          "image_name": "my-service"
        }
      }
    }
  }
}
```

## Jobs

1. **prepare**: Lee config de Secret Manager, genera matriz
2. **build**: Build + push a Artifact Registry (por imagen unica)
3. **deploy**: Deploy a Cloud Run (por region)

## Caracteristicas

- Multi-region automatico
- Build unico, deploy multiple
- Config centralizada en Secret Manager
- Workload Identity Federation (sin keys)
- Cache Docker (GHA + registry)
