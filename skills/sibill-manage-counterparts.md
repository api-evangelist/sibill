---
name: Manage counterparts (customers and suppliers)
description: Look up a counterpart by VAT, create it if missing, and keep its details current.
api: openapi/sibill-openapi-original.json
operations:
  - SibillWeb.Integration.V1.CounterpartsActionsController.search
  - SibillWeb.Integration.V1.CounterpartsController.index
  - SibillWeb.Integration.V1.CounterpartsController.create
  - SibillWeb.Integration.V1.CounterpartsController.show
  - SibillWeb.Integration.V1.CounterpartsController.update
---

# Manage counterparts

Maintain the customers/suppliers you invoice or pay.

## Auth & base
- `Authorization: Bearer ${api_key}`, HTTPS, `https://integration.sibill.com/api/v1`.

## Steps
1. **Search by VAT first** to avoid duplicates: `GET /counterparts/search` (`SibillWeb.Integration.V1.CounterpartsActionsController.search`).
2. **List existing** counterparts for the company: `GET /companies/{company_id}/counterparts` (`SibillWeb.Integration.V1.CounterpartsController.index`) with cursor pagination.
3. **Create** when missing: `POST /companies/{company_id}/counterparts` (`SibillWeb.Integration.V1.CounterpartsController.create`).
4. **Read / update** an existing one: `GET .../counterparts/{id}` (`SibillWeb.Integration.V1.CounterpartsController.show`) then `PATCH .../counterparts/{id}` (`SibillWeb.Integration.V1.CounterpartsController.update`).

## Rules
- Validation failures return `422` with the `IntegrationError` envelope; `source.pointer` names the bad field.
- Counterparts are referenced by `Document.counterpart`; resolve/create the counterpart before issuing an invoice.
