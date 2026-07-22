---
id: ext/aws-lambda
kind: external
system: cloudledger
name: AWS Lambda API
tech: AWS Lambda control-plane API
owner: external
tags: [aws, compute, serverless]
relations: []
---

The AWS Lambda control-plane API. Read **read-only** (`lambda:List*`/`Get*`) by the
[Lambda Collector](../components/collector/lambda-collector.md) to inventory functions and their
configuration. Function code is never fetched.
