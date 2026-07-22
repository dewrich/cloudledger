# 0004 — OpenAPI-first Go API framework

## Context

The Inventory API needs a consistent, code-generated contract so the mobile client (and any
later clients) stay in lockstep with the server, and so new domains can be added uniformly.
The team standard is an **`api-spec.yml`-first Go API framework**: OpenAPI is the source of
truth, `oapi-codegen` emits the Fiber server + models, and member services mount under path
prefixes on one domain server.

## Decision

Build the [Inventory API](../../containers/inventory-api.md) to that framework's layout:
`api/openapi/<domain>/v1/api-spec.yml` is the source of truth; generated code lives in
`internal/<domain>api/`. Start with two domains — **resources** and **costs** — mounted at
`/resources` and `/costs`.

## Consequences

- **Spec-first** — the [resources](../../api/openapi/resources/v1/api-spec.yml) and
  [costs](../../api/openapi/costs/v1/api-spec.yml) specs are the reviewable artifact now, and
  become the codegen input later; server and client cannot drift from them.
- **Consistency with the org** — same tooling, gates (`redocly lint`, `oasdiff` for breaking
  changes), and idioms as the rest of our Go services, so no new framework to learn.
- **Uniform growth** — a new domain is a new `api-spec.yml` + a mount.
- Trade-off: couples us to the framework's conventions and Fiber. Accepted — the org standard
  outweighs framework independence for a POC.

## Status

Accepted
