# n8n-MCP Helm Chart

A Helm chart for deploying [n8n-MCP](https://github.com/czlonkowski/n8n-mcp) - a Model Context Protocol server that provides AI assistants with comprehensive access to n8n node documentation, properties, and operations.

## Features

- **1,084 n8n nodes** - 537 core + 547 community nodes
- **2,709 workflow templates** - with smart filtering
- **2,646 real-world examples** - pre-extracted configurations
- **Complete validation** - nodes, workflows, and AI Agent validation
- **HTTP mode** - for Kubernetes/Qovery deployment

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- (Optional) n8n instance with API access for workflow management

## Quick Start

### Basic Installation (Documentation Only)

```bash
# Generate auth token
AUTH_TOKEN=$(openssl rand -hex 32)

# Install
helm install n8n-mcp ./helm-charts/n8n-mcp-server \
  --set mcp.authToken=$AUTH_TOKEN
```

### With n8n Integration (Full Features)

```bash
# Create values file
cat > my-values.yaml << EOF
mcp:
  mode: "http"
  authToken: "$(openssl rand -hex 32)"

n8n:
  enabled: true
  apiUrl: "https://your-n8n-instance.com"
  apiKey: "your-n8n-api-key"
EOF

# Install
helm install n8n-mcp ./helm-charts/n8n-mcp-server -f my-values.yaml
```

## Configuration

### MCP Server Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `mcp.mode` | Server mode: "stdio" or "http" | `"http"` |
| `mcp.port` | HTTP server port | `3000` |
| `mcp.authToken` | Auth token for HTTP mode (required!) | `""` |
| `mcp.existingAuthSecret` | Use existing secret for auth token | `""` |
| `mcp.logLevel` | Log level: error, warn, info, debug | `"error"` |
| `mcp.disableConsoleOutput` | Disable console output | `true` |

### n8n Integration (Optional)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `n8n.enabled` | Enable n8n integration | `false` |
| `n8n.apiUrl` | n8n instance URL (without /api/v1) | `""` |
| `n8n.apiKey` | n8n API key | `""` |
| `n8n.existingSecret` | Use existing secret for n8n credentials | `""` |
| `n8n.webhook.securityMode` | Webhook security: strict, moderate, permissive | `"strict"` |

### Resources

| Parameter | Description | Default |
|-----------|-------------|---------|
| `resources.requests.cpu` | CPU request | `100m` |
| `resources.requests.memory` | Memory request | `128Mi` |
| `resources.limits.cpu` | CPU limit | `500m` |
| `resources.limits.memory` | Memory limit | `512Mi` |

### Other Options

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `1` |
| `image.repository` | Docker image | `ghcr.io/czlonkowski/n8n-mcp` |
| `image.tag` | Image tag | `"latest"` |
| `telemetry.disabled` | Disable anonymous telemetry | `false` |
| `persistence.enabled` | Enable persistence for SQLite | `false` |
| `ingress.enabled` | Enable ingress | `false` |

## Qovery Deployment

### Option 1: Helm Service

1. Go to Qovery Console → Environment → **Add Service** → **Helm**
2. Configure:
   - **Source**: Git repository containing this chart
   - **Chart path**: `helm-charts/n8n-mcp-server`
   - **Values override**:

```yaml
mcp:
  mode: "http"
  authToken: "your-secure-token-here"

# Optional: Enable n8n integration
n8n:
  enabled: true
  apiUrl: "https://your-n8n.qovery.io"
  apiKey: "your-api-key"

resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 512Mi
```

### Option 2: Using Qovery CLI

```bash
# Create and deploy
qovery helm create \
  --name n8n-mcp \
  --chart ./helm-charts/n8n-mcp-server \
  --set mcp.mode=http \
  --set mcp.authToken=$AUTH_TOKEN
```

## Available MCP Tools

### Core Tools (7 tools) - Always Available

| Tool | Description |
|------|-------------|
| `tools_documentation` | Get documentation for any MCP tool |
| `search_nodes` | Full-text search across 1,084 nodes |
| `get_node` | Get node info, docs, properties, versions |
| `validate_node` | Validate node configurations |
| `validate_workflow` | Complete workflow validation |
| `search_templates` | Search 2,709 workflow templates |
| `get_template` | Get complete workflow JSON |

### n8n Management Tools (13 tools) - Requires n8n Integration

| Tool | Description |
|------|-------------|
| `n8n_create_workflow` | Create new workflows |
| `n8n_get_workflow` | Get workflow details |
| `n8n_update_full_workflow` | Update entire workflow |
| `n8n_update_partial_workflow` | Update using diff operations |
| `n8n_delete_workflow` | Delete workflows |
| `n8n_list_workflows` | List workflows with filtering |
| `n8n_validate_workflow` | Validate workflows by ID |
| `n8n_autofix_workflow` | Auto-fix common errors |
| `n8n_workflow_versions` | Version history and rollback |
| `n8n_deploy_template` | Deploy templates from n8n.io |
| `n8n_test_workflow` | Test/trigger workflow execution |
| `n8n_executions` | Manage executions |
| `n8n_health_check` | Check n8n API connectivity |

## Claude Desktop Configuration

After deploying to Kubernetes, configure Claude Desktop to connect:

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "url": "https://your-n8n-mcp-endpoint.com",
      "headers": {
        "Authorization": "Bearer your-auth-token"
      }
    }
  }
}
```

## Using Existing Secrets

### For MCP Auth Token

```bash
# Create secret
kubectl create secret generic n8n-mcp-auth \
  --from-literal=MCP_AUTH_TOKEN=$(openssl rand -hex 32)

