# ADR 0003: Tenant isolation, orgId only from the verified JWT

- Status: accepted
- Date: 2026-10-28

## Context
Many organizations share one database. A bug that leaks one organization's data to another is the most serious failure this system can have.

## Decision
- `orgId` comes only from the verified JWT (`req.auth.orgId`). Never from the body, query or URL.
- Every repository method takes `orgId` as its first argument and uses it in every SQL query (`WHERE ... AND org_id = ?`).
- A record that belongs to another organization returns 404, never 403, so its existence is not revealed.
- Zod strips unknown fields, so clients cannot send `orgId`, `id`, `status` or totals.
- Roles: owner, accountant, sales. Deleting customers and leads is owner only.
- Customers and leads use soft delete (`deleted_at`).
- One deliberate exception: the background reminder job is a system job with no user. Its repository looks across organizations, every row carries its `orgId`, and the result is only logged, never returned from an API.

## Consequences
- Isolation depends on discipline in every query, so tests must cover cross-organization access.
- Any new background job that reads across organizations must be reviewed against this ADR.
