---
generated: '2026-10-07'
method: generated
name: akta-pro-add-missing-company
description: When company search returns nothing, submit the company for addition and track the asynchronous request
  to completion.
api: openapi/akta-pro-openapi.yml
operations:
- companySearch
- createCompanyAdditionRequest
- getRequestStatus
source: Grounded in openapi/akta-pro-openapi.yml (operationIds verified verbatim) and the docs pages cited in conventions/,
  errors/, plans/ and rate-limits/.
---

# Add a company that is missing

When company search returns nothing, submit the company for addition and track the asynchronous request to completion.

## Steps

1. `companySearch` first; only continue when it returns 204/no match.
2. `createCompanyAdditionRequest` - POST /v1/company/addition-requests with `{company_name, website}`. If `already_exists` is true, use the returned `uuid` immediately; otherwise keep `request_id` (status starts `pending`). Free; Subscription/Enterprise only; 100 requests/day on Subscription.
3. `getRequestStatus` - GET /v1/status/{request_id} until `completed` or `failed` (statuses: pending, in_progress, completed, failed). There is no cancel operation. A webhook alternative is mentioned but its configuration guide is a 404.

## Rules

- Authenticate every call with `x-api-key: wk_...` (authentication/akta-pro-authentication.yml).
- On 429 back off exponentially (start 1s, max 60s); retry 401 once after re-authenticating; never retry 400/403/404/422 (errors/akta-pro-error-codes.yml).
- 403 can mean "endpoint not in your plan" as well as forbidden - check rate-limits/akta-pro-rate-limits.yml for plan coverage.
- No idempotency keys exist (conventions/akta-pro-conventions.yml): do not blindly re-POST addition requests.
