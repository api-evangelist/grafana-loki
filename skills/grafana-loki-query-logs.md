---
name: grafana-loki-query-logs
description: Run a LogQL query against Grafana Loki over a time range, after checking what it will cost.
api: Grafana Loki HTTP API
version: v1
generated: '2026-08-27'
method: generated
source: https://grafana.com/docs/loki/latest/reference/loki-http-api/
operations:
  - GET /loki/api/v1/labels
  - GET /loki/api/v1/label/{name}/values
  - GET /loki/api/v1/format_query
  - GET /loki/api/v1/index/stats
  - GET /loki/api/v1/query_range
  - GET /loki/api/v1/query
---

# Query logs from Grafana Loki

Loki has no cursor pagination and no cost cap. A badly-scoped LogQL query can scan
terabytes before it returns anything. Check the cost first; that is the whole discipline.

## 1. Set the base address and tenant

The base URL is whatever the operator runs Loki on — Loki is self-hosted software and there is
no vendor host. Common forms:

- self-hosted: `http://<loki-host>:3100`
- Grafana Cloud Logs: `https://logs-prod-<cluster>.grafana.net`

If the cluster is multi-tenant, send `X-Scope-OrgID: <tenant>`. On Grafana Cloud Logs use HTTP
Basic with the tenant as the username and an access policy token as the password.

## 2. Discover what you can select on

```
GET /loki/api/v1/labels?start=<ns>&end=<ns>
GET /loki/api/v1/label/{name}/values?start=<ns>&end=<ns>
```

Never guess labels. A selector that matches nothing looks identical to a selector that matched
nothing *in that window*.

## 3. Validate the query without running it

```
GET /loki/api/v1/format_query?query=<logql>
```

This parses and pretty-prints the expression. It does not execute it. Use it to catch a syntax
error before you spend anything.

## 4. Estimate the cost before you run it

```
GET /loki/api/v1/index/stats?query=<selector>&start=<ns>&end=<ns>
```

Returns `streams`, `chunks`, `entries` and `bytes` the selector would touch. This is the same
call the Grafana MCP Loki guardrail makes before it will run `query_loki_logs`; its default
byte budget is 100 GiB and its default range cap is 24h. Apply the same judgment: if `bytes` is
large, narrow the stream selector or the window before proceeding — a broad selector with no
line filter is not capped by `max_query_bytes_read`.

## 5. Run the query

```
GET /loki/api/v1/query_range?query=<logql>&start=<ns>&end=<ns>&limit=<n>&direction=backward
```

- `start`/`end` accept nanosecond Unix epoch, a float epoch with fractional seconds, or
  RFC3339 / RFC3339Nano strings.
- `since` computes `start` from `end` if `start` is omitted; an explicit `start` always wins.
- `limit` is capped by `max_entries_limit_per_query`, default 5000.
- `direction` is `backward` (newest first) by default.
- `step` only applies to metric queries. For a single point in time use
  `GET /loki/api/v1/query` instead.

Add `X-Query-Tags: <tag>` so this specific query is traceable in the server's `metrics.go`
statistics afterwards.

## 6. Read what it cost

Every response carries `data.stats` with `ingester`, `store` and `summary` blocks —
`totalBytesProcessed`, `totalLinesProcessed`, `execTime`, `queueTime`. Record it. It is the
only feedback loop you have, because Loki publishes no rate-limit headers.

## 7. Page by moving the window, not by a cursor

There is no `next` token. To walk a range larger than `limit`, re-query with the boundary moved
to the timestamp of the last entry you received. `logcli --batch` implements exactly this, and
`--parallel-duration` / `--parallel-max-workers` split a long window across workers.

## Failure handling

| Status | Meaning | Retry |
|---|---|---|
| 400 | Malformed LogQL, or a limit exceeded (entries, series, query length) | No — fix the query |
| 401 | No credentials, on a Loki behind auth | No |
| 429 | Tenant or stream rate limit | Yes, after backoff |

Errors on the query surface come back as `{"status":"error","error":"<message>"}`. There is no
`application/problem+json` and no `Retry-After`.
