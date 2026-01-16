# syntax=docker/dockerfile:1
#
# Headlamp "overlay" image: start from an upstream Headlamp image and apply
# branding/customizations without patching Headlamp sources.
#
# Build example:
#   docker build -f overlays/kui/Dockerfile \
#     --build-arg HEADLAMP_IMAGE=ghcr.io/headlamp-k8s/headlamp:v0.39.0 \
#     --build-arg BRAND_NAME=KUI \
#     --build-arg BRAND_DESCRIPTION="Kubernetes UI" \
#     -t headlamp-kui:v0.39.0 .

ARG HEADLAMP_IMAGE=ghcr.io/headlamp-k8s/headlamp:v0.39.0
FROM ${HEADLAMP_IMAGE} AS base

ARG BRAND_NAME=KUI
ARG BRAND_DESCRIPTION=Kubernetes\ UI

# The upstream image runs as the "headlamp" user. Switch to root for file edits.
USER root

# 1) Optional: overlay static frontend files (favicons, manifest, svg logos, etc).
# Put your custom files under overlays/kui/frontend/ with the same names as in
# /headlamp/frontend (e.g. favicon.ico, manifest.json, logo-light.svg, ...).
COPY overlays/kui/frontend/ /headlamp/frontend/
RUN rm -f /headlamp/frontend/.gitkeep || true

# 2) Patch index.html title and meta description (best-effort).
RUN if [ -f /headlamp/frontend/index.html ]; then \
      sed -i "s|<title>[^<]*</title>|<title>${BRAND_NAME}</title>|g" /headlamp/frontend/index.html || true; \
      sed -i "s|<meta name=\"description\" content=\"[^\"]*\"|<meta name=\"description\" content=\"${BRAND_DESCRIPTION}\"|g" /headlamp/frontend/index.html || true; \
    fi

# 3) Patch manifest.json name/short_name (best-effort).
RUN if [ -f /headlamp/frontend/manifest.json ]; then \
      sed -i "s|\"name\"[[:space:]]*:[[:space:]]*\"[^\"]*\"|\"name\": \"${BRAND_NAME}\"|g" /headlamp/frontend/manifest.json || true; \
      sed -i "s|\"short_name\"[[:space:]]*:[[:space:]]*\"[^\"]*\"|\"short_name\": \"${BRAND_NAME}\"|g" /headlamp/frontend/manifest.json || true; \
    fi

# 4) Patch bundled JS to replace the default product name string (best-effort).
# This is a heuristic: it replaces the exact string "Headlamp" when it appears
# as a quoted value in built assets.
RUN if [ -d /headlamp/frontend/assets ]; then \
      find /headlamp/frontend/assets -type f -name "*.js" -print0 | xargs -0 -r sed -i "s|\"Headlamp\"|\"${BRAND_NAME}\"|g" || true; \
    fi

# 5) Optional: overlay additional (non-static) plugins.
# Put built plugins under overlays/kui/plugins/<pluginName>/{main.js,package.json}
# so they end up in /headlamp/plugins/<pluginName>/...
COPY overlays/kui/plugins/ /headlamp/plugins/
RUN rm -f /headlamp/plugins/.gitkeep || true

RUN chown -R headlamp:headlamp /headlamp
USER headlamp

