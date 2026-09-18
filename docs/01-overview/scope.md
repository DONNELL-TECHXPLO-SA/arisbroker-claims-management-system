# Scope

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 3.

## In Scope (Phase 1)

- Commercial lines only, across the full, finalised list of **29 products** confirmed by the Client (see
  [`../02-requirements/product-document-requirements.md`](../02-requirements/product-document-requirements.md)),
  including Property Damage, Motor Vehicle/Fleet, Public Liability, Fidelity Guarantee, and Directors &
  Officers Liability. Motor, Fidelity, and Directors & Officers were flagged as priority products for
  initial rollout; the remaining products are confirmed for inclusion. Product-specific claims workflow
  variations remain an open item (see
  [`../02-requirements/open-questions.md`](../02-requirements/open-questions.md)).
- Online claim lodgement, tracking, document management, and a communication log between Client (SOE) and
  Broker.
- Capture of Client, Policy, Insurer, Broker, and Asset information (key fields sourced from the policy
  summary; the full policy wording remains referenced offline). Clients claim against a specific policy,
  section, and — where the product requires it — a specific insured asset (e.g. a vehicle under a Motor
  Fleet policy).
- A documented mapping of required documents to claim stage and responsible party (Client, Broker, or
  Insurer/Assessor offline).
- Assessor detail capture (three fields: Name/Company, Contact Details, Date Appointed) for accountability
  only — not assessor appointment functionality, which remains the Insurer's function.
- Two downloadable report workbooks: the Claims History Report (claims register) and the Performance
  Report (which includes the claim-loss ratio and the motor and non-motor claims history).
- Role-based access control with least-privilege principles and multi-factor authentication (MFA).
- A full audit trail, filterable by responsible Broker, retained for a minimum of five years in line with
  FSCA record-keeping requirements.
- Outbound email integration (Outlook) so that portal-originated messages reach the Client's normal email
  inbox.
- Onboarding of approximately 14–15 SOE clients, ~12 broker-side users (1 Administrator, 1 Manager,
  remaining Broker/Users), and up to 2 users per SOE client (Primary and Secondary contact).
- Migration of existing Policy, Client, and Insurer information into the system ahead of go-live (see
  [`../02-requirements/data-migration-requirements.md`](../02-requirements/data-migration-requirements.md)).

## Out of Scope for Phase 1 (Deferred / Excluded)

- Broker-to-Insurer communication remains via email, outside the system, for Phase 1.
- Two-way email synchronisation (inbound client email replies automatically appearing in the portal) —
  feasibility to be assessed (open item).
- Microsoft 365 integration beyond outbound Outlook email — scope not yet defined by the Client (open
  item).
- Assessor appointment functionality or assessor portal access — assessors are appointed and managed
  solely by the Insurer.
- Personal lines insurance (e.g. personal motor, household contents) — the Client confirmed commercial
  lines only.
- Payment processing or integration with insurer/banking systems — settlement remains executed by the
  Insurer outside the portal.
- Policy administration, new business, or underwriting functionality.

## Assumptions

- The Client has provided the comprehensive, finalised list of commercial products; confirmation of which
  follow a standard claims workflow versus which require product-specific variations remains outstanding.
- The Client has supplied its existing report templates (the Claim Register Template and the Performance
  Reporting Template) for TechXplo to replicate digitally.
- The Client will make relevant staff (including the proposed System Administrator, David) available for
  further discovery sessions and user acceptance testing (UAT).
- Policy data will be captured manually from policy summaries in Phase 1; no live integration with insurer
  or underwriting-management systems is assumed.
- Hosting will be within South Africa (or another arrangement satisfying POPIA/FSCA data-residency
  expectations) — to be confirmed.

## Constraints

- The project will proceed in phases: this BRD covers Phase 1 only; Phase 2 items (e.g. Microsoft 365
  integration, potential insurer integration) will be scoped separately once Phase 1 is delivered.
- The Client is regulated by the FSCA and must retain client and claims information for a minimum of five
  years after policy cancellation — this drives the audit trail and data retention requirements (see
  [`../02-requirements/non-functional-requirements.md`](../02-requirements/non-functional-requirements.md)).
- Build will only commence once the BRD has been reviewed, amended as required, and formally signed off by
  the Client (see the accompanying Scope of Work document).