# Install with existing secret
helm install n8n-mcp ./helm-charts/n8n-mcp-server \
  --set mcp.existingAuthSecret=n8n-mcp-auth
```

### For n8n Credentials

```bash
# Create secret
kubectl create secret generic n8n-credentials \
  --from-literal=N8N_API_URL=https://your-n8n.com \
  --from-literal=N8N_API_KEY=your-api-key

# Install with existing secret
helm install n8n-mcp ./helm-charts/n8n-mcp-server \
  --set mcp.authToken=$AUTH_TOKEN \
  --set n8n.enabled=true \
  --set n8n.existingSecret=n8n-credentials
```

## Full values.yaml Example

```yaml
replicaCount: 1

image:
  repository: ghcr.io/czlonkowski/n8n-mcp
  pullPolicy: IfNotPresent
  tag: "latest"

mcp:
  mode: "http"
  port: 3000
  authToken: "your-secure-token"
  logLevel: "error"
  disableConsoleOutput: true

n8n:
  enabled: true
  apiUrl: "https://n8n.example.com"
  apiKey: "n8n_api_xxxxx"
  webhook:
    securityMode: "strict"

telemetry:
  disabled: true

resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 100m
    memory: 128Mi

ingress:
  enabled: true
  className: "nginx"
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
  hosts:
    - host: n8n-mcp.example.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: n8n-mcp-tls
      hosts:
        - n8n-mcp.example.com

persistence:
  enabled: false
```

## Troubleshooting

### Check Pod Status

```bash
kubectl get pods -l app.kubernetes.io/name=n8n-mcp
```

### View Logs

```bash
kubectl logs -l app.kubernetes.io/name=n8n-mcp -f
```

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Pod CrashLoopBackOff | Missing auth token | Set `mcp.authToken` |
| 401 Unauthorized | Invalid auth token | Check `MCP_AUTH_TOKEN` |
| n8n tools not available | n8n not enabled | Set `n8n.enabled=true` |
| Connection refused to n8n | Wrong URL | Check `n8n.apiUrl` |

## Upgrading

```bash
helm upgrade n8n-mcp ./helm-charts/n8n-mcp-server -f my-values.yaml
```

## Uninstalling

```bash
helm uninstall n8n-mcp
```

## Links

- [n8n-MCP GitHub](https://github.com/czlonkowski/n8n-mcp)
- [n8n Documentation](https://docs.n8n.io)
- [Model Context Protocol](https://modelcontextprotocol.io)

## License

MIT - Same as [n8n-MCP](https://github.com/czlonkowski/n8n-mcp)
