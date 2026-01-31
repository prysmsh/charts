# Prysm Agent Chart

Helm chart that deploys the Prysm Kubernetes agent as a privileged DaemonSet with the RBAC, host networking, and WireGuard configuration it requires for secure infrastructure access.

## Prerequisites

- Kubernetes 1.24+
- Helm 3.8+
- A Prysm cluster ID and agent token provisioned via the control plane
- WireGuard enabled hosts (for `/etc/wireguard` mounts) and access to the Prysm DERP relay

## Installation

Supply the cluster-specific identifiers and secret material via Helm values. The chart will create a Secret that is consumed by the agent when `configSecret.create=true` (default):

```bash
helm repo add prysm https://charts.prysm.sh
helm install prysm-agent prysm/agent \
  --namespace prysm-system \
  --create-namespace \
  --set configSecret.data.CLUSTER_ID="cluster-prod" \
  --set configSecret.data.AGENT_TOKEN="tkn_..." \
  --set configSecret.data.BACKEND_URL="https://api.prysm.sh" \
  --set configSecret.data.REGION="us-east" \
  --set configSecret.data.DERP_SERVER="wss://derp.prysm.sh/derp"
```

The defaults in `values.yaml` are illustrative and safe to install, but they will not establish a live connection until you replace the example token, cluster ID, and region with values from your Prysm control plane.

If you manage secrets externally, disable creation and reference an existing Kubernetes Secret with the same environment variable keys:

```bash
helm install prysm-agent prysm/agent \
  --namespace prysm-system \
  --set configSecret.create=false \
  --set configSecret.existingSecret=prysm-agent-config
```

## Configuration

| Value | Description | Default |
|-------|-------------|---------|
| `image.repository` | Container image repository | `ghcr.io/prysmsh/agent` |
| `image.tag` | Container image tag | `latest` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `serviceAccount.create` | Create the agent ServiceAccount | `true` |
| `rbac.create` | Provision ClusterRole and binding with broad Kubernetes access (matches Docker Compose manifest) | `true` |
| `configSecret.create` | Manage the Secret with agent configuration | `true` |
| `configSecret.name` | Override name for managed Secret | `<release>-config` |
| `configSecret.existingSecret` | Use a pre-created Secret instead of managing one | `""` |
| `configSecret.data.CLUSTER_ID` | Cluster identifier reported to the Prysm backend | `example-cluster` |
| `configSecret.data.AGENT_TOKEN` | Agent authentication token (replace with tenant token) | `example-agent-token` |
| `configSecret.data.BACKEND_URL` | Prysm backend API endpoint | `https://api.prysm.sh` |
| `configSecret.data.REGION` | Logical region label for the cluster | `us-east-1` |
| `configSecret.data.DERP_SERVER` | Primary DERP relay URL | `wss://derp.prysm.sh/derp` |
| `configSecret.data.DERP_SERVERS` | Comma-separated list of DERP relays | `wss://derp.prysm.sh/derp` |
| `configSecret.data.ORGANIZATION_ID` | Numeric Prysm organization identifier | `"1"` |
| `configSecret.data.LOG_*` | Logging and sink tuning flags used by the enhanced agent | see `values.yaml` |
| `hostNetwork` | Run the agent on the host network (required for WireGuard) | `true` |
| `hostPID` | Share the host PID namespace | `true` |
| `containerSecurityContext` | Container security settings (privileged + capabilities) | see `values.yaml` |
| `volumes.wireguardConfig.*` | HostPath mount for WireGuard configuration | `/etc/wireguard` |
| `volumes.modules.*` | HostPath mount for kernel modules (`/lib/modules`) | enabled |
| `volumes.kubeconfig.*` | HostPath mount for admin kubeconfig | enabled |
| `service.enabled` | Publish NodePort service for WireGuard (UDP 51820) and HTTP health (TCP 8080) | `true` |
| `service.ports.wireguard.nodePort` | Explicit NodePort for WireGuard (set to expose on fixed port) | `null` |
| `resources` | Resource requests/limits | CPU `100m/500m`, Memory `64Mi/256Mi` |
| `tolerations` | Default tolerations to schedule on tainted nodes | `Exists` for `NoSchedule`, `NoExecute` |
| `extraEnv` | Additional environment variables | `[]` |
| `extraEnvFrom` | Additional `envFrom` references | `[]` |
| `extraVolumes` | Additional volumes to mount | `[]` |
| `extraVolumeMounts` | Additional volume mounts | `[]` |

Refer to `values.yaml` for the full list of optional logging and DERP tuning values that mirror the Docker Compose environment.

## Upgrading

Override any value and upgrade in place. For example, rotate the agent token and region label:

```bash
helm upgrade prysm-agent prysm/agent \
  --namespace prysm-system \
  --set configSecret.data.AGENT_TOKEN="tkn_new" \
  --set configSecret.data.REGION="us-west"
```

## Removing

```bash
helm uninstall prysm-agent --namespace prysm-system
```
