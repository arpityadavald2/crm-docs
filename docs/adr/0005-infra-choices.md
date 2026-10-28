# ADR 0005: Infrastructure choices

- Status: accepted
- Date: 2026-10-28

## Context
This is a DevOps learning project. We need a cheap, simple environment to build and test on before Terraform, Ansible, CD and Kubernetes.

## Decision
- Cloud: AWS, region us-east-1 (N. Virginia).
- Dev server: one Ubuntu EC2 instance (instance type: t3.micro), stopped (not terminated) when not in use, to avoid cost.
- MySQL and Redis run in Docker Compose on the dev server.
- Source code lives in public GitHub repos (crm-app, crm-docs, and the infra repos). Reason: this is the first build and it is only for testing; visibility will be reconsidered later (see Consequences). Secrets are never committed (`.env` is git-ignored; only `.env.example` is tracked).

## Consequences
- us-east-1 is usually cheapest and gets new AWS features first, but it is far from Indian users (higher latency), and data stays outside India. If this became a real product for Indian customers, moving to ap-south-1 (Mumbai) should be reconsidered.
- Public repos mean anyone can read the code, so no credentials, keys or customer data may ever be committed.
- A t3.micro has 1 GB RAM, so MySQL and Redis are given small memory limits in Docker Compose.
- Repos are public for now only because this is a first test build. Before real customer data or real infrastructure details exist, switch them to private or review what is exposed.
