# build-go

Build de aplicaciones Go y push a Amazon ECR. Soporta dependencias privadas via GOPRIVATE.

## Uso

```yaml
jobs:
  build:
    uses: TetherEducation/tether-ci-cd/.github/workflows/build-go.yml@staging
    with:
      ENVIRONMENT: staging
      ECR_REPOSITORY: my-go-app
      RELEASE_REVISION: ${{ github.sha }}
    secrets: inherit
```

## Inputs

| Input              | Tipo   | Requerido | Default        | Descripcion                    |
| ------------------ | ------ | --------- | -------------- | ------------------------------ |
| `ENVIRONMENT`      | string | Si        | -              | Ambiente (staging, production) |
| `ECR_REPOSITORY`   | string | Si        | -              | Nombre del repositorio en ECR  |
| `RELEASE_REVISION` | string | Si        | -              | Tag de la imagen               |
| `DOCKERFILE_PATH`  | string | No        | `./Dockerfile` | Ruta al Dockerfile             |

## Secrets

| Secret                     | Requerido | Descripcion          |
| -------------------------- | --------- | -------------------- |
| `AWS_ACCESS_KEY_ID`        | Si\*      | AWS access key       |
| `AWS_SECRET_ACCESS_KEY`    | Si\*      | AWS secret key       |
| `AWS_REGION`               | Si\*      | Region AWS           |
| `GH_APP_CREDENTIALS_TOKEN` | Si\*      | Token para GOPRIVATE |

> \*Configurados a nivel org

## Caracteristicas

- GOPRIVATE configurado para `github.com/TetherEducation/*`
- Docker Buildx con cache (GHA)
- Tags: `{revision}` y `latest`

## Build Args disponibles

El Dockerfile recibe:

- `INPUT_ENVIRONMENT`
- `INPUT_GITHUB_TOKEN`
- `INPUT_AWS_ACCESS`
- `INPUT_AWS_ACCESS_KEY`
- `INPUT_AWS_REGION`
- `INPUT_LOGDNA_INGESTION_KEY`

## Ejemplo con Dockerfile custom

```yaml
jobs:
  build:
    uses: TetherEducation/tether-ci-cd/.github/workflows/build-go.yml@staging
    with:
      ENVIRONMENT: staging
      ECR_REPOSITORY: my-service
      RELEASE_REVISION: ${{ github.sha }}
      DOCKERFILE_PATH: ./docker/Dockerfile.prod
    secrets: inherit
```
