# notify-slack

Notificaciones Slack para deploys con formato card y @menciones reales.

## Uso

```yaml
jobs:
  deploy:
    # ... tu job de deploy

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

## Inputs

| Input | Tipo | Requerido | Descripcion |
|-------|------|-----------|-------------|
| `status` | string | Si | `success` o `failure` |
| `environment` | string | No | Forzar ambiente (auto-detecta de rama) |
| `custom_message` | string | No | Mensaje adicional |
| `actor_email` | string | No | Email para @mencion via API |

## Secrets

| Secret | Requerido | Descripcion |
|--------|-----------|-------------|
| `SLACK_WEBHOOK_URL` | Si | Incoming Webhook URL |
| `SLACK_BOT_TOKEN` | No | Token bot (scope: `users:read.email`) |

## Auto-deteccion de ambiente

| Rama | Ambiente |
|------|----------|
| main, master | PROD |
| staging | STAGING |
| dev, development | DEV |
| otra | nombre de la rama |

## @Menciones

Prioridad de resolucion:

1. **Mapeo directo GitHub → Slack ID**
   - Archivo: [`config/slack-users.json`](../../config/slack-users.json)
   - Formato: `<@SLACK_ID>`

2. **Busqueda por email** (si `SLACK_BOT_TOKEN` disponible)
   - Usa API `users.lookupByEmail`

3. **Fallback**
   - Texto plano: `@github_username`

## Agregar usuario al mapeo

Edita `config/slack-users.json`:

```json
{
  "github_username": "SLACK_USER_ID"
}
```

> **Tip:** Click derecho en perfil Slack → "Copy member ID"

## Formato de notificacion

```
┌─────────────────────────────────────┐
│ ✅ Deploy Exitoso / ❌ Deploy Fallido │
├─────────────────────────────────────┤
│ Repo: org/repo                      │
│ Rama: staging                       │
│ Env: STAGING                        │
│ Por: @usuario                       │
├─────────────────────────────────────┤
│ Commit: abc1234 | mensaje custom    │
├─────────────────────────────────────┤
│ [📋 Ver Action] [⋮]                 │
└─────────────────────────────────────┘
```

## Ejemplo completo

```yaml
name: Deploy

on:
  push:
    branches: [staging]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying..."

  notify-success:
    needs: deploy
    if: success()
    uses: TetherEducation/tether-ci-cd/.github/workflows/notify-slack.yml@staging
    with:
      status: success
      custom_message: "Deploy v1.2.3"
    secrets:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
      SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}

  notify-failure:
    needs: deploy
    if: failure()
    uses: TetherEducation/tether-ci-cd/.github/workflows/notify-slack.yml@staging
    with:
      status: failure
    secrets:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```
