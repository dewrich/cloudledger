---
id: cloudledger/collector/lambda-collector
kind: component
system: cloudledger
parent: cloudledger/collector
name: Lambda Collector
tech: Go / AWS SDK for Go v2 (lambda)
owner: platform-eng
tags: [aws, compute, serverless]
relations:
  - to: ext/aws-lambda
    kind: reads
    desc: Paginates ListFunctions and reads per-function configuration per region
    tech: lambda:List*/Get* (read-only)
---

# Lambda Collector — component (C4 L3)

A component *inside* the [Collector](../../containers/collector.md) container. Lists functions
per region and captures runtime, memory, timeout, package type, last-modified, and tags. Does
not fetch code — configuration and metadata only. Emits `resource` rows with
`service = 'lambda'`.

<!-- archdocs:begin -->
## Relations

- **reads** → [ext/aws-lambda](../../externals/aws-lambda.md) — Paginates ListFunctions and reads per-function configuration per region
<!-- archdocs:end -->
