---
id: cloudledger/collector/ec2-collector
kind: component
system: cloudledger
parent: cloudledger/collector
name: EC2 Collector
tech: Go / AWS SDK for Go v2 (ec2)
owner: platform-eng
tags: [aws, compute]
relations:
  - to: ext/aws-ec2
    kind: reads
    desc: Paginates DescribeInstances / DescribeVolumes / DescribeImages per region
    tech: ec2:Describe* (read-only)
---

# EC2 Collector — component (C4 L3)

A component *inside* the [Collector](../../containers/collector.md) container. Sweeps EC2 in
each region: instances (type, state, launch time, tags), attached EBS volumes, and owned AMIs.
Normalizes each into a `resource` row (`service = 'ec2'`) with the raw shape preserved in the
JSONB `attributes` column. The DB write is owned by the parent container's single upsert path.

<!-- archdocs:begin -->
## Relations

- **reads** → [ext/aws-ec2](../../externals/aws-ec2.md) — Paginates DescribeInstances / DescribeVolumes / DescribeImages per region
<!-- archdocs:end -->
