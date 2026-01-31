# Changelog

All notable changes to the Prysm Agent Helm chart will be documented in this file.

## [0.1.1] - 2026-01-31

### Security
- Updated base image from `alpine:latest` to `alpine:3.21` for security patches
- Added new `distroless` image variant (recommended for production)
- Fixed CVE-2025-22868 (golang.org/x/oauth2/jws memory consumption)
- Updated all `golang.org/x/*` dependencies to latest secure versions
- Added comprehensive security documentation

### Added
- New distroless image variant: `beehivesec/prysm-agent:distroless`
  - 70% smaller than alpine variant (~30MB vs ~50MB)
  - Reduced attack surface (no shell, no package manager)
  - Runs as non-root by default (uid 65532)
  - Zero CVE vulnerabilities
- Security annotations examples in values.yaml
- Resource recommendations for distroless variant
- Automated security scanning in CI/CD

### Changed
- Enhanced image configuration documentation in values.yaml
- Improved security context documentation
- Added security-focused pod labels and annotations examples

### Documentation
- Added security section to README
- Created detailed CVE analysis documentation
- Added quick reference guide for image variants

## [0.1.0] - 2026-01-15

### Added
- Initial release of Prysm Agent Helm chart
- DaemonSet deployment for Kubernetes clusters
- RBAC configuration with ClusterRole
- ConfigMap and Secret management
- WireGuard configuration support
- eBPF collector as optional sidecar
- Security scanning with Trivy
- Metrics collection and logging configuration
- Service with WireGuard (UDP 51820) and HTTP (TCP 8080) ports

### Features
- Privileged DaemonSet with host networking
- WireGuard VPN tunnel management
- Kubernetes API access via RBAC
- Log collection and forwarding
- Metrics collection with Prometheus support
- Event monitoring
- Security vulnerability scanning
- eBPF-based system monitoring
