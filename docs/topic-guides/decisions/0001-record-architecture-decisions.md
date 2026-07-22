# 0001 — Record architecture decisions

## Context

CloudLedger is being designed documentation-first: the design must be reviewable and
solidified before any code exists. Decisions made now (store, collector shape, API framework,
UI stack) will be hard to reverse once code lands, and their *rationale* is exactly what a
reviewer six months from now will lack.

## Decision

We record architecturally significant decisions as **MADR** records, trimmed to four
headings (Context, Decision, Consequences, Status), kept in
`docs/topic-guides/decisions/` in the repo they affect. One decision per file, numbered.

## Consequences

- The "why" lives next to the code and diffs in PRs, so drift between decision and
  implementation is visible.
- A small tax on each significant change (write the ADR) in exchange for durable rationale.

## Status

Accepted
