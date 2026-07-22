---
id: cloudledger
kind: system
name: CloudLedger
system: cloudledger
owner: platform-eng
tags: [aws, inventory, finops, batch]
relations: []          # system relations are derived from its containers' edges
---

# CloudLedger — system (C4 L1 Context)

CloudLedger is the bounded context for **AWS inventory + cost aggregation**. It reads —
never writes — the AWS control-plane and billing APIs, normalizes what it finds into a
Postgres system-of-record, and serves that back to operators through an API and a mobile app.

## Containers

- **[Collector](../containers/collector.md)** — a scheduled Go CLI that sweeps AWS APIs
  account-by-account, region-by-region, and upserts the results. Decomposed into one
  [component](../components/collector/) per AWS service.
- **[Inventory API](../containers/inventory-api.md)** — a Go/Fiber service (an
  OpenAPI-first Go API framework layout) that reads the inventory and serves it as versioned
  REST, described by `api-spec.yml` OpenAPI documents.
- **[Mobile UI](../containers/mobile-ui.md)** — a React Native (Expo) app that browses the
  inventory and cost views.

## What the boundary talks to

The system talks *through* one internal contract and out to several external AWS services:

- **Contract:** [`cloudledger/inventory-db`](../contracts/inventory-db.md) — the Postgres
  system-of-record. The Collector writes it; the Inventory API reads it.
- **AWS externals (read-only):** [EC2](../externals/aws-ec2.md), [S3](../externals/aws-s3.md),
  [RDS](../externals/aws-rds.md), [Lambda](../externals/aws-lambda.md), and
  [Cost Explorer](../externals/aws-cost-explorer.md).
- **Identity external:** [AWS Cognito](../externals/aws-cognito.md) — issues the tokens the
  API validates and the Mobile UI logs in against.

> **POC scope.** This is the initial-phases design: three containers, five AWS collectors,
> one database. It is documentation-first — the design is meant to be reviewed and
> solidified here before any code is written.
