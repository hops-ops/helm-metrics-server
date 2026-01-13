# helm-metrics-server

A Crossplane configuration that installs [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server) via Helm.

Metrics Server collects resource metrics from Kubelets and exposes them in Kubernetes apiserver through Metrics API for use by Horizontal Pod Autoscaler and Vertical Pod Autoscaler. Metrics API can also be accessed by `kubectl top`, making it easier to debug autoscaling pipelines.

## Getting Started

### Minimal Installation

Deploy metrics-server with sensible defaults:

```yaml
apiVersion: helm.hops.ops.com.ai/v1alpha1
kind: MetricsServer
metadata:
  name: metrics-server
  namespace: my-env
spec:
  clusterName: my-cluster
```

This installs metrics-server in `kube-system` namespace using defaults optimized for most clusters.

### Standard Installation

Customize the deployment with labels and Helm values:

```yaml
apiVersion: helm.hops.ops.com.ai/v1alpha1
kind: MetricsServer
metadata:
  name: metrics-server
  namespace: my-env
spec:
  clusterName: my-cluster
  labels:
    team: platform
  namespace: kube-system
  values:
    replicas: 2
    resources:
      requests:
        cpu: 100m
        memory: 200Mi
```

## Configuration

| Field | Description | Default |
|-------|-------------|---------|
| `spec.clusterName` | Target cluster name, used for provider config | Required |
| `spec.namespace` | Kubernetes namespace for the Helm release | `kube-system` |
| `spec.name` | Helm release name | XR metadata.name |
| `spec.labels` | Labels merged with defaults | `{}` |
| `spec.providerConfigRef.name` | Helm ProviderConfig name | `clusterName` |
| `spec.providerConfigRef.kind` | ProviderConfig kind | `ProviderConfig` |
| `spec.values` | Helm values merged with defaults | `{}` |
| `spec.overrideAllValues` | Replace all default Helm values | `{}` |

### Default Labels

All resources automatically receive these labels:

- `hops.ops.com.ai/managed: "true"`
- `hops.ops.com.ai/metricsserver: <name>`

Custom labels from `spec.labels` are merged with these defaults.

### Helm Values

The configuration applies these default Helm values:

```yaml
fullnameOverride: metrics-server
nameOverride: metrics-server
```

Use `spec.values` to add additional values (merged with defaults) or `spec.overrideAllValues` to completely replace all defaults.

## Chart Details

- **Chart**: metrics-server
- **Repository**: https://kubernetes-sigs.github.io/metrics-server
- **Version**: 3.12.2

## Dependencies

- Crossplane 2.0+
- [provider-helm](https://marketplace.upbound.io/providers/crossplane-contrib/provider-helm) >= v1.0.2
- [function-auto-ready](https://marketplace.upbound.io/functions/crossplane-contrib/function-auto-ready) >= v0.6.0

## Development

```bash
# Render examples
make render:all

# Validate examples
make validate:all

# Run unit tests
make test

# Run E2E tests
make e2e

# Build package
make build
```
