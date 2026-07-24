---
name: Create a payment dispute
description: File a new chargeback/dispute with Casap for one or more disputed transactions.
api: openapi/casap-openapi.json
operations: [Auth_Authenticate, Disputes_Create, Disputes_GetDisputeStatus]
---

# Create a payment dispute

Use the Casap API to open a dispute (chargeback) on behalf of a customer.

## Auth
Every request needs two headers together:
- `Authorization: <api_key>`
- `x-user-uuid: <user_uuid>`

Optionally call `GET /v1/authenticate` (`Auth_Authenticate`) first to validate credentials.

## Steps
1. Build the request body for `POST /v1/disputes` (`Disputes_Create`):
   - `orgUuid` — your organization UUID.
   - `transactionDetails[]` — the disputed transaction(s): `amount` ({amountInCents, currency}), `merchant`, `settledTimestamp`, `network`, `arn`, `cardType`, etc.
   - `customer` — {uuid, name, email, phone, accountUuid, address}.
   - `reason` — the DisputeReason.
   - `questionnaireAnswers[]` — answers Casap needs to substantiate the claim.
2. Send the request. The response returns the created dispute (`uuid` + human-friendly `shortId`).
3. Poll `GET /v1/dispute/status/{disputeUuid}` (`Disputes_GetDisputeStatus`) to track progress.

## Rules
- Errors come back as a google.rpc.Status body ({code, message, details}); map `code` to HTTP semantics via errors/casap-problem-types.yml.
- No idempotency key is supported — do not blindly retry a create; check for an existing dispute (via `shortId`/`customerUuid` on `Disputes_GetDisputes`) before recreating.
- Monetary amounts are integer cents (`amountInCents`) with an explicit `currency`.
