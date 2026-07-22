---
id: ext/aws-rds
kind: external
system: cloudledger
name: AWS RDS API
tech: AWS RDS control-plane API
owner: external
tags: [aws, database]
relations: []
---

The AWS RDS control-plane API. Read **read-only** (`rds:Describe*`) by the
[RDS Collector](../components/collector/rds-collector.md) to inventory DB instances and Aurora
clusters. CloudLedger never connects to the databases themselves — only their control-plane
metadata.
