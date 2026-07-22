---
id: cloudledger/inventory-api
kind: container
system: cloudledger
name: Inventory API
tech: Go 1.23 / Fiber / oapi-codegen (OpenAPI-first Go API framework)
owner: platform-eng
tags: [http, api, openapi]
relations:
  - to: cloudledger/inventory-db
    kind: reads
    desc: Serves inventory and cost queries; read-only against the system-of-record
    tech: pgx (read replica)
  - to: ext/aws-cognito
    kind: uses
    desc: Validates the caller's bearer token (JWT) against the Cognito JWKS before serving
    tech: Cognito user-pool OIDC (JWKS)
links:
  openapi: ../../api/openapi/resources/v1/api-spec.yml
---

# Inventory API — container (C4 L2)

The read surface over the inventory. A Go/Fiber service built to an **OpenAPI-first Go API
framework** layout: each **domain** owns an `api-spec.yml` OpenAPI document that is the source
of truth for code generation (`oapi-codegen`, Fiber server + models) and Swagger UI. Member
services mount under path prefixes on one domain server.

## API surface — two domains, `api-spec.yml`-first

Follows the `api/openapi/<domain>/v1/api-spec.yml` → `internal/<domain>api/` convention:

| Domain spec | Path prefix | Serves |
|---|---|---|
| [`api/openapi/resources/v1/api-spec.yml`](../../api/openapi/resources/v1/api-spec.yml) | `/resources` | EC2 / S3 / RDS / Lambda inventory, filterable by account, region, tag |
| [`api/openapi/costs/v1/api-spec.yml`](../../api/openapi/costs/v1/api-spec.yml) | `/costs` | Cost rollups by service, account, and day |

The Go packages generated from these specs live under `internal/resourcesapi/` and
`internal/costsapi/` (the `internal/<domain>api/` convention; **not implemented in this
POC** — the specs are the reviewable design artifact).

## Responsibilities

- **Read-only.** The API never writes inventory; it queries a read replica of
  [`inventory-db`](../contracts/inventory-db.md). The Collector is the sole writer.
- **AuthN/Z.** Every request carries a Cognito-issued bearer token; the API validates it
  against the [Cognito](../externals/aws-cognito.md) JWKS and enforces scope-per-operation
  (e.g. `resources:view`, `costs:view`) via `bearerAuth` on each operation.
- **Pagination + filtering** are pushed to SQL; large result sets are keyset-paginated.

## Failure modes

- **DB replica lag.** Reads may trail the latest sweep by seconds; responses carry the
  `collected_at` of the underlying rows so clients can reason about freshness.
- **Cognito unreachable** → `503` (fail closed; never serve inventory unauthenticated).

<!-- archdocs:begin -->
## Relations

- **reads** → [cloudledger/inventory-db](../contracts/inventory-db.md) — Serves inventory and cost queries; read-only against the system-of-record
- **uses** → [ext/aws-cognito](../externals/aws-cognito.md) — Validates the caller's bearer token (JWT) against the Cognito JWKS before serving
<!-- archdocs:end -->
