---
id: ext/aws-cost-explorer
kind: external
system: cloudledger
name: AWS Cost Explorer
tech: AWS Cost Explorer API (Cost & Usage data)
owner: external
tags: [aws, finops, cost]
relations: []
---

The AWS Cost Explorer API. Read **read-only** (`ce:GetCostAndUsage`) by the
[Cost Collector](../components/collector/cost-collector.md) for daily spend grouped by service
and linked account. A *global* endpoint (us-east-1), so it is swept once per account rather than
per region.
