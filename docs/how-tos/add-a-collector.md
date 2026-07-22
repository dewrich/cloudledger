# Add an AWS collector

How to extend CloudLedger to a new AWS service (say, DynamoDB). The design goal is that this
is **additive** — no schema migration, no change to the API's core.

1. **Add a component doc.** Create `docs/components/collector/dynamodb-collector.md` with
   `kind: component`, `parent: cloudledger/collector`, and a `reads` relation to a new
   `ext/aws-dynamodb` external node (add `docs/externals/aws-dynamodb.md`, `kind: external`).
   Run `archdocs validate .` — the graph must stay green.
2. **Emit the common shape.** The collector maps each DynamoDB table to a `resource` row with
   `service = 'dynamodb'`, the ARN as the natural key, and the table's config in the JSONB
   `attributes` blob. No new column, no migration — this is the point of the
   [common-row design](../topic-guides/decisions/0002-postgres-as-system-of-record.md).
3. **Grant the IAM read.** Add `dynamodb:List*`/`Describe*` to the read-only role the
   Collector assumes. Read-only only — see
   [ADR 0003](../topic-guides/decisions/0003-cli-collectors-over-central-daemon.md).
4. **Surface it.** Add `dynamodb` to the `service` enum in
   [`api/openapi/resources/v1/api-spec.yml`](../../api/openapi/resources/v1/api-spec.yml) and
   regenerate (`make gen ACTION=api`). The UI picks it up from the enum.

The whole change is one component + one external + one enum value. That "cost of a new
service" is the metric the architecture is optimized for.
