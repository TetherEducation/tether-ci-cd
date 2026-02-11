# spa-sync-gcs

Sync a built SPA to Google Cloud Storage with correct cache behavior for the HTML shell, and optional CDN cache invalidation.

## Usage

Use as a step **after** checkout, build, and GCP authentication (the job must already have `gcloud`/`gsutil` available and be authenticated).

```yaml
- name: Sync SPA to GCS
  uses: TetherEducation/tether-ci-cd/.github/actions/spa-sync-gcs@staging
  with:
    bucket: my-bucket
    path_prefix: dnk          # optional, e.g. tenant ID or empty for root
    source_dir: dist/         # optional, default dist/
    index_file: index.html   # optional, default index.html
    # Optional CDN invalidation (set both url_map and project):
    url_map: my-url-map
    project: my-gcp-project
    path: /*                  # optional, default /*
    host: ''                  # optional
    # Optional fallback page with same Cache-Control:
    fallback_path: 404.html
```

## What it does

1. **Syncs** `source_dir` to `gs://bucket/path_prefix/` via `gsutil rsync -r -d`, **excluding** the index file (and optional fallback) so they never get default cache headers.
2. **Uploads** the index file (and optionally `fallback_path`) once with `Cache-Control: no-cache, no-store, must-revalidate`.
3. **Optionally** runs CDN cache invalidation when `url_map` and `project` are set.

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `bucket` | Yes | - | GCS bucket name |
| `path_prefix` | No | `''` | Object prefix (e.g. tenant ID or empty for root) |
| `source_dir` | No | `dist/` | Local build directory to sync |
| `index_file` | No | `index.html` | Index file name to re-upload with no-cache |
| `index_cache_control` | No | `no-cache, no-store, must-revalidate` | Cache-Control for index and fallback |
| `fallback_path` | No | `''` | Optional file to re-upload with same Cache-Control (e.g. `404.html`) |
| `url_map` | No | `''` | URL map name for CDN invalidation |
| `project` | No | `''` | GCP project for CDN invalidation |
| `path` | No | `/*` | Path(s) to invalidate |
| `host` | No | `''` | Optional host for CDN invalidation |

## Requirements

- The job must run **after** `google-github-actions/auth` and `google-github-actions/setup-gcloud` (or equivalent) so `gsutil` and `gcloud` are available and authenticated.
- **GCS:** Identity needs `storage.objects.create` and `storage.objects.delete` on the bucket.
- **CDN:** If using invalidation, identity needs e.g. `compute.urlMaps.invalidateCache` (or `compute.networkAdmin` / `compute.loadBalancerAdmin`).

Full doc: [docs/deploys/spa-sync-gcs.md](../../../docs/deploys/spa-sync-gcs.md)
