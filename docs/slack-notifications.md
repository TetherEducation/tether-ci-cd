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
      # environment: STAGING           # Opcional: forzar nombre de ambiente
      # custom_message: "Deploy v1.2.3"  # Opcional: mensaje adicional
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
      # environment: STAGING           # Opcional: forzar nombre de ambiente
      # custom_message: "Error en build"  # Opcional: mensaje adicional
    secrets:
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
      SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
```

## Secretos requeridos

| Secreto | Requerido | Descripcion |
|---------|-----------|-------------|
| `SLACK_WEBHOOK_URL` | Si | URL del Incoming Webhook de Slack |
| `SLACK_BOT_TOKEN` | No | Token del bot para @menciones reales (scope: `users:read.email`) |

> **Nota:** Estos secretos ya estan configurados a nivel de organizacion TetherEducation. No necesitas agregarlos manualmente a tu repositorio.

## Inputs

| Input | Requerido | Descripcion |
|-------|-----------|-------------|
| `status` | Si | `success` o `failure` |
| `actor_email` | No | Email del actor para buscar @mencion en Slack |
| `environment` | No | Forzar nombre de ambiente (si no se provee, se detecta de la rama) |
| `custom_message` | No | Mensaje adicional a incluir en la notificacion |

## Auto-deteccion de ambiente

Si no se especifica `environment`, se detecta automaticamente de la rama:

| Rama | Ambiente |
|------|----------|
| `main`, `master` | PROD |
| `staging` | STAGING |
| `dev`, `development` | DEV |
| otra | nombre de la rama |

## @Menciones

El workflow intenta mencionar al usuario responsable en este orden:

1. **Mapeo directo GitHub → Slack ID** (usuarios conocidos)
2. Busqueda por email en Slack (requiere `SLACK_BOT_TOKEN`)
3. Fallback: `@github_username` como texto plano

### Mapeo de usuarios

Los mapeos de GitHub username a Slack ID estan definidos en:

📁 `.github/workflows/notify-slack.yml` → paso "Set mention format" → array `SLACK_MAP`

Para agregar un nuevo usuario al mapeo, editar el array:

```bash
declare -A SLACK_MAP=(
  ["github_username"]="SLACK_USER_ID"
  ...
)
```

> **Tip:** Para obtener el Slack ID de un usuario, click derecho en su perfil de Slack → "Copy member ID"
