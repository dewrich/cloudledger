---
id: ext/aws-ec2
kind: external
system: cloudledger
name: AWS EC2 API
tech: AWS EC2 control-plane API
owner: external
tags: [aws, compute]
relations: []
---

The AWS EC2 control-plane API. CloudLedger reads it **read-only** (`ec2:Describe*`) to inventory
instances, EBS volumes, and AMIs. Outside our control; modelled as a boundary node so the
[EC2 Collector](../components/collector/ec2-collector.md)'s `reads` edge has somewhere to point.
