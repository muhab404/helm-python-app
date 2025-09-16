# Python App Helm Chart

A Kubernetes Helm chart for deploying the Python Flask API application with configurable ingress, autoscaling, and service mesh support.

## Chart Information

- **Chart Name**: python-app
- **Chart Version**: 0.1.0
- **App Version**: 1.16.0
- **Type**: Application

## Features

- Kubernetes Deployment with configurable replicas
- Service exposure (ClusterIP/NodePort/LoadBalancer)
- Ingress support with NGINX controller
- Horizontal Pod Autoscaler (HPA)
- Gateway API HTTPRoute support
- ECR image pull secrets
- Configurable security contexts
- Health checks (liveness/readiness probes)

## Installation

### Prerequisites

- Kubernetes cluster (1.19+)
- Helm 3.x
- NGINX Ingress Controller (if using ingress)

### Install Chart

```bash
# Add the chart repository (if applicable)
helm repo add python-app <repository-url>
helm repo update

# Install with default values
helm install my-python-app python-app

# Install with custom values
helm install my-python-app python-app -f custom-values.yaml

# Install from local directory
helm install my-python-app .
```

## Configuration

### Key Values

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `1` |
| `image.repository` | Container image repository | `256057170649.dkr.ecr.us-east-1.amazonaws.com/python-app` |
| `image.tag` | Container image tag | `9a3afa3` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `service.type` | Kubernetes service type | `ClusterIP` |
| `service.port` | Service port | `5000` |
| `containerPort` | Container port | `5000` |
| `ingress.enabled` | Enable ingress | `true` |
| `autoscaling.enabled` | Enable HPA | `false` |
| `httpRoute.enabled` | Enable Gateway API HTTPRoute | `false` |

### Image Pull Secrets

The chart is configured to use ECR image pull secrets:

```yaml
imagePullSecrets:
  - name: ecr-secret
```

### Ingress Configuration

When enabled, the ingress is configured for:
- **Host**: `python-app.local`
- **Ingress Class**: `nginx`
- **Path**: `/` (Prefix)
- **Rewrite Target**: `/`

### Autoscaling

HPA can be enabled with CPU/Memory metrics:

```yaml
autoscaling:
  enabled: true
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80
  targetMemoryUtilizationPercentage: 80
```

## Templates

The chart includes the following Kubernetes resources:

- **Deployment**: Main application deployment
- **Service**: Service to expose the application
- **Ingress**: NGINX ingress for external access
- **HPA**: Horizontal Pod Autoscaler for scaling
- **HTTPRoute**: Gateway API route (optional)
- **ServiceAccount**: Service account for the pods
- **Tests**: Connection test pod


## GitOps Integration

This chart is designed for GitOps workflows where:
1. CI/CD pipeline builds and pushes new images
2. Pipeline updates `values.yaml` with new image tags
3. ArgoCD detects changes and deploys automatically

The image tag is automatically updated by the CI pipeline using the commit SHA.

## Accessing the Application

### Via Ingress (Default)

Add to `/etc/hosts`:
```
<ingress-ip> python-app.local
```

Access: `http://python-app.local`

### Via Port Forward

```bash
kubectl port-forward svc/python-app 5000:5000
```

Access: `http://localhost:5000`

## API Endpoints

- `GET /users` - List all users
- `GET /users/<id>` - Get user by ID
- `GET /products` - List all products  
- `GET /products/<id>` - Get product by ID

## Uninstallation

```bash
helm uninstall my-python-app
```