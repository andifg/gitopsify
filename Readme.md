# GitOpsify - GitOps Infrastructure Management

This repository contains GitOps configurations for managing Kubernetes clusters and applications using ArgoCD.

## Project Structure

```
gitopsify/
├── bootstrap/           # Bootstrap ArgoCD applications
│   ├── base/           # Base bootstrap configuration
│   └── overlays/       # Environment-specific overlays
│       └── hetzner-k8s/ # Hetzner K8s cluster overlay
├── clusters/            # Cluster configuration
│   ├── base/           # Base cluster config
│   └── overlays/       # Environment-specific overlays
│       └── hetzner-k8s/ # Hetzner K8s cluster config
├── components/          # Reusable application components
└── tenants/            # Tenant-specific configurations
```

## Prerequisites

- Kubernetes cluster with ArgoCD installed
- `kubectl` configured to access your cluster
- `kustomize` (optional, for local testing)

## Quick Start

To apply the bootstrap application: `kubectl apply -k bootstrap/overlays/hetzner-k8s`

