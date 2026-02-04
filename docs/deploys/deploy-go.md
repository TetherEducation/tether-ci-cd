# deploy-go

Deploy de servicios Go a Amazon EKS con notificaciones Slack.

## Uso

```yaml
jobs:
  deploy:
    uses: TetherEducation/tether-ci-cd/.github/workflows/deploy-go.yml@staging
    with:
      ENVIRONMENT: staging
      ECR_REPOSITORY: my-go-app
      KUBE_NAMESPACE: my-namespace
      DEPLOYMENT_NAME: my-deployment
      CONTAINER_APP_NAME: my-container
      RELEASE_REVISION: ${{ github.sha }}
    secrets: inherit
```

## Inputs

| Input                | Tipo   | Requerido | Default        | Descripcion       |
| -------------------- | ------ | --------- | -------------- | ----------------- |
| `ENVIRONMENT`        | string | Si        | -              | Ambiente          |
| `ECR_REPOSITORY`     | string | Si        | -              | Repositorio ECR   |
| `KUBE_NAMESPACE`     | string | Si        | -              | Namespace K8s     |
| `DEPLOYMENT_NAME`    | string | Si        | -              | Nombre deployment |
| `CONTAINER_APP_NAME` | string | Si        | -              | Nombre container  |
| `RELEASE_REVISION`   | string | No        | -              | Tag imagen        |
| `DOCKERFILE_PATH`    | string | No        | `./Dockerfile` | Ruta Dockerfile   |

## Secrets

| Secret                  | Requerido | Descripcion            |
| ----------------------- | --------- | ---------------------- |
| `KUBE_CONFIG_DATA`      | Si        | kubeconfig en base64   |
| `SLACK_WEBHOOK`         | Si        | Webhook notificaciones |
| `AWS_ACCESS_KEY_ID`     | Si        | AWS access key         |
| `AWS_SECRET_ACCESS_KEY` | Si        | AWS secret key         |
| `AWS_REGION`            | Si        | Region AWS             |

## Caracteristicas

- ECR Registry: `363711807478.dkr.ecr.us-east-1.amazonaws.com`
- Usa `TetherEducation/kubectl-aws-eks@staging`
- Notificaciones Slack integradas
- Verificacion rollout

## Flujo

1. Configura AWS credentials
2. Login a ECR
3. kubectl set image
4. Espera rollout complete
