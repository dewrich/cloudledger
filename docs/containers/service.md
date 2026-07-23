---
id: cloudledger/service
kind: container
name: Service
system: cloudledger
tech: TODO
owner: TODO
tags: []
relations: []
  # Point at a CONTRACT node (a topic/db/bucket), never a peer service —
  # the graph builder joins producers to consumers. Example:
  # - to: platform/some-topic
  #   kind: publishes
  #   desc: Emits SomethingHappened
  #   tech: boto3
---

# Service — container (C4 L2)

TODO: responsibilities, failure modes, scaling notes.
