---
generated: '2026-10-07'
method: generated
name: akta-pro-resolve-and-enrich-company
description: Resolve a company name or website to its akta.pro uuid, then pull only the Company Data sections you
  need.
api: openapi/akta-pro-openapi.yml
operations:
- companySearch
- companyEnrichment
source: Grounded in openapi/akta-pro-openapi.yml (operationIds verified verbatim) and the docs pages cited in conventions/,
  errors/, plans/ and rate-limits/.
---

# Resolve and enrich a company

Resolve a company name or website to its akta.pro uuid, then pull only the Company Data sections you need.

## Steps

1. `companySearch` - GET /v1/company/search?query=<name or website>. Take `data[0].uuid` (and `website`, `company_status`). A 204 means no match: fall through to the add-missing-company skill.
2. `companyEnrichment` - GET /v1/company/enrichment?company=<uuid>&sections=<comma list>. Choose sections explicitly (firmographic, business_model, financial_estimate, location, management_profile, technology ...); there is no "all". Each section is billed separately (see plans/).
3. Read `credits_consumed` from the response and stop if the budget is spent. `funding_detail` and `mna_and_investment` are silently skipped on Pay-as-you-go: do not treat their absence as an error.

## Rules

- Authenticate every call with `x-api-key: wk_...` (authentication/akta-pro-authentication.yml).
- On 429 back off exponentially (start 1s, max 60s); retry 401 once after re-authenticating; never retry 400/403/404/422 (errors/akta-pro-error-codes.yml).
- 403 can mean "endpoint not in your plan" as well as forbidden - check rate-limits/akta-pro-rate-limits.yml for plan coverage.
- No idempotency keys exist (conventions/akta-pro-conventions.yml): do not blindly re-POST addition requests.
