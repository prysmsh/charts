# Prysm Helm Charts

This directory hosts Helm charts for deploying Prysm components. Charts packaged from this tree can be published to a GitHub Pages backed repository (for example `https://charts.prysm.sh`).

## Repository Layout

- `charts/agent` – Deploys the Prysm agent as a privileged DaemonSet with the RBAC, host mounts, and environment expected by the Docker Compose reference deployment.
- `.helmignore` – Files to exclude when packaging charts.

## Packaging & Publishing

1. Bump `version` and `appVersion` in the chart you are releasing.
2. Package the chart:
   ```bash
   helm package charts/agent --destination dist
   ```
3. Regenerate the repository index next to the packaged archives:
   ```bash
   helm repo index dist --url https://charts.prysm.sh
   ```
4. Commit and push the updated archives and `index.yaml` to the GitHub repository or Pages branch that serves the chart repo.

## Linting

Run `helm lint charts/agent` before packaging to validate templates and required values.

> The bundled `values.yaml` ships with illustrative defaults so the chart installs cleanly for demos. Replace the sample cluster ID, token, and region with tenant-specific values before promoting to production.
