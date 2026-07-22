# CloudLedger

CloudLedger is an **AWS resource-and-cost aggregator**: a set of CLI collectors sweep the
compute, storage, and billing APIs across every account and region, land a normalized
inventory in Postgres, and a Go API surfaces it to a React Native app. It owns the
*inventory of record* — one queryable place that answers "what do we run, where, and what
does it cost" — but it is strictly read-only toward AWS and never mutates a customer resource.
It is run by the platform team as a scheduled batch job plus an always-on API.

Architecture (C4) is the primary axis: [context/](context/index.md) is the system,
[containers/](containers/) the deployable units (the [Collector](containers/collector.md)
CLI, the [Inventory API](containers/inventory-api.md), and the
[Mobile UI](containers/mobile-ui.md)), and [components/](components/collector/) decomposes the
Collector. New here? Read
[How Our Documentation is Organized](reference/how-our-documentation-is-organized/index.md)
for the C4 × Divio scheme these docs follow.
