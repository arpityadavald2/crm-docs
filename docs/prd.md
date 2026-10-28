# Product Requirements: Multi-tenant CRM + Invoicing (India / GST)

## Goal
A CRM with GST invoicing for small Indian businesses. Each business (organization) has its own users, customers, leads and invoices, fully separated from other businesses.

## Users and roles
| Role | Can do |
|---|---|
| owner | Everything, including deleting customers and leads, and changing organization GST settings |
| accountant | Create, issue and cancel invoices; record payments; read everything |
| sales | Work with customers and leads; read invoices (read-only) |

## In scope (Phase 1)
- Register an organization, login, JWT access tokens
- Customers (soft delete, GSTIN format check, state code derived from GSTIN)
- Leads (status filter, convert a lead into a customer in one transaction)
- Invoices: draft, issue (number assigned), cancel; GST split into CGST+SGST or IGST
- Payments: partial and full, never edited or deleted
- Organization settings: seller GSTIN and state code
- Daily background job that logs reminders for overdue invoices
- Health, readiness and metrics endpoints; Docker image for API and worker

## Out of scope (for now)
- Email/SMS sending (reminders are only logged)
- GSTIN verification against the government portal (format check only)
- Payment reversal or refunds
- Editing issued invoices
- E-invoicing / IRN, GST return filing

## Non-functional goals
- Tenant isolation: one organization never sees another's data
- Money is exact (integer paise, no floating point)
- Reproducible deployment (Terraform, Ansible, CI/CD in later phases)
