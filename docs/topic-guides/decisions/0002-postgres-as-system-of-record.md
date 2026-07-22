# 0002 — Postgres as the inventory system-of-record

## Context

CloudLedger aggregates heterogeneous AWS resources (EC2, S3, RDS, Lambda, …) plus a cost time
series. The store must: answer flexible filter/rollup queries for the API; absorb new services
without churn; and be the single authoritative "what exists" surface. Candidates weighed:
Postgres, DynamoDB, and a search index (OpenSearch).

## Decision

Use a single **PostgreSQL** database as the system-of-record. Model a **common `resource` row**
(the columns every service shares) plus a JSONB `attributes` blob for service-specific shape,
and a separate `cost_daily` time-series table. The [Collector](../../containers/collector.md)
is the sole writer via idempotent upserts keyed on ARN; the
[Inventory API](../../containers/inventory-api.md) reads a replica.

## Consequences

- **New services are additive** — a new collector adds a `service` value and fills `attributes`;
  no migration. This is the property [add-a-collector](../../how-tos/add-a-collector.md) relies on.
- Rich SQL filtering/rollups for the API for free; JSONB is queryable when a service-specific
  field must be indexed.
- Trade-off: JSONB is schemaless, so per-service field validation lives in the collector, not
  the DB. Accepted — the collectors own normalization anyway.
- A relational store won't scale to millions of resources as cheaply as DynamoDB, but the POC's
  scale (tens of accounts) is far under that ceiling; revisit if it changes.

## Status

Accepted
