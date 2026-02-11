# SPA Sync to GCS

Reusable GitHub Action that deploys a single-page app (SPA) build to Google Cloud Storage and sets correct cache behavior for the HTML shell.

## What it does

- **Syncs** the build directory (e.g. `dist/`) to `gs://<bucket>/<path>/` using `gsutil rsync -r -d` (adds/updates/deletes to match source). The index file (and optional fallback) are **excluded from the sync** so they are never written with default cache headers.
- **Uploads** the index file (and optionally a fallback/404 page) once with `Cache-Control: no-cache, no-store, must-revalidate` so the HTML is not cached and the app always loads the latest JS/CSS.
- **Optionally** runs `gcloud compute url-maps invalidate-cdn-cache` for the given URL map and path so CDN edge caches are cleared after deploy.

## When to use it

Use this action in workflows that deploy a frontend to GCS behind a load balancer (e.g. explorer-web, other Tether SPAs). The job must already be authenticated (e.g. via `google-github-actions/auth` with a service account that has `storage.objects.create` on the target bucket).

## Uso

Add the action as a step after checkout, build, and GCP auth:

```yaml
jobs:
  deploy-spa:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build SPA
        run: npm ci && npm run build

      - name: Authenticate to Google Cloud
        uses: google-github-actions/auth@v2
        with:
          workload_identity_provider: ${{ secrets.WIF_PROVIDER }}
          service_account: ${{ secrets.WIF_SA }}

      - name: Set up Cloud SDK
        uses: google-github-actions/setup-gcloud@v2

      - name: Sync SPA to GCS
        uses: TetherEducation/tether-ci-cd/.github/actions/spa-sync-gcs@staging
        with:
          bucket: my-bucket
          path_prefix: dnk
          source_dir: dist/
          url_map: my-url-map
          project: my-gcp-project
          path: /*
```

## Inputs

| Input                 | Tipo   | Requerido | Default                                  | Descripcion                                                                 |
| --------------------- | ------ | --------- | ---------------------------------------- | --------------------------------------------------------------------------- |
| `bucket`              | string | Si        | -                                        | GCS bucket name                                                             |
| `path_prefix`         | string | No        | `''`                                     | Object prefix (e.g. tenant ID `dnk` or empty for root)                       |
| `source_dir`          | string | No        | `dist/`                                  | Local build directory to sync                                               |
| `index_file`          | string | No        | `index.html`                             | Index file name to re-upload with no-cache (e.g. `index.html`, `app.html`)  |
| `index_cache_control` | string | No        | `no-cache, no-store, must-revalidate`    | Cache-Control for index and fallback                                        |
| `fallback_path`       | string | No        | `''`                                     | Optional file to re-upload with same Cache-Control (e.g. `404.html`)        |
| `url_map`             | string | No        | `''`                                     | URL map name for CDN cache invalidation                                    |
| `project`             | string | No        | `''`                                     | GCP project for CDN invalidation                                            |
| `path`                | string | No        | `/*`                                     | Path(s) to invalidate (e.g. `/*` or `/`)                                   |
| `host`                | string | No        | `''`                                     | Optional host for CDN invalidation                                          |

If `url_map` and `project` are set, the action runs CDN cache invalidation after the sync.

## Permissions / IAM

- **GCS:** The service account (or WIF identity) must have `storage.objects.create` and `storage.objects.delete` on the target bucket (for rsync -d).
- **CDN invalidation:** For optional cache invalidation, the identity needs e.g. `compute.urlMaps.invalidateCache` (or roles such as `compute.networkAdmin` / `compute.loadBalancerAdmin`).
