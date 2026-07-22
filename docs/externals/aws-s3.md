---
id: ext/aws-s3
kind: external
system: cloudledger
name: AWS S3 API
tech: AWS S3 API + CloudWatch metrics
owner: external
tags: [aws, storage]
relations: []
---

The AWS S3 API (plus CloudWatch for size/object metrics). Read **read-only** by the
[S3 Collector](../components/collector/s3-collector.md) to inventory buckets and their
configuration. Object *contents* are never read — only bucket metadata and CloudWatch
aggregates.
