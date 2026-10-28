# ADR 0002: Money as integer paise, GST rate in basis points

- Status: accepted
- Date: 2026-10-28

## Context
Invoices need exact tax arithmetic. Floating point numbers give rounding errors (for example 0.1 + 0.2), and decimals are easy to misuse across languages and databases.

## Decision
- All money is stored and calculated as integer paise (BIGINT in MySQL). Rs 1 = 100 paise.
- GST rate is stored in basis points: 18% = 1800. Allowed rates: 0, 500, 1200, 1800, 2800.
- Rounding is half-up, per line item, with integer math only.
- For intra-state supply, CGST gets floor(tax / 2) and SGST gets the rest, so the two always add up to the total tax.
- Limits: quantity 1..1000, unit price at most 1,000,000,000 paise, at most 100 items per invoice.

## Consequences
- No rounding surprises; totals always add up exactly.
- API clients send and receive paise, so a UI must convert for display.
- Changing the allowed GST rates needs a code change, which is intended.
