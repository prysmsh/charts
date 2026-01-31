# Prysm Helm Charts

Official Helm charts for deploying Prysm components to Kubernetes clusters.

Charts packaged from this repository are automatically published to **`https://prysmsh.github.io/charts`** via GitHub Actions.

## Quick Start

```bash
# Add the Prysm Helm repository
helm repo add prysm https://prysmsh.github.io/charts

# Update your repositories
helm repo update

# Search for available charts
helm search repo prysm

# Install the Prysm agent
helm install prysm-agent prysm/agent \
  --namespace prysm-system \
  --create-namespace \
  --set configSecret.data.AGENT_TOKEN="your-token" \
  --set configSecret.data.CLUSTER_ID="your-cluster-id"
```

## Available Charts

### Agent Chart (`prysm/agent`)

Deploys the Prysm agent as a privileged DaemonSet with RBAC, host mounts, and environment configuration for Kubernetes cluster management.

**Features:**
- Zero-trust access to Kubernetes clusters
- Real-time log streaming and metrics collection
- Security scanning with Trivy
- eBPF-based network observability
- Mesh networking with WireGuard

**Documentation:** [charts/agent/README.md](charts/agent/README.md)

## Repository Layout

```
prysm-charts/
├── charts/
│   └── agent/              # Prysm agent chart
│       ├── Chart.yaml      # Chart metadata
│       ├── values.yaml     # Default configuration
│       ├── README.md       # Chart documentation
│       ├── CHANGELOG.md    # Version history
│       ├── templates/      # Kubernetes manifests
│       └── examples/       # Example configurations
├── .github/
│   └── workflows/
│       └── release-charts.yml  # Auto-publishing workflow
└── README.md
```

## Development

### Prerequisites

- Helm 3.x
- Kubernetes cluster for testing
- kubectl configured

### Linting

```bash
# Lint the agent chart
helm lint charts/agent

# Lint with specific values
helm lint charts/agent -f charts/agent/examples/values-production.yaml
```

### Testing Locally

```bash
# Render templates without installing
helm template my-release charts/agent

# Dry run installation
helm install my-release charts/agent --dry-run --debug

# Install to a test cluster
helm install test-agent charts/agent \
  --namespace test \
  --create-namespace \
  -f charts/agent/examples/values-development.yaml
```

### Releasing New Versions

Charts are automatically packaged and published when changes are pushed to the `main` branch.

1. Update `version` in `charts/agent/Chart.yaml`
2. Update `appVersion` if the application version changed
3. Document changes in `charts/agent/CHANGELOG.md`
4. Commit and push to `main`:
   ```bash
   git add charts/agent/
   git commit -m "Release agent chart v0.1.2"
   git push
   ```
5. GitHub Actions will automatically:
   - Package the chart
   - Create a GitHub Release
   - Update the Helm repository index
   - Publish to GitHub Pages

## Setup Instructions

For initial GitHub repository setup and configuration, see [.github/SETUP.md](.github/SETUP.md).

## Chart Configuration

All charts ship with sensible defaults for demo purposes. **Replace sample values** (cluster ID, tokens, regions) with your actual values before deploying to production.

See individual chart READMEs for detailed configuration options:
- [Agent Chart Configuration](charts/agent/README.md)

## Support

- **Documentation:** https://docs.prysm.sh
- **Issues:** https://github.com/prysmsh/charts/issues
- **Slack:** https://prysm.sh/slack

## License

Apache-2.0
