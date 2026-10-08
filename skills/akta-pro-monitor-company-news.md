---
generated: '2026-10-07'
method: generated
name: akta-pro-monitor-company-news
description: Pull entity-resolved, de-duplicated news for a company, an industry code or an open-ended query.
api: openapi/akta-pro-openapi.yml
operations:
- companySearch
- industrySearch
- getNews
source: Grounded in openapi/akta-pro-openapi.yml (operationIds verified verbatim) and the docs pages cited in conventions/,
  errors/, plans/ and rate-limits/.
---

# Monitor company or industry news

Pull entity-resolved, de-duplicated news for a company, an industry code or an open-ended query.

## Steps

1. Resolve the anchor: `companySearch` for a company uuid, or `industrySearch` (GET /v1/industry/search?query=...) for industry codes. Since July 9, 2026 the `industry` parameter of getNews expects resolved codes, not free text.
2. `getNews` - GET /v1/news with one of `company`, `primary_company`, `industry`, `query` or `title`, plus `start_date`/`end_date`, `limit`/`offset` and `group_articles=true` for event-level de-duplication. A 204 means no articles.
3. Page with `offset`; cost is 0.1 credits per call plus 0.01 per article returned, so keep `limit` tight and filter with `news_score_list` / `countries` rather than over-fetching.

## Rules

- Authenticate every call with `x-api-key: wk_...` (authentication/akta-pro-authentication.yml).
- On 429 back off exponentially (start 1s, max 60s); retry 401 once after re-authenticating; never retry 400/403/404/422 (errors/akta-pro-error-codes.yml).
- 403 can mean "endpoint not in your plan" as well as forbidden - check rate-limits/akta-pro-rate-limits.yml for plan coverage.
- No idempotency keys exist (conventions/akta-pro-conventions.yml): do not blindly re-POST addition requests.
