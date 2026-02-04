# check-version

Manejo de versiones y creacion automatica de tags git.

## Uso

```yaml
jobs:
  version:
    uses: TetherEducation/tether-ci-cd/.github/workflows/check-version.yml@staging
    with:
      TAG_POSTFIX: "-api"

  build:
    needs: version
    if: needs.version.outputs.SKIP_DEPLOY_OUTPUT == 'false'
    # ... solo ejecuta si la version es nueva
```

## Inputs

| Input         | Tipo   | Requerido | Default | Descripcion                                |
| ------------- | ------ | --------- | ------- | ------------------------------------------ |
| `TAG_POSTFIX` | string | No        | -       | Sufijo para el tag (ej: `-api`, `-worker`) |

## Outputs

| Output               | Descripcion                                     |
| -------------------- | ----------------------------------------------- |
| `SKIP_DEPLOY_OUTPUT` | `true` si el tag ya existe, `false` si es nuevo |

## Requisitos

Tu repo debe tener un archivo `version.txt` en la raiz con la version:

```plaintext
1.2.3
```

## Formato de tags

El tag se genera asi:

| Rama    | Formato                      |
| ------- | ---------------------------- |
| staging | `{version}{postfix}-staging` |
| master  | `{version}{postfix}-master`  |

Ejemplo con `version.txt = 1.2.3` y `TAG_POSTFIX = -api`:

- staging: `1.2.3-api-staging`
- master: `1.2.3-api-master`

## Flujo

1. Lee `version.txt`
2. Genera tag segun rama
3. Verifica si tag existe en remote
4. Si no existe:
   - Crea tag local
   - Push tag a origin
   - Output: `SKIP_DEPLOY=false`
5. Si existe:
   - Output: `SKIP_DEPLOY=true`

## Patron: Deploy solo si version nueva

```yaml
jobs:
  version:
    uses: .../check-version.yml@staging

  build:
    needs: version
    if: needs.version.outputs.SKIP_DEPLOY_OUTPUT == 'false'
    uses: .../build-go.yml@staging
    # ...

  deploy:
    needs: [version, build]
    if: needs.version.outputs.SKIP_DEPLOY_OUTPUT == 'false'
    uses: .../deploy-eks.yml@staging
    # ...
```

## Para actualizar version

1. Edita `version.txt`
2. Commit y push
3. El workflow crea el tag automaticamente
