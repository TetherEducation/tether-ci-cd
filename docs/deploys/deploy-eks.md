# deploy-eks

Deploy a Amazon EKS (version limpia, sin notificaciones).

## Uso

```yaml
jobs:
  deploy:
    uses: TetherEducation/tether-ci-cd/.github/workflows/deploy-eks.yml@staging
    with:
      ENVIRONMENT: staging
      ECR_REPOSITORY: my-app
      KUBE_NAMESPACE: my-namespace
      DEPLOYMENT_NAME: my-deployment
      CONTAINER_APP_NAME: my-container
      RELEASE_REVISION: ${{ github.sha }}
    secrets: inherit
```

## Inputs

| Input | Tipo | Requerido | Descripcion |
|-------|------|-----------|-------------|
| `ENVIRONMENT` | string | Si | Ambiente (staging, production) |
| `ECR_REPOSITORY` | string | Si | Nombre del repositorio en ECR |
| `KUBE_NAMESPACE` | string | Si | Namespace de Kubernetes |
| `DEPLOYMENT_NAME` | string | Si | Nombre del deployment |
| `CONTAINER_APP_NAME` | string | Si | Nombre del container en el pod |
| `RELEASE_REVISION` | string | Si | Tag de la imagen a deployar |

## Secrets

| Secret | Requerido | Descripcion |
|--------|-----------|-------------|
| `KUBE_CONFIG_DATA` | Si | kubeconfig en base64 |
| `AWS_ACCESS_KEY_ID` | Si | AWS access key |
| `AWS_SECRET_ACCESS_KEY` | Si | AWS secret key |
| `AWS_REGION` | Si | Region AWS |

## Caracteristicas

- Sin notificaciones Slack (usar `notify-slack.yml` separado)
- ECR Registry: `363711807478.dkr.ecr.us-east-1.amazonaws.com`
- Usa `TetherEducation/kubectl-aws-eks@master`

## Diferencias con deploy-django/deploy-go

| Workflow | Notificaciones | GIFs | Uso recomendado |
|----------|----------------|------|-----------------|
| `deploy-eks` | No | No | Pipelines limpios |
| `deploy-django` | Si | Si | Django legacy |
| `deploy-go` | Si | Si | Go legacy |

## Ejemplo con notificaciones separadas

```yaml
jobs:
  deploy:
    uses: .../deploy-eks.yml@staging
    with:
      # ...
    secrets: inherit

  notify-success:
    needs: deploy
    if: success()
    uses: .../notify-slack.yml@staging
    with:
      status: success
    secrets: inherit

  notify-failure:
    needs: deploy
    if: failure()
    uses: .../notify-slack.yml@staging
    with:
      status: failure
    secrets: inherit
```
