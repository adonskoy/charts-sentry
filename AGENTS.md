## Cursor Cloud specific instructions

This is a **Helm chart repository** (not an application). There are three charts under `charts/`:

| Chart | Path | Description |
|-------|------|-------------|
| `sentry` | `charts/sentry/` | Main Sentry Helm chart (primary) |
| `sentry-kubernetes` | `charts/sentry-kubernetes/` | K8s event reporter helper chart |
| `clickhouse` | `charts/clickhouse/` | Deprecated ClickHouse chart |

### Key tools

- **Helm v3.14.4** — chart rendering, linting, dependency management
- **chart-testing (`ct`) v3.11.0** — CI-grade lint and validation tool
- **yamale / yamllint** — YAML schema and style validation (required by `ct`)

### Lint / Test / Build commands

- `helm lint charts/sentry` — lint the main chart
- `helm lint charts/sentry-kubernetes` — lint the helper chart
- `helm lint charts/clickhouse` — lint the deprecated chart
- `ct lint --all --check-version-increment=false` — CI-equivalent lint for all charts (requires `PATH` to include `~/.local/bin` for yamale/yamllint)
- `helm template <release-name> charts/sentry` — render full Kubernetes manifests (the "build" equivalent)
- `helm dependency build charts/sentry` — rebuild subchart archives after dependency changes

### Required Helm repos

These repos must be added before `helm dependency build` or `ct lint` will work:

```
helm repo add sentry-kubernetes https://sentry-kubernetes.github.io/charts
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add altinity https://helm.altinity.com
```

### Gotchas

- The `kafka.config` coalesce warning (`skipped value for kafka.config: Not a table`) is a known benign warning from the Bitnami Kafka subchart and does not indicate an error.
- `ct lint` requires `yamale` and `yamllint` Python packages on `PATH`. Install them with `pip3 install yamale yamllint` and ensure `~/.local/bin` is on `PATH`.
- Full CI also runs `ct install` which deploys to a `kind` Kubernetes cluster with an external ClickHouse operator. This requires a running K8s cluster and is not part of the basic lint workflow.
- The `charts/sentry/ci/kind-values.yaml` file provides CI test values; `ct lint` automatically picks it up.
