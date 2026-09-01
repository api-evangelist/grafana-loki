---
name: grafana-loki-push-logs
description: Ingest log lines into Grafana Loki without getting them silently discarded.
api: Grafana Loki HTTP API
version: v1
generated: '2026-08-27'
method: generated
source: https://grafana.com/docs/loki/latest/reference/loki-http-api/
operations:
  - POST /loki/api/v1/push
  - POST /otlp/v1/logs
  - GET /ready
  - GET /metrics
---

# Push logs into Grafana Loki

There is no un-push. Once a line is accepted it can only be removed through the deletion API
(see `grafana-loki-delete-logs.md`) or by waiting for retention to expire it. Get the payload
right on the first attempt.

## 1. Choose the ingest path

| Path | Use when |
|---|---|
| `POST /loki/api/v1/push` | You control the producer and want Loki's native shape. |
| `POST /otlp/v1/logs` | You already emit OpenTelemetry logs. No connector needed — Loki maps OTLP resource and log-record attributes onto labels and structured metadata. |

Both are served by the `distributor`, `write` and `all` components.

## 2. Shape the payload

Native JSON push:

```json
{
  "streams": [
    {
      "stream": { "service_name": "checkout", "env": "prod" },
      "values": [
        [ "1700000000000000000", "order 41 accepted" ]
      ]
    }
  ]
}
```

Content types accepted: `application/json`, and protobuf (`application/x-protobuf`, usually
snappy-compressed) matching `grpc/grafana-loki-push.proto`.

**The single most common 400:** on `/api/v1/push` the timestamp must be sent as a *string*, not
a JSON number. A number returns 400.

## 3. Respect the label budget before you send

A stream's identity *is* its label set, so every distinct label value creates a stream. Keep
labels low-cardinality (service, environment, cluster) and put high-cardinality detail in
structured metadata instead.

Shipped defaults you must stay inside:

| Limit | Default |
|---|---|
| Labels per stream | 15 |
| Label name length | 1024 bytes |
| Label value length | 2048 bytes |
| Log line length | 256KB |
| Structured metadata per line | 64KB / 128 entries |
| Active streams per tenant | 5000 |
| Ingestion rate | 4 MB/s, 6 MB burst |
| Per-stream rate | 3 MB/s, 15 MB burst |

Every one of these is operator-configurable per tenant, so confirm the real values against the
cluster you are writing to rather than assuming the defaults.

## 4. Set the tenant

Send `X-Scope-OrgID: <tenant>` when the cluster runs with `auth_enabled: true`. On Grafana
Cloud Logs / Grafana Enterprise Logs, use HTTP Basic with the tenant as user and an access
policy token carrying `logs:write` as password.

## 5. Handle the rejection correctly

This is where agents lose data. The distinction is published and it is absolute:

| Status | Class | Sample kept? | Retry the same payload? |
|---|---|---|---|
| 429 | Rate limit (`rate_limited`, `per_stream_rate_limit`, `stream_limit`) | Never accepted | **Yes** — back off and resend |
| 400 | Validation (`line_too_long`, `invalid_labels`, `missing_labels`, `too_far_behind`, `greater_than_max_sample_age`, `too_far_in_future`, `max_label_names_per_series`, `label_name_too_long`, `label_value_too_long`, `duplicate_label_names`, `disallowed_structured_metadata`, `structured_metadata_too_large`, `structured_metadata_too_many`, `missing_enforced_labels`) | **Discarded** | **No** — an identical retry fails identically |
| 413 | `request_body_too_large` | Discarded | No — split the batch |

The full catalogue with the configuration option behind each reason is in
`errors/grafana-loki-problem-types.yml`.

There is no `Retry-After` header and no `X-RateLimit-*` header. Back off on your own schedule.

## 6. Verify

- `GET /ready` on the write target returns `ready` once the ingester has settled.
- `GET /metrics` exposes `loki_discarded_samples_total` and `loki_discarded_bytes_total`,
  labelled with the same `reason` values above. Scrape them: they are the only way to see
  silent discards across a fleet of producers.

## Idempotency

Loki publishes no `Idempotency-Key` header. Re-pushing a byte-identical line with a
byte-identical timestamp to the same stream is absorbed rather than duplicated, because a
stream is keyed on its label set and entries are ordered by timestamp — but that is a storage
property, not a contract, and it does not hold if the line or the timestamp differs by one
nanosecond. Make retries carry the original timestamps.
