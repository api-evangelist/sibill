---
name: Issue and send an electronic invoice
description: Create an electronic invoice for a company, submit it to the Italian SDI, and email a copy to the counterpart.
api: openapi/sibill-openapi-original.json
operations:
  - SibillWeb.Integration.V1.CompaniesController.index
  - SibillWeb.Integration.V1.CounterpartsActionsController.search
  - SibillWeb.Integration.V1.DocumentsActionsController.create_invoice
  - SibillWeb.Integration.V1.DocumentsActionsController.get_invoice
  - SibillWeb.Integration.V1.DocumentsActionsController.share_invoice
---

# Issue and send an electronic invoice

Use the Sibill Integration API to raise an electronic invoice (fattura elettronica), submit it to the Agenzia delle Entrate SDI, and email a copy.

## Auth & base
- Send `Authorization: Bearer ${api_key}` on every request, HTTPS only.
- Production base: `https://integration.sibill.com/api/v1` (sandbox: `https://integration.dev.sibill.com/api/v1`).
- Stay under 10 requests/second (429 otherwise).

## Steps
1. **Pick the company.** `GET /companies` (`SibillWeb.Integration.V1.CompaniesController.index`) and choose the `company_id` you are invoicing from.
2. **Resolve the counterpart.** `GET /counterparts/search` (`SibillWeb.Integration.V1.CounterpartsActionsController.search`) by VAT number to find or confirm the customer.
3. **Create the invoice.** `POST /companies/{company_id}/documents/invoice` (`SibillWeb.Integration.V1.DocumentsActionsController.create_invoice`) — this creates the document, submits it to SDI, and reconciles it. Capture the returned document id.
4. **Confirm submission.** `GET /companies/{company_id}/documents/{document_id}/invoice` (`SibillWeb.Integration.V1.DocumentsActionsController.get_invoice`) to read the invoice/SDI status.
5. **Email a copy.** `POST /companies/{company_id}/documents/{document_id}/share-invoice` (`SibillWeb.Integration.V1.DocumentsActionsController.share_invoice`).

## Rules
- Errors come back as the `IntegrationError` envelope (`errors[]` with `code`/`detail`/`source.pointer`) — read `source.pointer` to fix a `422`.
- There is no idempotency key; do not blindly retry `create_invoice` on a timeout — re-check with `list documents` first.
- Subscribe to the `document.updated` webhook to track SDI/reconciliation state asynchronously.
