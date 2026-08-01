---
name: Reconcile a bank transaction to a payment flow
description: Match an incoming bank transaction against a document's payment flow to reconcile it.
api: openapi/sibill-openapi-original.json
operations:
  - SibillWeb.Integration.V1.AccountsController.index
  - SibillWeb.Integration.V1.TransactionsController.index
  - SibillWeb.Integration.V1.DocumentsController.index
  - SibillWeb.Integration.V1.FlowsController.index
  - SibillWeb.Integration.V1.ReconciliationsController.create
---

# Reconcile a bank transaction to a payment flow

Match a bank movement to the payment flow of an invoice/bill so cash flow stays accurate.

## Auth & base
- `Authorization: Bearer ${api_key}`, HTTPS, `https://integration.sibill.com/api/v1`.
- Everything is scoped to `company_id`.

## Steps
1. **List accounts** for the company: `GET /companies/{company_id}/accounts` (`SibillWeb.Integration.V1.AccountsController.index`).
2. **Find the unreconciled transaction:** `GET /companies/{company_id}/transactions` (`SibillWeb.Integration.V1.TransactionsController.index`) — page with `cursor`/`page_size`, and use `filter`/`sort` to narrow to the account and date.
3. **Locate the target document and its flow:** `GET /companies/{company_id}/documents` (`SibillWeb.Integration.V1.DocumentsController.index`), then `GET /companies/{company_id}/documents/{document_id}/flows` (`SibillWeb.Integration.V1.FlowsController.index`) to get the flow id.
4. **Create the reconciliation:** `POST /companies/{company_id}/reconciliations` (`SibillWeb.Integration.V1.ReconciliationsController.create`) linking the transaction id and the flow id.

## Rules
- A `Reconciliation` references a `Flow` (`flow_id`) and a `Transaction` (`transaction_id`); supply both.
- Use `expand` to inline related resources where supported instead of extra round-trips.
- Watch the `flow.updated` webhook to confirm the flow moved to a reconciled state.
