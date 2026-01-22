# Helm Charts by ceiling-cryptopond

This repository contains Helm charts for AI automation infrastructure.

## Available Charts

- [n8n-mcp-server](./n8n-mcp-server/) - n8n workflow automation with MCP server support

## Usage

Add this repository to Helm:

```bash
helm repo add ceiling-cryptopond https://ceiling-cryptopond.github.io/helm-charts
helm install my-release ceiling-cryptopond/n8n-mcp-server
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
