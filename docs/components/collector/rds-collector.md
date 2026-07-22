---
id: cloudledger/collector/rds-collector
kind: component
system: cloudledger
parent: cloudledger/collector
name: RDS Collector
tech: Go / AWS SDK for Go v2 (rds)
owner: platform-eng
tags: [aws, database]
relations:
  - to: ext/aws-rds
    kind: reads
    desc: Paginates DescribeDBInstances / DescribeDBClusters per region
    tech: rds:Describe* (read-only)
---

# RDS Collector — component (C4 L3)

A component *inside* the [Collector](../../containers/collector.md) container. Sweeps RDS DB
instances and Aurora clusters per region: engine and version, instance class, storage,
Multi-AZ, and tags. Instances belonging to a cluster carry the cluster ARN so the API can
group them. Emits `resource` rows with `service = 'rds'`.

<!-- archdocs:begin -->
## Relations

- **reads** → [ext/aws-rds](../../externals/aws-rds.md) — Paginates DescribeDBInstances / DescribeDBClusters per region
<!-- archdocs:end -->
