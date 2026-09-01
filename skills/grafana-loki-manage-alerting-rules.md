---
name: grafana-loki-manage-alerting-rules
description: Read and write Grafana Loki ruler alerting and recording rule groups over the Prometheus-compatible API.
api: Grafana Loki HTTP API (ruler)
version: v1
generated: '2026-08-27'
method: generated
source: https://grafana.com/docs/loki/latest/reference/loki-http-api/
operations:
  - GET /loki/api/v1/rules
  - GET /loki/api/v1/rules/{namespace}
  - GET /loki/api/v1/rules/{namespace}/{groupName}
  - POST /loki/api/v1/rules/{namespace}
  - DELETE /loki/api/v1/rules/{namespace}/{groupName}
  - DELETE /loki/api/v1/rules/{namespace}
  - GET /prometheus/api/v1/rules
  - GET /prometheus/api/v1/alerts
  - GET /ruler/ring
---

# Manage Loki alerting and recording rules

The ruler is Prometheus-shaped. If you already speak Prometheus rule groups, the only thing that
changes is that `expr` holds LogQL rather than PromQL.

## Surface map

| Purpose | Endpoint |
|---|---|
| List every rule group for the tenant | `GET /loki/api/v1/rules` |
| List groups in one namespace | `GET /loki/api/v1/rules/{namespace}` |
| Read one group | `GET /loki/api/v1/rules/{namespace}/{groupName}` |
| Create or replace a group | `POST /loki/api/v1/rules/{namespace}` |
| Delete one group | `DELETE /loki/api/v1/rules/{namespace}/{groupName}` |
| Delete a whole namespace | `DELETE /loki/api/v1/rules/{namespace}` |
| Read rules, Prometheus format | `GET /prometheus/api/v1/rules` |
| Read firing alerts | `GET /prometheus/api/v1/alerts` |
| Ruler ring health | `GET /ruler/ring` |

The `/api/prom/rules...` variants exist and are Prometheus-API-compatible; the documentation
states the result formats can be used interchangeably.

## Write semantics

`POST /loki/api/v1/rules/{namespace}` is **set-based, not patch-based**: it creates the named
group or replaces it wholesale. It takes YAML.

```yaml
name: high-error-rate
interval: 1m
rules:
  - alert: HighErrorRate
    expr: sum(rate({service_name="checkout"} |= "ERROR" [5m])) > 10
    for: 5m
    labels:
      severity: page
    annotations:
      summary: checkout error rate is elevated
```

## Reversibility

- **Replacing a group is reversible** — re-POST the previous YAML. Read the group with
  `GET /loki/api/v1/rules/{namespace}/{groupName}` and keep the response *before* you write.
- **Deleting a group is not.** There is no undo on
  `DELETE /loki/api/v1/rules/{namespace}/{groupName}`, and none on deleting a namespace. The
  only recovery is the copy you kept. Read before you delete, always.

## Tenancy

Rule groups are per-tenant. Send `X-Scope-OrgID`, or Basic auth with the tenant as user on
Grafana Cloud Logs / Grafana Enterprise Logs. Pipe-separated multi-tenant values work on the
query path; do not use them on a rule write.
