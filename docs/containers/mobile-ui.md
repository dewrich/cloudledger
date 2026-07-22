---
id: cloudledger/mobile-ui
kind: container
system: cloudledger
name: Mobile UI
tech: React Native / Expo / TypeScript
owner: platform-eng
tags: [mobile, react-native, ui]
relations:
  - to: cloudledger/inventory-api
    kind: uses
    desc: Fetches inventory and cost views over HTTPS (intra-system container edge)
    tech: REST / fetch + TanStack Query
  - to: ext/aws-cognito
    kind: uses
    desc: Signs the operator in and obtains the bearer token sent to the Inventory API
    tech: Cognito Hosted UI (OAuth 2.0 / PKCE)
---

# Mobile UI — container (C4 L2)

A React Native (Expo) app that is the first surface on the inventory — the "initially surface
them" phase. Operators sign in with [Cognito](../externals/aws-cognito.md), then browse:

- **Resources** — list/detail for EC2, S3, RDS, Lambda, filterable by account, region, and tag.
- **Cost** — per-service and per-account spend, with a simple trend view.

## Boundary

The app talks **only** to the [Inventory API](../containers/inventory-api.md) — never to AWS
or the database directly. This keeps credentials off the device (the app holds only a
short-lived Cognito token) and means every read goes through the API's authz and pagination.

> This is an intra-system edge (`mobile-ui → inventory-api`), legitimate because both
> containers live in the same system. The producer→consumer *contract* join is for
> **cross-repo** seams; within one repo, containers may reference each other directly.

## Phase note

Phase 1 is read-only browsing. Write actions (tagging policies, ignore-lists, cost budgets)
are explicitly out of scope until the API grows a write surface — see
[the decisions log](../topic-guides/decisions/).

<!-- archdocs:begin -->
## Relations

- **uses** → [cloudledger/inventory-api](inventory-api.md) — Fetches inventory and cost views over HTTPS (intra-system container edge)
- **uses** → [ext/aws-cognito](../externals/aws-cognito.md) — Signs the operator in and obtains the bearer token sent to the Inventory API
<!-- archdocs:end -->
