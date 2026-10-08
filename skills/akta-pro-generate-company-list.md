---
generated: '2026-10-07'
method: generated
name: akta-pro-generate-company-list
description: Turn a natural-language target description into a reviewable structured filter, then generate and optionally
  enrich the list.
api: openapi/akta-pro-openapi.yml
operations:
- translateQuery
- generateCompanyList
source: Grounded in openapi/akta-pro-openapi.yml (operationIds verified verbatim) and the docs pages cited in conventions/,
  errors/, plans/ and rate-limits/.
---

# Generate a targeted company list

Turn a natural-language target description into a reviewable structured filter, then generate and optionally enrich the list.

## Steps

1. `translateQuery` - POST /v1/list/filter-builder/ with the natural-language description. It returns a structured `filters` object you can inspect, tweak or cache (2.5 credits; the same surcharge List Generation charges for raw `query`).
2. `generateCompanyList` - POST /v1/list/generate/companies/ with `filters` (not `query`), optional enrichment sections and a sort (revenue, valuation, headcount, total_funding, founded_year). Subscription and Enterprise plans only.
3. Budget before you run: total credits = 1.5 + 0.2 x companies + (section cost x companies). Re-run with the cached `filters` instead of re-translating.

## Rules

- Authenticate every call with `x-api-key: wk_...` (authentication/akta-pro-authentication.yml).
- On 429 back off exponentially (start 1s, max 60s); retry 401 once after re-authenticating; never retry 400/403/404/422 (errors/akta-pro-error-codes.yml).
- 403 can mean "endpoint not in your plan" as well as forbidden - check rate-limits/akta-pro-rate-limits.yml for plan coverage.
- No idempotency keys exist (conventions/akta-pro-conventions.yml): do not blindly re-POST addition requests.
