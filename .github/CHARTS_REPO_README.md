# Prysm Helm Charts

Official Helm charts for deploying Prysm components on Kubernetes.

## Usage

Add the Prysm Helm repository:

```bash
helm repo add prysm https://charts.prysm.sh
helm repo update
```

## Available Charts

### Agent Chart

Deploy the Prysm Kubernetes agent as a privileged DaemonSet with RBAC, host networking, and WireGuard configuration.

**Installation:**

```bash
helm install prysm-agent prysm/agent \
  --namespace prysm-system \
  --create-namespace \
  --set configSecret.data.CLUSTER_ID="your-cluster-id" \
  --set configSecret.data.AGENT_TOKEN="tkn_..." \
  --set configSecret.data.REGION="us-east"
```

**Documentation:** See [charts/agent/README.md](charts/agent/README.md)

## Prerequisites

- Kubernetes 1.24+
- Helm 3.8+

## Contributing

Charts are maintained in the [prysmsh/prysm](https://github.com/prysmsh/prysm) repository under `prysm/prysm-charts/charts/`.

To contribute:

1. Make changes in the main repository
2. Charts are automatically synced and released via CI/CD

## Support

- Documentation: https://docs.prysm.sh
- Issues: https://github.com/prysmsh/prysm/issues
- Email: support@prysm.sh

## License

Apache License 2.0
