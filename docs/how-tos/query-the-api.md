# Query the Inventory API

Exercise the read surface once a collector has populated the DB. The API is described by the
OpenAPI domain specs — [resources](../../api/openapi/resources/v1/api-spec.yml) and
[costs](../../api/openapi/costs/v1/api-spec.yml). Every call needs a Cognito bearer token.

```bash
export TOKEN=... # a Cognito-issued JWT with resources:view / costs:view scopes
BASE=http://localhost:9080
```

**List EC2 resources in one account/region:**

```bash
curl -s -H "Authorization: Bearer $TOKEN" \
  "$BASE/resources/resources?service=ec2&region=us-east-1&page_size=50" | jq
```

**Fetch one resource by ARN** (URL-encode the ARN):

```bash
curl -s -H "Authorization: Bearer $TOKEN" \
  "$BASE/resources/resources/arn%3Aaws%3Aec2%3Aus-east-1%3A123456789012%3Ainstance%2Fi-0abc" | jq
```

**Spend by service, last 30 days** (costs domain, mounted at `/costs`):

```bash
curl -s -H "Authorization: Bearer $TOKEN" \
  "http://localhost:9081/costs/rollups/by-service" | jq
```

Responses carry `collected_at` so a client can tell how fresh the underlying sweep is — see
[the API container](../containers/inventory-api.md) for the freshness contract.
