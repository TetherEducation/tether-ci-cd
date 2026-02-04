# Notificaciones Slack para Deploys

Workflow reutilizable que notifica a Slack cuando un deploy termina (exitoso o fallido).

## Agregar a tu repositorio

Agrega estos jobs al final de tu workflow de deploy:

```yaml
  notify-success:
    needs: [build-and-deploy]  # Cambiar por el job principal de tu workflow
    if: success()
    uses: TetherEducation/tether-ci-cd/.github/workflows/notify-slack.yml@staging
    with:
      status: success
      actor_email: ${{ github.event.head_commit.author.email }}
    secrets:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
      SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}

  notify-failure:
    needs: [build-and-deploy]  # Cambiar por el job principal de tu workflow
    if: failure()
    uses: TetherEducation/tether-ci-cd/.github/workflows/notify-slack.yml@staging
    with:
      status: failure
      actor_email: ${{ github.event.head_commit.author.email }}
    secrets:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
      SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
```

## Secretos requeridos

Estos secretos deben estar configurados a nivel de organización o repositorio:

| Secreto | Requerido | Descripción |
|---------|-----------|-------------|
| `SLACK_WEBHOOK_URL` | Si | URL del Incoming Webhook de Slack |
| `SLACK_BOT_TOKEN` | No | Token del bot para @menciones reales (scope: `users:read.email`) |

## Inputs opcionales

| Input | Descripción |
|-------|-------------|
| `environment` | Forzar nombre de ambiente (si no se provee, se detecta de la rama) |
| `custom_message` | Mensaje adicional a incluir en la notificación |
| `actor_email` | Email del actor para buscar @mención en Slack |

## Auto-detección de ambiente

Si no se especifica `environment`, se detecta automáticamente:

| Rama | Ambiente |
|------|----------|
| `main`, `master` | PROD |
| `staging` | STAGING |
| `dev`, `development` | DEV |
| otra | nombre de la rama |

## @Menciones

El workflow intenta mencionar al usuario responsable en este orden:
1. Mapeo directo de username de GitHub a Slack ID (usuarios conocidos)
2. Búsqueda por email en Slack (requiere `SLACK_BOT_TOKEN`)
3. Fallback: `@github_username` como texto plano
