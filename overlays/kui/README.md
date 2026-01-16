## Headlamp overlay image (KUI example)

This overlay image lets you **brand** Headlamp without patching Headlamp source
code. It starts from an upstream `ghcr.io/headlamp-k8s/headlamp:<version>` image
and applies best-effort overrides.

### What it can change

- **Browser tab title** (`index.html`)
- **Meta description** (`index.html`)
- **PWA manifest name/short_name** (`manifest.json`)
- **Favicons / icons / SVGs** (by copying files into `/headlamp/frontend`)
- **Product name string in JS bundles** (best-effort replacement of `"Headlamp"`)
- **Extra plugins** (by copying into `/headlamp/plugins`)

### Build

From repo root:

```bash
docker build -f overlays/kui/Dockerfile \
  --build-arg HEADLAMP_IMAGE=ghcr.io/headlamp-k8s/headlamp:v0.39.0 \
  --build-arg BRAND_NAME=KUI \
  --build-arg BRAND_DESCRIPTION="Kubernetes UI" \
  -t headlamp-kui:v0.39.0 .
```

### Providing your custom icons/files

Put your files under:

- `overlays/kui/frontend/` (copied to `/headlamp/frontend/`)
- `overlays/kui/plugins/` (copied to `/headlamp/plugins/`)

Use the **same filenames** as in the upstream image (e.g. `favicon.ico`,
`manifest.json`, `apple-touch-icon.png`, ...).

