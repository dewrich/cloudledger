# 0005 — React Native for the first UI

## Context

Phase 1 needs to "initially surface" the inventory to operators. The surface should be quick to
stand up, reach both iOS and Android, and talk only to the API (no AWS or DB access from the
client). Options: a React web app, native per-platform apps, or React Native.

## Decision

Ship the first surface as a **React Native (Expo)** app — the
[Mobile UI](../../containers/mobile-ui.md). It authenticates via the
[Cognito](../../externals/aws-cognito.md) Hosted UI (OAuth 2.0 / PKCE) and reads exclusively
through the [Inventory API](../../containers/inventory-api.md).

## Consequences

- **One codebase, two platforms** — iOS + Android from a single TypeScript app; Expo removes
  most native-build friction for a POC.
- **Credentials stay off the device** — the app holds only a short-lived Cognito token; every
  read is authorized and paginated by the API.
- Phase 1 is **read-only browsing**; write actions wait for the API to grow a write surface.
- Trade-off: a mobile-first choice deprioritizes a desktop/web console. Revisit if operators
  want a large-screen dashboard; the API is client-agnostic, so a web client is additive.

## Status

Accepted
