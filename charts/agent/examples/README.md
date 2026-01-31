# Prysm Agent Helm Chart Examples

This directory contains example values files for different deployment scenarios.

## Available Examples

### Production (`values-production.yaml`)

**Recommended for production deployments**

Features:
- Uses distroless image for maximum security
- Production-grade resource limits
- Security annotations enabled
- eBPF collector enabled for enhanced monitoring
- Comprehensive logging and metrics
- Reasonable security scanning intervals

Install:
```bash
helm install prysm-agent prysm/agent \
  --namespace prysm-system \
  --create-namespace \
  -f examples/values-production.yaml \
  --set configSecret.data.AGENT_TOKEN="your-token"
```

### Development (`values-development.yaml`)

**For development and testing environments**

Features:
- Uses alpine image with shell and debug tools
- Lower resource limits
- Debug-level logging
- Frequent scanning for testing
- eBPF collector disabled

Install:
```bash
helm install prysm-agent prysm/agent \
  --namespace prysm-dev \
  --create-namespace \
  -f examples/values-development.yaml
```

### Minimal (`values-minimal.yaml`)

**For resource-constrained environments or testing**

Features:
- Uses distroless image
- Minimal resource limits
- Logging and metrics disabled
- Scanning disabled
- Only essential volumes mounted

Install:
```bash
helm install prysm-agent prysm/agent \
  --namespace prysm-minimal \
  --create-namespace \
  -f examples/values-minimal.yaml \
  --set configSecret.data.AGENT_TOKEN="your-token"
```

## Customizing Examples

You can combine examples with additional overrides:

```bash
# Production with custom region
helm install prysm-agent prysm/agent \
  -f examples/values-production.yaml \
  --set configSecret.data.REGION="eu-west-1"

# Development with custom backend URL
helm install prysm-agent prysm/agent \
  -f examples/values-development.yaml \
  --set configSecret.data.BACKEND_URL="http://localhost:8080"

# Multiple values files
helm install prysm-agent prysm/agent \
  -f examples/values-production.yaml \
  -f my-custom-values.yaml
```

## Security Best Practices

### For Production:
1. ✅ Use `distroless` image tag
2. ✅ Enable security annotations
3. ✅ Use external secret management (Vault, AWS Secrets Manager, etc.)
4. ✅ Enable eBPF collector for enhanced security monitoring
5. ✅ Configure appropriate resource limits
6. ✅ Enable logging and metrics

### For Development:
1. ✅ Use `alpine` image tag for debugging
2. ✅ Keep resource limits low
3. ✅ Use debug-level logging
4. ⚠️  OK to commit example tokens (but not real ones!)

## Common Overrides

### Use External Secret

Instead of creating a secret in the chart:

```bash
helm install prysm-agent prysm/agent \
  -f examples/values-production.yaml \
  --set configSecret.create=false \
  --set configSecret.existingSecret=my-prysm-secret
```

### Change Image

```bash
helm install prysm-agent prysm/agent \
  -f examples/values-production.yaml \
  --set image.repository=my-registry/prysm-agent \
  --set image.tag=v1.2.3
```

### Adjust Resources

```bash
helm install prysm-agent prysm/agent \
  -f examples/values-production.yaml \
  --set resources.limits.memory=512Mi \
  --set resources.limits.cpu=1000m
```

## Verification

After installation, verify the agent is running:

```bash
# Check pod status
kubectl get pods -n prysm-system -l app=prysm-agent

# Check logs
kubectl logs -n prysm-system -l app=prysm-agent -f

# Check resources
kubectl top pods -n prysm-system

# Test connectivity
kubectl port-forward -n prysm-system svc/prysm-agent 8080:8080
curl http://localhost:8080/health
```

## Upgrading

To upgrade an existing installation with new values:

```bash
helm upgrade prysm-agent prysm/agent \
  -f examples/values-production.yaml \
  --reuse-values
```

## Troubleshooting

### Agent not starting

Check logs:
```bash
kubectl logs -n prysm-system -l app=prysm-agent --tail=100
```

Check events:
```bash
kubectl get events -n prysm-system --sort-by='.lastTimestamp'
```

### WireGuard issues

Verify kernel module:
```bash
kubectl exec -n prysm-system <pod-name> -- sh -c "lsmod | grep wireguard"
```

### Permission issues

Check RBAC:
```bash
kubectl auth can-i --list --as=system:serviceaccount:prysm-system:prysm-agent
```

## Support

- Documentation: https://docs.prysm.sh
- Issues: https://github.com/prysmsh/prysm/issues
- Email: support@prysm.sh
