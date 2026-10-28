# ADR 0004: Invoice numbering and lifecycle

- Status: accepted
- Date: 2026-10-28

## Context
Indian tax invoices need sequential numbers per financial year, and issued invoices must not change silently.

## Decision
- Number format: `INV/<FY>/<0001>`, for example `INV/2026-27/0001`. The financial year runs April to March.
- One counter per (organization, financial year) in `invoice_counters`, increased inside a transaction.
- The number is assigned only when the invoice is issued. Drafts have no number. Cancelled invoices keep their number.
- Status flow: `draft` -> `issued` -> `partially_paid` / `paid`, plus `cancelled`.
- Issued invoices are not editable. Cancel is allowed only for a draft, or an issued invoice with zero payments.
- Payments are allowed only on `issued` or `partially_paid` invoices (otherwise 409). An amount above the balance gives 422. The date cannot be in the future or before the issue date (422). Payments are never edited or deleted.
- The invoice row is locked with `FOR UPDATE` in a transaction when a payment is added, so two payments at once cannot overpay.
- Seller and buyer state codes are copied onto the invoice when the draft is created, so changing the GSTIN later does not change old invoices.
- "Today" is computed in India time, not UTC.

## Consequences
- Numbers have no gaps from abandoned drafts.
- Fixing a mistake means cancelling and re-issuing, or a future reversal feature.
- Known gap: the seller GSTIN is not yet shown on the invoice response, which a real tax invoice requires.
