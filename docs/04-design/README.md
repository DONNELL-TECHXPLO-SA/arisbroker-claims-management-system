# Design

Visual and data design for the system.

## Current status

[`database/`](database/) contains the proposed Phase 1 database schema — **draft, pending review**, not
yet built against. [`database/README.md`](database/README.md) explains the design principles, entity
groups, the business-rule-to-enforcement-layer mapping, and every assumption flagged for confirmation;
[`database/schema.prisma`](database/schema.prisma) is the concrete schema. UI/UX design has not started.

## Suggested contents

- `ui-ux/` — wireframes, mockups, user flows, design system notes
- `database/` — schema definitions, ER diagrams, migration notes
- `api-contracts.md` (or link to `05-api/`) — how design decisions map to the API surface
