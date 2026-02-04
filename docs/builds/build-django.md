# build-django

Build de aplicaciones Python/Django y push a Amazon ECR.

## Uso

```yaml
jobs:
  build:
    uses: TetherEducation/tether-ci-cd/.github/workflows/build-django.yml@staging
    with:
      ENVIRONMENT: staging
      ECR_REPOSITORY: my-django-app
      RELEASE_REVISION: ${{ github.sha }}
    secrets: inherit
```

## Inputs

| Input | Tipo | Requerido | Descripcion |
|-------|------|-----------|-------------|
| `ENVIRONMENT` | string | Si | Ambiente (staging, production) |
| `ECR_REPOSITORY` | string | Si | Nombre del repositorio en ECR |
| `RELEASE_REVISION` | string | Si | Tag de la imagen (generalmente `${{ github.sha }}`) |

## Secrets

| Secret | Requerido | Descripcion |
|--------|-----------|-------------|
| `AWS_ACCESS_KEY_ID` | No* | AWS access key |
| `AWS_SECRET_ACCESS_KEY` | No* | AWS secret key |
| `AWS_REGION` | No* | Region AWS |
| `GH_APP_CREDENTIALS_TOKEN` | No* | Token para repos privados |
| `SLACK_WEBHOOK` | No | Webhook para notificaciones |
| `BOT_SLACK_TOKEN` | No | Token bot Slack |
| `BOT_SLACK_CHANNEL` | No | Canal Slack |

> *Configurados a nivel org

## Caracteristicas

- Setup Python 3.9
- Docker Buildx con cache
- Notificaciones Slack (inicio, exito, error)
- Tags: `{sha}` y `latest`

## Build Args disponibles

El Dockerfile recibe:

- `INPUT_ENVIRONMENT`
- `INPUT_GITHUB_TOKEN`
- `INPUT_AWS_ACCESS`
- `INPUT_AWS_ACCESS_KEY`
- `INPUT_AWS_REGION`
- `INPUT_LOGDNA_INGESTION_KEY`
