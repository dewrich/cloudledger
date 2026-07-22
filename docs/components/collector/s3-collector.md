---
id: cloudledger/collector/s3-collector
kind: component
system: cloudledger
parent: cloudledger/collector
name: S3 Collector
tech: Go / AWS SDK for Go v2 (s3, cloudwatch)
owner: platform-eng
tags: [aws, storage]
relations:
  - to: ext/aws-s3
    kind: reads
    desc: Lists buckets, resolves region/encryption/policy; pulls size & object count from CloudWatch
    tech: s3:List*/Get* + cloudwatch:GetMetricData (read-only)
---

# S3 Collector — component (C4 L3)

A component *inside* the [Collector](../../containers/collector.md) container. Enumerates
buckets (a global list, then per-bucket region, encryption, public-access, and lifecycle
config), and reads `BucketSizeBytes` / `NumberOfObjects` from CloudWatch daily metrics rather
than listing objects (which would be prohibitively expensive). Emits one `resource` row per
bucket (`service = 's3'`).

<!-- archdocs:begin -->
## Relations

- **reads** → [ext/aws-s3](../../externals/aws-s3.md) — Lists buckets, resolves region/encryption/policy; pulls size & object count from CloudWatch
<!-- archdocs:end -->
