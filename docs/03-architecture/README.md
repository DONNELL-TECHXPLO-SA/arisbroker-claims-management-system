# Architecture

How the system is built and why.

## Current status

[`system-architecture.md`](system-architecture.md) is the **approved** Phase 1 architecture (approved
2026-09-17) — not yet built against, but no longer a draft. It covers the monorepo structure, the Admin
Portal and User Portal (both Next.js), the shared NestJS backend, the Supabase/database design, auth &
authorisation, the role/permission model, API structure, security, audit logging, testing, and
deployment — and explicitly flags which decisions remain open pending answers in
[`../02-requirements/open-questions.md`](../02-requirements/open-questions.md). See
[`../09-project-management/decisions/2026-09-17-architecture-approved.md`](../09-project-management/decisions/2026-09-17-architecture-approved.md)
for the approval record.

The database design proposed in
[`../04-design/database/`](../04-design/database/) is a separate, **still-pending-review** artifact built
on top of this architecture — it has two flagged assumptions (§7.4, §7.7 of
[`../04-design/database/README.md`](../04-design/database/README.md)) not yet confirmed.

## Suggested contents

- `tech-stack.md` — chosen languages, frameworks, databases, hosting, and the reasoning behind each
- `system-architecture.md` — high-level component breakdown (frontend, backend, services, integrations)
- `data-flow.md` — how data moves through the system
- `integrations.md` — third-party services (payment gateways, insurers' APIs, SMS/email providers, etc.)
- `diagrams/` — architecture diagrams (export as `.png`/`.svg`, or keep source `.drawio`/mermaid `.md` files)
- `adr/` (optional) — Architecture Decision Records for significant technical decisions
