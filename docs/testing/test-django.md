# test-django

Ejecuta tests de Django con PostgreSQL/PostGIS.

## Uso

```yaml
jobs:
  test:
    uses: TetherEducation/tether-ci-cd/.github/workflows/test-django.yml@staging
    secrets: inherit
```

## Inputs

Sin inputs requeridos.

## Secrets

| Secret                | Requerido | Descripcion               |
| --------------------- | --------- | ------------------------- |
| `TESTING_SECRET_CONF` | Si        | Contenido de secrets.json |
| `SLACK_WEBHOOK`       | No        | Webhook notificaciones    |
| `BOT_SLACK_TOKEN`     | No        | Token bot Slack           |
| `BOT_SLACK_CHANNEL`   | No        | Canal Slack               |

## Servicios

El workflow levanta PostgreSQL con PostGIS:

```yaml
services:
  db_service:
    image: postgis/postgis
    env:
      POSTGRES_USER: postgres
      POSTGRES_DB: postgres
      POSTGRES_PASSWORD: postgres
```

## Variables de entorno para tests

```plaintext
TEST_DB_HOST: localhost
TEST_DB_NAME: postgres
TEST_DB_PASS: postgres
TEST_DB_PORT: 5432
TEST_DB_USER: postgres
```

## Flujo

1. Setup Python 3.9
2. Crea virtualenv
3. Instala dependencias sistema (memcached, gdal-bin)
4. Instala dependencias Python (pipenv)
5. Copia secrets.json
6. Ejecuta `python manage.py test -v 2 --parallel`
7. Notifica resultado a Slack

## Dependencias sistema instaladas

- memcached
- libmemcached-tools
- libmemcached-dev
- zlib1g-dev
- gdal-bin

## Cache

Usa cache de pipenv:

```yaml
key: ${{ runner.os }}-pipenv-${{ hashFiles('**/Pipfile.lock') }}
```

## Notificaciones

- Inicio de tests
- Exito con GIF
- Error con mensaje
