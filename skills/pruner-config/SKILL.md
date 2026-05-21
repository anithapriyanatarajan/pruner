---
name: pruner-config
description: >-
  Understand, author, validate, or debug tekton-pruner ConfigMap configuration.
  Use when working with TTL-based or history-based pruning rules, namespace
  overrides, resource-group selectors, or per-resource annotations. Covers the
  full spec format, validation rules, and hierarchical override semantics.
license: Apache-2.0
metadata:
  project: tekton-pruner
allowed-tools: Read Bash(kubectl get:*) Bash(kubectl apply:*) Bash(kubectl describe:*)
---

# Pruner Configuration

tekton-pruner is configured via a `ConfigMap` named `tekton-pruner-config`
in the `tekton-pipelines` namespace, plus optional per-namespace annotations.

## Configuration Hierarchy

Settings are resolved in this order (most specific wins):

```
per-resource annotation
  └─ namespace-level ConfigMap entry
       └─ cluster-level default (tekton-pruner-config)
```

See `docs/tutorials/namespace-configuration.md` and
`docs/tutorials/history-based-pruning.md` for worked examples.

## ConfigMap Structure

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: tekton-pruner-config
  namespace: tekton-pipelines
data:
  # Cluster-wide defaults
  global-config: |
    ttlSecondsAfterFinished: 3600     # delete 1h after completion
    successfulPipelineRunsHistoryLimit: 3
    failedPipelineRunsHistoryLimit:    3
    successfulTaskRunsHistoryLimit:    3
    failedTaskRunsHistoryLimit:        3

  # Namespace-level overrides
  my-namespace: |
    ttlSecondsAfterFinished: 600
    successfulPipelineRunsHistoryLimit: 5
```

## Key Fields

| Field | Type | Description |
|-------|------|-------------|
| `ttlSecondsAfterFinished` | int | Seconds after completion before deletion (0 = disabled) |
| `successfulPipelineRunsHistoryLimit` | int | Max successful PipelineRuns to keep |
| `failedPipelineRunsHistoryLimit` | int | Max failed PipelineRuns to keep |
| `successfulTaskRunsHistoryLimit` | int | Max successful TaskRuns to keep |
| `failedTaskRunsHistoryLimit` | int | Max failed TaskRuns to keep |

## Validation

The admission webhook (`pkg/webhook/configmapvalidation.go`) validates all
ConfigMap entries on create/update. Common validation errors:

- Negative values for any limit field
- TTL and history limit both set to `0` (would delete everything immediately)
- Unknown keys in the spec YAML

Validate locally by reviewing `pkg/config/config_validation_test.go`.

## Apply and Verify

```bash
# Apply config
kubectl apply -f config/600-tekton-pruner-default-spec.yaml

# Check the webhook accepted it
kubectl get configmap tekton-pruner-config -n tekton-pipelines -o yaml

# Watch controller logs for pruning activity
kubectl -n tekton-pipelines logs deployment/tekton-pruner-controller -f | grep -E "pruned|TTL|history"
```

## Resource-Group Selectors

Advanced scoping by label selector — see `docs/tutorials/resource-groups.md`:

```yaml
data:
  my-namespace: |
    resourceGroups:
      - selector:
          matchLabels:
            pipeline: nightly-build
        successfulPipelineRunsHistoryLimit: 1
```

## Per-Resource Annotations

Override config at the individual resource level:

```yaml
annotations:
  pruner.tekton.dev/ttl-seconds-after-finished: "300"
  pruner.tekton.dev/successful-history-limit: "2"
```

Annotation keys are defined in `pkg/config/constants.go`. Never hardcode
annotation strings elsewhere.

## References

- `docs/tutorials/getting-started.md` — first-time setup
- `docs/tutorials/time-based-pruning.md` — TTL examples
- `docs/tutorials/history-based-pruning.md` — history limit examples
- `docs/configmap-validation.md` — validation rules reference
- `config/600-tekton-pruner-default-spec.yaml` — default ConfigMap shipped with the release
