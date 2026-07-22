---
id: cloudledger/collector/cost-collector
kind: component
system: cloudledger
parent: cloudledger/collector
name: Cost Collector
tech: Go / AWS SDK for Go v2 (costexplorer)
owner: platform-eng
tags: [aws, finops, cost]
relations:
  - to: ext/aws-cost-explorer
    kind: reads
    desc: Pulls GetCostAndUsage grouped by SERVICE and LINKED_ACCOUNT, daily granularity
    tech: ce:GetCostAndUsage (read-only)
---

# Cost Collector — component (C4 L3)

A component *inside* the [Collector](../../containers/collector.md) container, and the one
non-resource sweep. Calls Cost Explorer `GetCostAndUsage` at **daily** granularity, grouped by
`SERVICE` and `LINKED_ACCOUNT`, capturing both **unblended** and **amortized** amounts. Writes
`cost_daily` rows (not `resource` rows) — a distinct table keyed on
`(account, service, usage_date)` — so the [Costs API domain](../../containers/inventory-api.md)
can roll spend up by service, account, and day.

**Note:** Cost Explorer is a *global* (us-east-1) endpoint, so this collector runs once per
account, not once per region — unlike the resource collectors.

<!-- archdocs:begin -->
## Relations

- **reads** → [ext/aws-cost-explorer](../../externals/aws-cost-explorer.md) — Pulls GetCostAndUsage grouped by SERVICE and LINKED_ACCOUNT, daily granularity
<!-- archdocs:end -->
