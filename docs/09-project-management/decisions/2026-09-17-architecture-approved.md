# 2026-09-17 — Phase 1 System Architecture Approved

## Context

Following adoption of BRD v0.7 as the requirements baseline (see
[`2026-09-17-brd-v0.7-baseline.md`](2026-09-17-brd-v0.7-baseline.md)), TechXplo proposed a Phase 1 technical
architecture in [`../../03-architecture/system-architecture.md`](../../03-architecture/system-architecture.md),
covering the monorepo structure, the Admin Portal and User Portal (Next.js), the shared NestJS backend, the
Supabase/database design, authentication & authorisation, the role/permission model, API structure,
security, audit logging, testing, and deployment — built against the confirmed technology stack (Next.js,
NestJS, Supabase, single monorepo) and derived directly from the requirements baseline, with every
BRD-driven open item explicitly flagged rather than assumed (§20.5 of that document).

## Decision

**The architecture proposed in [`../../03-architecture/system-architecture.md`](../../03-architecture/system-architecture.md)
is approved as the accepted Phase 1 architecture.**

- Approved by: Donnell Naidoo (Lead Software Developer, TechXplo — solution design & development
  sign-off, per [`../../01-overview/stakeholders.md`](../../01-overview/stakeholders.md)).
- No implementation has started as a result of this approval alone — this record approves the *design*,
  not a go-ahead to build, which remains gated on the Client's formal BRD/SOW sign-off per
  [`2026-09-17-brd-v0.7-baseline.md`](2026-09-17-brd-v0.7-baseline.md).

## What This Approval Does and Does Not Cover

- **Covers**: the structural decisions in the architecture document — the two-frontend/one-backend
  topology, the monorepo layout, Supabase's scope (Auth + Postgres + Storage only), the auth/authorisation
  mechanism (Supabase Auth + JWT custom claims + MFA, enforced by NestJS Guards and mirrored by Postgres
  RLS), the module boundaries within NestJS, and the module-level shape of the notification/report-generation
  engines.
- **Does not cover**: the [§20.5 Deferred Decisions](../../03-architecture/system-architecture.md#205-architectural-decisions-deferred-until-implementation-or-further-requirements)
  table in that document — hosting/data residency, exact RLS policy SQL, SLA/backup targets, the Graph API
  auth flow, and the Phase-2/product-workflow items all remain open pending the BRD's own open questions
  ([`../../02-requirements/open-questions.md`](../../02-requirements/open-questions.md)). Resolving one of
  these does not require re-approving the architecture unless it changes a structural decision above.
- **Does not cover** the proposed database schema in
  [`../../04-design/database/`](../../04-design/database/), which remains a separate, still-pending-review
  artifact with two flagged assumptions (insurer/excess placement on `PolicySection` vs. `Policy`, and the
  `Progress`/FR-30 interpretation) awaiting confirmation before it can be similarly approved.

## Consequences

- The architecture document's structure (module boundaries, entity/domain split, auth mechanism) is now the
  reference point for all subsequent design work, including the database schema and any future API
  contract documentation in [`../../05-api/`](../../05-api/).
- A change to a structural decision in the approved architecture (e.g. abandoning the two-frontend split,
  or moving business logic out of NestJS) should be raised as a new decision record superseding this one,
  not as a silent edit to the architecture document.

## Addendum (2026-09-17) — Outbound Email Provider Resolved to EmailJS

The "exact Graph API auth flow for the Broker's Outlook mailbox" item listed under §20.5 Deferred Decisions
at the time of this approval has since been resolved: outbound email (FR-07, FR-08/FR-20, FR-33) will be
sent via **EmailJS**, called server-side from `NotificationsModule` only, with EmailJS configured against
the Broker's own mailbox so replies still land in their normal inbox. This is a resolution of a
previously-deferred implementation detail, not a change to a structural decision covered by this record's
approval (the `NotificationsModule` boundary itself was already approved) — so no re-approval was required.
See [`../../03-architecture/system-architecture.md §5`](../../03-architecture/system-architecture.md#5-nestjs-backend-architecture)
for the updated detail.
