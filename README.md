# Helm Charts by Pond International

This repository contains Helm charts for Pond International's infrastructure.

## Available Charts

- [n8n-mcp-server](./n8n-mcp-server/) - n8n workflow automation with MCP server support

## Usage

Add this repository to Helm:

```bash
helm repo add pond-international https:// Pond-International.github.io/helm-charts
helm install my-release pond-international/<chart-name>
```

## Development

### Prerequisites

- Helm v3.x
- kubectl
- kind or minikube (for local testing)

### Linting

```bash
helm lint ./<chart-name>
```

### Template Testing

```bash
helm template ./<chart-name>
```

### Documentation

Charts are documented using [helm-docs](https://github.com/norwoodj/helm-docs).

```bash
helm-docs -t ./<chart-name>/README.md.gotmpl -c ./<chart-name>/
```
