# 0003 — CLI collectors over a central daemon

## Context

The aggregation tier can be built as either (a) a set of **CLI tools** run on a schedule, or
(b) a long-running **daemon/service** that polls AWS continuously. We must also decide the
blast radius of the AWS credentials CloudLedger holds.

## Decision

Build the aggregation tier as a **single Go CLI** (`cloudledger collect`) with one subcommand
per AWS service, run as a scheduled batch job (ECS Scheduled Task via EventBridge cron). It
assumes a **read-only** role per account and performs only `Describe*`/`List*`/`Get*` calls.

## Consequences

- **Operationally simple** — no always-on process to keep healthy; a failed run just retries
  next tick, and partial runs are safe because writes are idempotent upserts.
- **Least privilege** — read-only IAM means CloudLedger can never mutate a customer resource,
  which bounds the blast radius and simplifies the security review.
- **Composable & testable** — each subcommand runs standalone locally (see
  [run-locally](../../how-tos/run-locally.md)) and shards trivially across accounts.
- Trade-off: inventory freshness is bounded by the schedule (minutes), not real-time. Acceptable
  for an inventory/FinOps use case; event-driven freshness (EventBridge → per-resource updates)
  is a later phase if needed.

## Status

Accepted
