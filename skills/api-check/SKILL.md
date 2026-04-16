---
name: api-check
description: Validate and integrate external APIs safely
---

## Trigger conditions

Trigger when:
- User asks to integrate a new API or external service
- User wants to test an API endpoint before building on it
- User is switching API providers or updating credentials
- An existing API integration starts returning unexpected results

---

## Step-by-step instructions

### 1. Make a minimal test call

Before writing any integration code, send the simplest possible request to verify the API is reachable and credentials work.

- Use `curl` or a one-off Python script — not integrated code
- Include only the required auth headers and one required parameter
- Confirm you get a 2xx response before proceeding

```python
import requests

resp = requests.get(
    "https://api.example.com/v1/endpoint",
    headers={"Authorization": "Bearer YOUR_KEY"},
    params={"q": "test"},
)
print(resp.status_code, resp.json())
```

### 2. Verify authentication and headers

- Confirm the auth method: API key (header vs query param), Bearer token, OAuth, HMAC
- Check whether the key is scoped (read-only vs read-write) and whether scope matches the use case
- Verify required headers: `Content-Type`, `Accept`, any versioning headers (e.g. `X-API-Version`)
- Test with a deliberately bad key to confirm the API returns a clear 401/403 — not a silent failure

### 3. Inspect response structure

- Log the full raw response on the first call — do not assume field names from docs
- Identify the data envelope (e.g. `response.data`, `response.results`, `response.items`)
- Note nullable fields, optional fields, and any fields with inconsistent types
- Check whether timestamps are Unix epoch, ISO 8601, or something else
- Confirm pagination shape: cursor-based, offset-based, or page-number-based

### 4. Check limits

- **Rate limits**: note the limit (requests/min or requests/day) and which header carries the remaining count (e.g. `X-RateLimit-Remaining`)
- **Pagination**: confirm max page size and whether there is a hard cap on total records returned
- **Quotas**: check if there is a monthly or daily call quota distinct from per-minute rate limits
- **Payload size**: note any max request body size or max response record count

### 5. Validate edge cases

Test each of these before writing integration code:

- Empty result set (query that returns zero records)
- Partial data (request a field that is sometimes missing)
- Error responses: 400 (bad request), 429 (rate limited), 500 (server error)
- What happens at pagination boundary (last page, page beyond last)
- Null or unexpected types in key fields

### 6. Only proceed after API is confirmed

Do not write integration code until steps 1–5 pass. Confirm:
- Auth works
- Response schema matches what the code will consume
- Edge cases are handled or documented
- Rate limits are understood and the integration will stay within them

---

## Example: successful API check output

```
API Check — Serper Search API
------------------------------
[x] Minimal test call: 200 OK
[x] Auth: X-API-Key header, key is read-only scoped
[x] Required headers: Content-Type: application/json
[x] Response envelope: response["organic"] → list of result dicts
[x] Key fields: title (str), link (str), snippet (str) — all present in test response
[x] Pagination: "num" param (max 100), no cursor — offset via "start"
[x] Rate limit: 2,500 searches/month free tier; no per-minute limit found
[x] Empty result: returns {"organic": []} — safe to handle
[x] Error on bad key: 403 {"message": "Invalid API key"} — clear failure
[x] Edge case: "snippet" is absent on some results — must default to ""

Status: PASS — safe to proceed with integration
```

---

## Failure path

If any step above fails, stop and do the following before touching integration code:

1. **Auth failure (401/403)**: re-read the API docs auth section; check key expiry, IP allowlisting, or required scopes. Do not retry more than 2–3 times — escalate or propose an alternative.

2. **Unexpected response schema**: print the full raw response and compare against docs. If docs are wrong or stale, treat the observed schema as ground truth and document the discrepancy.

3. **Rate limit hit during testing**: back off immediately. Calculate whether the integration's expected call volume fits within the quota. If not, propose a caching layer or a different API tier.

4. **Unreliable or intermittent errors (5xx)**: check the API's status page. If the service has a history of instability, flag this to the user and propose an alternative before building on it.

5. **No viable alternative**: document what failed, what was tried, and hand off clearly to the user with a recommended next step (e.g. contact API support, use a different data source).

---

Run with: /api-check
