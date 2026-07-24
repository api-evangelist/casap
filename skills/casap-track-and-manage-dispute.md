---
name: Track and manage a dispute
description: List disputes, check a dispute's status, upload evidence, and reopen a closed dispute.
api: openapi/casap-openapi.json
operations: [Disputes_GetDisputes, Disputes_GetDisputeStatus, Disputes_UploadFile, Disputes_ReopenDispute]
---

# Track and manage a dispute

## Auth
Send `Authorization: <api_key>` and `x-user-uuid: <user_uuid>` on every request.

## Steps
1. **Find disputes** — `GET /v1/disputes` (`Disputes_GetDisputes`). Page with `page` and `size`; filter with `customerUuid` or `shortId`. The response has `items[]`, `totalItems`, `totalPages`, `currentPage`.
2. **Check status** — `GET /v1/dispute/status/{disputeUuid}` (`Disputes_GetDisputeStatus`) returns the current DisputeStatus plus transaction/audit-log detail.
3. **Add evidence** — `POST /v1/disputes/{disputeUuid}/files` (`Disputes_UploadFile`) to attach a supporting file (application/json body) to the dispute.
4. **Reopen** — `POST /v1/disputes/{disputeUuid}/reopen` (`Disputes_ReopenDispute`) if a resolved dispute needs to be re-examined.

## Rules
- Reference a dispute by its opaque `uuid` in path calls; `shortId` is only for human display and list filtering.
- Errors return a google.rpc.Status body ({code, message, details}); see errors/casap-problem-types.yml.
- Paginate rather than requesting an unbounded list.
