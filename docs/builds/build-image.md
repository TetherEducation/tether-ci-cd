# build-image

Build generico de imagen Docker y push a Amazon ECR.

## Uso

```yaml
jobs:
  build:
    uses: TetherEducation/tether-ci-cd/.github/workflows/build-image.yml@staging
    with:
      ECR_REPOSITORY: my-app
      RELEASE_REVISION: ${{ github.sha }}
    secrets:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      AWS_REGION: ${{ secrets.AWS_REGION }}
```

## Inputs

| Input              | Tipo   | Requerido | Descripcion                   |
| ------------------ | ------ | --------- | ----------------------------- |
| `ECR_REPOSITORY`   | string | Si        | Nombre del repositorio en ECR |
| `RELEASE_REVISION` | string | Si        | Tag de la imagen              |

## Secrets

| Secret                  | Requerido | Descripcion    |
| ----------------------- | --------- | -------------- |
| `AWS_ACCESS_KEY_ID`     | Si        | AWS access key |
| `AWS_SECRET_ACCESS_KEY` | Si        | AWS secret key |
| `AWS_REGION`            | Si        | Region AWS     |

## Caracteristicas

- Workflow minimalista sin notificaciones
- Docker Buildx con cache
- Tags: `{revision}` y `latest`
- ECR Registry: `363711807478.dkr.ecr.us-east-1.amazonaws.com`

## Diferencias con otros builds

| Workflow       | Notificaciones | Build args | Uso                      |
| -------------- | -------------- | ---------- | ------------------------ |
| `build-image`  | No             | Basicos    | Simple, sin dependencias |
| `build-django` | Si             | Completos  | Django con secrets       |
| `build-go`     | No             | Completos  | Go con GOPRIVATE         |
