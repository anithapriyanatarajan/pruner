<!--

---
title: "Tekton resource pruning based on configuration"
linkTitle: "Tekton Resource Pruning"
weight: 10
description: Configuration based event driven pruning for Tekton
cascade:
  github_project_repo: https://github.com/tektoncd/pruner
---
-->

# Tekton Pruner

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/tektoncd/pruner/blob/main/LICENSE)

Tekton Pruner automatically manages the lifecycle of Tekton resources by cleaning up completed PipelineRuns and TaskRuns based on configurable time-based (TTL) and history-based policies.

## Overview

Tekton Pruner provides event-driven and configuration-based cleanup for Tekton Pipeline resources with flexible, hierarchical configuration options.

<p align="center">
<img src="docs/images/pruner_functional_abstract.png" alt="Tekton Pruner overview"></img>
</p>

> 📖 **For detailed architecture, component interactions, and design details, see [ARCHITECTURE.md](ARCHITECTURE.md)**

## Key Features

- **Time-based Pruning (TTL)**: Automatically delete resources after a specified duration
- **History-based Pruning**: Retain a fixed number of most recent runs (successful/failed)
- **Hierarchical Configuration**: Global defaults with namespace and resource-level overrides
- **Selector-based Policies**: Fine-grained control using labels and annotations
- **Validating Webhook**: Prevents invalid configurations

> 📖 **See [ARCHITECTURE.md](ARCHITECTURE.md) for detailed information on:**
> - System architecture and component interactions
> - Configuration hierarchy and precedence rules
> - Selector matching logic and examples
> - Data flows and processing pipelines
> - Monitoring, troubleshooting, and best practices

## Installation

**Prerequisites:**
- Kubernetes cluster with [Tekton Pipelines](https://github.com/tektoncd/pipeline/blob/main/docs/install.md) installed

**Install:**
```bash
export VERSION=0.1.0  # Update as needed
kubectl apply -f "https://github.com/tektoncd/pruner/releases/download/v$VERSION/release-v$VERSION.yaml"
```

**Verify:**
```bash
kubectl get pods -n tekton-pipelines -l app=tekton-pruner-controller
```

## Configuration

> **CRITICAL**: All pruner ConfigMaps **MUST** include these labels for validation and processing:
> 
> ```yaml
> labels:
>   app.kubernetes.io/part-of: tekton-pruner
>   pruner.tekton.dev/config-type: <global|namespace>
> ```
> 
> **System Boundaries**: Do NOT create namespace-level ConfigMaps in:
> - System namespaces (`kube-*`, `openshift-*`)
> - Tekton controller namespaces (`tekton-pipelines`, `tekton-*`)

### Configuration Hierarchy

Tekton Pruner uses a three-level configuration hierarchy:

1. **Global Config** - Cluster-wide defaults (`tekton-pruner-default-spec` in `tekton-pipelines` namespace)
2. **Namespace Config** - Per-namespace overrides (when `enforcedConfigLevel: namespace`)
3. **Resource Groups** - Selector-based policies for fine-grained control

> 📖 **See [ARCHITECTURE.md](ARCHITECTURE.md#configuration-hierarchy)** for detailed precedence rules and configuration flow diagrams

### Quick Start: Global Configuration

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: tekton-pruner-default-spec
  namespace: tekton-pipelines
  labels:
    app.kubernetes.io/part-of: tekton-pruner
    pruner.tekton.dev/config-type: global
data:
  global-config: |
    enforcedConfigLevel: global
    ttlSecondsAfterFinished: 300
    successfulHistoryLimit: 3
    failedHistoryLimit: 3
```

### Namespace-Specific Configuration

**Option 1: Inline in Global ConfigMap**
```yaml
data:
  global-config: |
    enforcedConfigLevel: namespace
    namespaces:
      my-namespace:
        ttlSecondsAfterFinished: 60
```

**Option 2: Separate Namespace ConfigMap** (Recommended for self-service)
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: tekton-pruner-namespace-spec
  namespace: my-app-namespace  # User namespace only
  labels:
    app.kubernetes.io/part-of: tekton-pruner
    pruner.tekton.dev/config-type: namespace
data:
  ns-config: |
    ttlSecondsAfterFinished: 300
    successfulHistoryLimit: 5
```

### Resource Groups (Fine-grained Control)

Group resources by labels/annotations for different policies within a namespace.

> **Note:** Selectors only work in namespace-level ConfigMaps, not global ConfigMaps.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: tekton-pruner-namespace-spec
  namespace: my-app
  labels:
    app.kubernetes.io/part-of: tekton-pruner
    pruner.tekton.dev/config-type: namespace
data:
  ns-config: |
    pipelineRuns:
      - selector:
          - matchLabels:
              environment: production
        ttlSecondsAfterFinished: 604800
        successfulHistoryLimit: 10
      - selector:
          - matchLabels:
              environment: development
        ttlSecondsAfterFinished: 300
        successfulHistoryLimit: 3
```

> 📖 **See [ARCHITECTURE.md](ARCHITECTURE.md#selector-matching)** for detailed selector matching logic, AND/OR rules, and bug fixes applied

## Documentation

### Getting Started
- [Getting Started Guide](docs/tutorials/getting-started.md) - Quick start and basic configuration
- [Namespace Configuration](docs/tutorials/namespace-configuration.md) - Namespace-level setup
- [Resource Groups](docs/tutorials/resource-groups.md) - Selector-based policies

### Reference Documentation
- **[ARCHITECTURE.md](ARCHITECTURE.md)** - Complete system architecture, components, data flows, and design details
- [ConfigMap Validation](docs/configmap-validation.md) - Validation rules and webhook behavior
- [Troubleshooting](docs/troubleshooting.md) - Common issues and solutions
- [Metrics](docs/metrics.md) - Monitoring and observability

## Contributing

- See [DEVELOPMENT.md](DEVELOPMENT.md) for development setup
- Submit issues and pull requests
- Follow coding standards and test coverage requirements

## License

Apache License 2.0 - See [LICENSE](LICENSE) for details