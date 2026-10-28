# ADR 0001: TypeScript, Node.js and Express

- Status: accepted
- Date: 2026-10-28

## Context
We need a backend for a multi-tenant CRM, built step by step as a DevOps learning project. The code should be easy to type-check, test and containerize.

## Decision
- Node.js 22 with TypeScript (strict mode) and Express.
- Zod for input validation; unknown fields are dropped.
- MySQL 8.4 accessed with mysql2 and raw SQL (no ORM). Knex is used for migrations only, run through `scripts/migrate.ts`.
- Redis 7 and BullMQ for background jobs.
- Vitest and Supertest for tests; ESLint and Prettier for code style.
- argon2 for password hashing, JWT for access tokens.

## Consequences
- Types catch many mistakes before runtime.
- Raw SQL keeps queries visible, but every query must include `org_id` by hand (see ADR 0003).
- The in-memory test fakes do not run real SQL, so every new SQL path also needs a smoke test against real MySQL.
- argon2 is a native package, so the Docker image uses Debian slim, not Alpine.
