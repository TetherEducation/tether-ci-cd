# Getting Started

Guia para integrar los workflows reutilizables de tether-ci-cd en tu repositorio.

## Requisitos

1. Repositorio en la organizacion `TetherEducation`
2. Secrets configurados (la mayoria ya estan a nivel org)

## Paso 1: Crear workflow en tu repo

Crea `.github/workflows/deploy.yml`:

```yaml
name: Deploy

on:
  push:
    branches: [staging, master]

jobs:
  build:
    uses: TetherEducation/tether-ci-cd/.github/workflows/build-go.yml@staging
    with:
      ENVIRONMENT: ${{ github.ref_name == 'master' && 'production' || 'staging' }}
      ECR_REPOSITORY: my-service
      RELEASE_REVISION: ${{ github.sha }}
    secrets: inherit # Hereda secrets de la org

  deploy:
    needs: build
    uses: TetherEducation/tether-ci-cd/.github/workflows/deploy-eks.yml@staging
    with:
      ENVIRONMENT: ${{ github.ref_name == 'master' && 'production' || 'staging' }}
      ECR_REPOSITORY: my-service
      KUBE_NAMESPACE: my-namespace
      DEPLOYMENT_NAME: my-deployment
      CONTAINER_APP_NAME: my-container
      RELEASE_REVISION: ${{ github.sha }}
    secrets: inherit

  notify-success:
    needs: deploy
    if: success()
    uses: TetherEducation/tether-ci-cd/.github/workflows/notify-slack.yml@staging
    with:
      status: success
    secrets: inherit

  notify-failure:
    needs: deploy
    if: failure()
    uses: TetherEducation/tether-ci-cd/.github/workflows/notify-slack.yml@staging
    with:
      status: failure
    secrets: inherit
```

## Paso 2: Secrets necesarios

### Ya configurados a nivel org

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_REGION`
- `SLACK_WEBHOOK_URL`
- `SLACK_BOT_TOKEN`
- `GH_APP_CREDENTIALS_TOKEN`

### Pueden necesitar configuracion por repo

- `KUBE_CONFIG_DATA` - kubeconfig base64 para EKS
- `TESTING_SECRET_CONF` - secrets.json para tests Django

## Paso 3: Elegir rama de referencia

```yaml
# Para desarrollo/pruebas
uses: TetherEducation/tether-ci-cd/.github/workflows/workflow.yml@staging

# Para produccion (mas estable)
uses: TetherEducation/tether-ci-cd/.github/workflows/workflow.yml@master
```

## Patrones comunes

### Build + Deploy + Notify

```yaml
jobs:
  build:
    uses: .../build-go.yml@staging
    # ...

  deploy:
    needs: build
    uses: .../deploy-eks.yml@staging
    # ...

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

### Versionado automatico

```yaml
jobs:
  version:
    uses: .../check-version.yml@staging
    with:
      TAG_POSTFIX: "-api"

  build:
    needs: version
    if: needs.version.outputs.SKIP_DEPLOY_OUTPUT == 'false'
    # ... solo hace build si la version cambio
```

### Migraciones Liquibase (preview + apply)

Ver [liquibase-preview.md](database/liquibase-preview.md) y [liquibase-apply.md](database/liquibase-apply.md).

## Troubleshooting

### Error: "reusable workflow must be public"

Los workflows de tether-ci-cd deben ser publicos o el repo debe estar en la misma org.

### Error: secrets no encontrados

Verifica que el repo hereda secrets de la org:

```yaml
secrets: inherit
```

O pasa secrets explicitamente:

```yaml
secrets:
  AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
```

### Notificacion Slack no menciona correctamente

Agrega tu usuario al mapeo en [config/slack-users.json](../config/slack-users.json).
