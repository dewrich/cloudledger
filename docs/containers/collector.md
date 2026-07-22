---
id: cloudledger/collector
kind: container
name: Collector
system: cloudledger
tech: Go 1.23 / AWS SDK for Go v2 / ECS Scheduled Task (EventBridge cron)
owner: platform-eng
tags: [cli, batch, aws]
relations:
  - to: cloudledger/inventory-db
    kind: writes
    desc: Upserts collected resources and cost rows; one transaction per account/region sweep
    tech: pgx / COPY + ON CONFLICT
links:
  dbml: ../reference/schema.dbml
---

# Collector — container (C4 L2)

A single Go binary (`cloudledger collect`) with one subcommand per AWS service. On each
scheduled run it iterates the configured **accounts × regions**, assumes a read-only role in
each account, calls the relevant AWS APIs, normalizes the responses, and **upserts** them into
[`inventory-db`](../contracts/inventory-db.md). It never mutates AWS — every IAM action it
needs is a `Describe*`/`List*`/`Get*`.

## Decomposition (C4 L3)

One [component](../components/collector/) per AWS service, each owning the read edge to its
service's API:

- [EC2 Collector](../components/collector/ec2-collector.md) — instances, volumes, AMIs
- [S3 Collector](../components/collector/s3-collector.md) — buckets, size/object metrics
- [RDS Collector](../components/collector/rds-collector.md) — DB instances and clusters
- [Lambda Collector](../components/collector/lambda-collector.md) — functions and configs
- [Cost Collector](../components/collector/cost-collector.md) — unblended/amortized cost by service

## Responsibilities

- **Fan-out** across accounts and regions with a bounded worker pool.
- **Normalize** heterogeneous AWS shapes into the common `resource` row plus a typed JSONB
  `attributes` blob (see [`schema.dbml`](../reference/schema.dbml)).
- **Reconcile** — a sweep is authoritative for its `(account, region, service)` slice:
  rows absent from the latest sweep are marked `deleted_at` (soft delete), so the inventory
  reflects teardowns, not just creations.

## Failure modes & scaling

- **Throttling.** AWS API rate limits are the primary constraint; the SDK's adaptive retry
  plus a per-service concurrency cap keep sweeps under the limit. A throttled service fails
  *that slice only* — the run is partial, not aborted, and the slice retries next tick.
- **Partial runs are safe.** Because writes are idempotent upserts keyed on ARN, a
  re-run over the same slice converges; there is no "half-applied" state.
- **Scale knob.** Horizontal by account: shard the account list across N task copies. The DB
  write is the only shared resource and it is upsert-only, so shards never conflict.

<!-- archdocs:begin -->
## Relations

- **writes** → [cloudledger/inventory-db](../contracts/inventory-db.md) — Upserts collected resources and cost rows; one transaction per account/region sweep
<!-- archdocs:end -->
