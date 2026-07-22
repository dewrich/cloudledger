---
id: cloudledger/inventory-db
kind: contract
subkind: db
system: cloudledger
name: Inventory DB
tech: PostgreSQL 16 / Amazon RDS (Multi-AZ, one read replica)
owner: platform-eng
tags: [postgres, system-of-record]
relations: []          # contract nodes are passive join points; no outbound edges
links:
  dbml: ../reference/schema.dbml
---

# Inventory DB — contract (Postgres system-of-record)

The single system-of-record for CloudLedger. The [Collector](../containers/collector.md)
container is the **sole writer** (idempotent upserts); the
[Inventory API](../containers/inventory-api.md) reads a **replica**. No other system touches it.

**Repo-local contract (G4).** Exactly one service (CloudLedger) owns and touches this store, so
it is declared here under the system's own namespace (`cloudledger/inventory-db`) rather than in
a shared `platform-docs`. If a second system ever needed to read this inventory directly, we
would promote it to `platform/inventory-db`; today the API is the only sanctioned read path, so
it stays local.

## Shape

Two core tables (full DDL in [`schema.dbml`](../reference/schema.dbml)):

| Table | Grain | Written by |
|---|---|---|
| `resource` | one row per AWS resource, keyed on `arn` | EC2 / S3 / RDS / Lambda collectors |
| `cost_daily` | one row per `(account, service, usage_date)` | Cost collector |

`resource` carries the common columns every service shares (account, region, service, name,
tags, `collected_at`, `deleted_at`) plus a JSONB `attributes` blob for the service-specific
shape — so a new collector adds a `service` value without a schema migration.

**Consistency contract:** a sweep is authoritative for its `(account, region, service)` slice.
Rows present last sweep but absent this one get `deleted_at = now()` (soft delete). Readers
filter `deleted_at IS NULL` for the live inventory.
