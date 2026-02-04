# deploy-django

Deploy de servicios Django a Amazon EKS con notificaciones Slack.

## Uso

```yaml
jobs:
  deploy:
    uses: TetherEducation/tether-ci-cd/.github/workflows/deploy-django.yml@staging
    with:
      ENVIRONMENT: staging
      ECR_REPOSITORY: my-django-app
      KUBE_NAMESPACE: my-namespace
      DEPLOYMENT_NAME: my-deployment
      CONTAINER_APP_NAME: my-container
      RELEASE_REVISION: ${{ github.sha }}
    secrets: inherit
```

## Inputs

| Input                | Tipo   | Requerido | Descripcion                    |
| -------------------- | ------ | --------- | ------------------------------ |
| `ENVIRONMENT`        | string | Si        | Ambiente (staging, production) |
| `ECR_REPOSITORY`     | string | Si        | Nombre del repositorio en ECR  |
| `KUBE_NAMESPACE`     | string | Si        | Namespace de Kubernetes        |
| `DEPLOYMENT_NAME`    | string | Si        | Nombre del deployment          |
| `CONTAINER_APP_NAME` | string | Si        | Nombre del container en el pod |
| `RELEASE_REVISION`   | string | Si        | Tag de la imagen a deployar    |

## Secrets

| Secret                  | Requerido | Descripcion                 |
| ----------------------- | --------- | --------------------------- |
| `KUBE_CONFIG_DATA`      | Si        | kubeconfig en base64        |
| `SLACK_WEBHOOK`         | Si        | Webhook para notificaciones |
| `AWS_ACCESS_KEY_ID`     | Si        | AWS access key              |
| `AWS_SECRET_ACCESS_KEY` | Si        | AWS secret key              |
| `AWS_REGION`            | Si        | Region AWS                  |
| `BOT_SLACK_TOKEN`       | No        | Token bot Slack (para GIFs) |
| `BOT_SLACK_CHANNEL`     | No        | Canal Slack                 |

## Caracteristicas

- Login a ECR automatico
- kubectl set image + rollout status
- Notificaciones Slack con GIFs (Dobby)
- Verificacion de rollout

## Flujo

1. Configura AWS credentials
2. Login a ECR
3. Notifica inicio a Slack
4. Actualiza imagen en deployment
5. Espera rollout complete
6. Notifica resultado (exito/error/cancel)
