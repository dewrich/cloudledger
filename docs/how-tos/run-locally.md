# Run a collector locally

> **POC / design-phase note.** No code exists yet — this is the *intended* developer
> workflow, written now so the design can be reviewed against a concrete "day one"
> experience. Paths and commands describe the target, not a shipped binary.

The shortest path to a full local loop: Postgres → one collector sweep → query it back.

## 1. Start Postgres

```bash
docker run --rm -d --name cloudledger-db \
  -e POSTGRES_PASSWORD=dev -e POSTGRES_DB=cloudledger \
  -p 5432:5432 postgres:16
# apply the schema (dbml2sql docs/reference/schema.dbml > schema.sql)
psql "postgres://postgres:dev@localhost:5432/cloudledger" -f schema.sql
```

## 2. Point at an AWS account (read-only)

The Collector assumes a **read-only** role per account. Locally, export a profile whose
credentials can `Describe*`/`List*`/`GetCostAndUsage` — nothing more.

```bash
export AWS_PROFILE=cloudledger-ro
export CLOUDLEDGER_DSN="postgres://postgres:dev@localhost:5432/cloudledger"
```

## 3. Run one collector

```bash
# sweep just EC2 in one region into the local DB
cloudledger collect ec2 --region us-east-1

# or the cost sweep (global endpoint, per-account)
cloudledger collect cost
```

## 4. Verify

```bash
psql "$CLOUDLEDGER_DSN" -c \
  "select service, count(*) from resource where deleted_at is null group by 1;"
```

See [add an AWS collector](add-a-collector.md) to extend coverage, and
[query the API](query-the-api.md) to exercise the read surface.
