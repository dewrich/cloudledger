---
id: ext/aws-cognito
kind: external
system: cloudledger
name: AWS Cognito
tech: Amazon Cognito user pool (OIDC / OAuth 2.0)
owner: external
tags: [aws, auth, identity]
relations: []
---

Amazon Cognito — the identity provider for CloudLedger's human operators. The
[Mobile UI](../containers/mobile-ui.md) signs in against the Cognito Hosted UI and receives a
bearer token; the [Inventory API](../containers/inventory-api.md) validates that token against
the Cognito JWKS and enforces per-operation scopes. Outside our control; modelled as a boundary
node so the `uses` edges have somewhere to point.
