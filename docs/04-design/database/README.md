# Database Design (Proposed — Pending Review)

> **Status: DRAFT — for review, not yet built against.** This document and its companion
> [`schema.prisma`](schema.prisma) propose the Phase 1 physical database schema, derived field-by-field
> from every entity in [`../../02-requirements/data-requirements.md`](../../02-requirements/data-requirements.md)
> and cross-checked against [`functional-requirements.md`](../../02-requirements/functional-requirements.md),
> [`business-rules.md`](../../02-requirements/business-rules.md), [`use-cases.md`](../../02-requirements/use-cases.md),
> [`process-flow.md`](../../02-requirements/process-flow.md), and
> [`product-document-requirements.md`](../../02-requirements/product-document-requirements.md). It assumes
> the architecture already proposed in
> [`../../03-architecture/system-architecture.md`](../../03-architecture/system-architecture.md) (NestJS +
> Prisma + Supabase Postgres). Every place the BRD left a field's shape ambiguous is called out explicitly
> in [§7](#7-design-decisions--assumptions-flagged-for-confirmation) rather than silently assumed.

## 1. How to read this

- [`schema.prisma`](schema.prisma) is the concrete schema — read it alongside this document, not instead
  of it. Every model there carries an inline comment pointing back to its BRD source.
- This document explains *why* the schema looks the way it does: the two organising principles (§2, §3),
  the entity groups (§4), the ERD (§5 — wait, see below), the business-rule enforcement mapping (§6), and
  every assumption that needs the Client's or the team's confirmation (§7).
- Per this docs set's own convention, field-level detail is not repeated here where
  [`data-requirements.md`](../../02-requirements/data-requirements.md) already states it — this document
  focuses on the schema decisions layered on top of that data dictionary.

## 2. Design Principle: Enum vs. Admin-Maintainable Reference Table

The BRD labels many fields "Lookup" without saying whether the list behind that lookup is fixed
(code-governed) or something an Administrator should be able to extend without a developer release. Two
places state this explicitly — user-roles.md says the Administrator can "add/amend products and document
requirements without a development release" (FR-31 context), and FR-31 itself lists the Status Label &
Colour Map as Administrator-maintained data. Everywhere else, this schema applies one rule:

- **Postgres enum** where the value set is small, stable, and — critically — *read by application logic to
  decide what's allowed* (a state machine or a business-rule gate). Changing one of these values has code
  consequences, so it should require a migration, not an admin screen. Examples: `ClaimStatus`,
  `DecisionOutcome`, `DocumentType` (several values gate claim-closure logic — see §6), `UserRole`,
  `RecordStatus`.
- **Admin-maintainable reference table** where the BRD explicitly says so, or where the list is naturally
  open-ended business data with no code depending on its exact membership. Examples: `Product` (29 today,
  admin can add a 30th), `ProductChecklistItem`, `Insurer`, `ClaimStatusReportLabel` (the *display*
  label/colour for each `ClaimStatus` value — the code-governed status itself doesn't change, only how it's
  presented on a report).

`PaymentFrequency` is a borderline case, kept as an enum for now — see [§7.1](#71-paymentfrequency-as-an-enum-not-a-reference-table).

## 3. Design Principle: Where a Calculated Value Is Computed

Several fields are explicitly system-calculated, never captured directly (FR-19, FR-23, FR-25, FR-30). This
schema draws one distinction the architecture document didn't need to:

- **Single-row arithmetic** (every input lives on the same row being written) → a Postgres
  `GENERATED ALWAYS AS (...) STORED` column, added via raw SQL in the Prisma migration (Prisma's schema DSL
  has no native syntax for generated columns, so these are hand-added to the generated `migration.sql`
  before it's applied — see [§8](#8-migration-workflow-notes)). This makes the value impossible to get
  wrong at the database layer, not just in NestJS. Used for: `Claim.lateReported`,
  `Claim.netClaimAmount`, `Claim.progress`.
- **Cross-table lookups** (the calculation needs data from a different row/table) → computed by NestJS at
  write time and stored as a snapshot, per
  [`../../03-architecture/system-architecture.md §5`](../../03-architecture/system-architecture.md#5-nestjs-backend-architecture).
  Used for: `Claim.underwritingYearStartDate` (needs `Policy.inceptionDate`). A generated column can't do
  this — Postgres generated columns may only reference other columns in the *same* row.

This is a refinement of, not a contradiction of, the architecture document's "calculated values live in
NestJS" principle: NestJS remains where every one of these values is *decided*; the database additionally
guarantees the purely-arithmetic ones can't drift from that decision even under a bug, given how directly
several of them feed FSCA-relevant financial reporting (FR-25) and compliance flags (FR-19).

## 4. Entity Groups

| Group | Models | Realises |
|---|---|---|
| Reference & configuration | `CompanySettings`, `ClaimStatusReportLabel`, `Product`, `ProductChecklistItem`, `Insurer` | FR-31, FR-03, product-document-requirements.md |
| Identity | `Profile` | FR-17, user-roles.md, data-requirements §12.5/§12.9 |
| Client/Policy/Asset | `Client`, `Policy`, `PolicySection`, `PolicyUnderwritingYear`, `Asset` | FR-09, FR-10, FR-21, FR-24 |
| Claims | `Claim`, `ClaimAsset`, `ClaimChecklistItem` | FR-01–FR-03, FR-05, FR-06, FR-11–FR-13, FR-19, FR-21, FR-23, FR-25, FR-26, FR-27, FR-30, FR-33 |
| Documents & communication | `Document`, `CommunicationLogEntry` | FR-03, FR-04, FR-08 |
| Notifications | `NotificationLog` | FR-07, FR-20, FR-33, NFR-11 |
| Reporting | `GeneratedReport` | FR-14–FR-16, FR-22, FR-28, FR-29, FR-32 |
| Audit | `AuditLogEntry` | FR-18, NFR-08, NFR-09 |

## 5. Why `ClaimStatus` and `DecisionOutcome` Both Exist

At first glance these look redundant — three of the sixteen `ClaimStatus` values (`SETTLED`, `REPUDIATED`,
`WITHIN_EXCESS`) are the same three values as `DecisionOutcome`. They're kept as two separate fields
because they answer two different questions that happen to coincide at one moment in the workflow:

- `status` answers **"what stage is this claim at right now"** — the single value driving the client-facing
  dashboard (FR-05) and the whole 16-stage state machine in
  [`process-flow.md`](../../02-requirements/process-flow.md), including stages that have nothing to do with
  the Insurer's decision (`DOCUMENTS_OUTSTANDING`, `AWAITING_SIGNED_AOL`, `NOT_TAKEN_UP`, `CLOSED`, …).
- `decisionOutcome` (+ `settlementMethod`) answers **"what did the Insurer decide, structurally"** — a fact
  captured once (UC-05) that then *branches* the subsequent workflow (FR-12): a `CASH` settlement drives the
  AOL cycle (UC-06) toward `AWAITING_SIGNED_AOL`; a `REPAIR`/`REPLACEMENT` settlement drives the excess-invoice
  path toward `AWAITING_EXCESS_INVOICE_AND_POP`. Both of those subsequent `status` values correspond to the
  *same* `decisionOutcome = SETTLED`, so `decisionOutcome`/`settlementMethod` can't be reconstructed from
  `status` alone — the branch needs its own field.

So: when a decision is recorded, both fields are written together (e.g. `decisionOutcome = REPUDIATED` and
`status = REPUDIATED`), but `status` continues to move on its own from there while `decisionOutcome` stays
fixed as the historical record of what was decided.

## 6. Business Rule → Enforcement Layer

Every numbered rule in [`business-rules.md`](../../02-requirements/business-rules.md) needs to be enforced
*somewhere*. This table states where, so a reviewer can check nothing was silently left to "the frontend
will handle it" (which is not an enforcement layer per
[`../../03-architecture/system-architecture.md §8`](../../03-architecture/system-architecture.md#8-role--permission-model)).

| Rule | Enforcement layer | Mechanism |
|---|---|---|
| #1 Late-reported flag at >30 days, not blocking | DB (generated column) | `claims.late_reported GENERATED ALWAYS AS (date_registered::date - date_of_loss > 30) STORED` |
| #2 Claim requires policy + section (+ asset where required) | NestJS (validation), DB (FK NOT NULL on `policyId`/`policySectionId`) | `ClaimsModule` checks whether the product requires asset-level claiming before requiring `ClaimAsset` rows; the FK columns themselves are mandatory |
| #3 In-house claim number immutable | NestJS (no update path exposed) | `Claim.inHouseClaimNumber` — no `PATCH` endpoint field permits it; consider revoking column-level `UPDATE` privilege in migration as a second layer |
| #4 Checklist is advisory only | NestJS | `ClaimChecklistItem.status` is informational; no code path blocks a status transition on outstanding items |
| #5 Broker scoped to own clients; Manager/Admin see all | NestJS (Guards) + RLS | See architecture doc §8; RLS policy on `claims`/`clients` checks `clients.assigned_broker_user_id = auth.uid()` or role ∈ {ADMINISTRATOR, MANAGER} |
| #6 Insurer claim number mandatory before "Submitted to Insurer" | NestJS (transition guard) | `ClaimsModule` status-transition service rejects the transition if `insurerClaimNumber IS NULL` — a cross-field, state-dependent rule, not a static CHECK |
| #7 Assessor = 3 fields only, no workflow | Schema shape | No assessor-workflow tables exist; `assessorNameCompany`/`assessorContactDetails`/`assessorDateAppointed` are plain nullable columns |
| #8 Decision = exactly one of 3 outcomes; Repudiated needs a reason | DB (CHECK constraint) + NestJS | `CHECK (decision_outcome IS DISTINCT FROM 'REPUDIATED' OR repudiation_reason IS NOT NULL)` added in migration; NestJS also validates before persisting |
| #9/#10 AOL cycle / excess-invoice cycle sequencing | NestJS (transition guard) | Requires checking `Document` rows exist for the relevant `DocumentType` before allowing the next `status` — cross-table, so NestJS not a static constraint |
| #11 Claim can only close with outcome-specific documents on file | NestJS (transition guard, transactional) | Checks for a `PROOF_OF_PAYMENT` document (Settled) or non-null `repudiationReason` (Repudiated) before setting `status = CLOSED` |
| #12 Closed claim is view-only to Client, editable only by Administrator; 5-year retention | NestJS (Guards) + RLS + no purge path | Guard rejects non-Administrator writes once `status = CLOSED`; no delete endpoint exists anywhere for `Claim` |
| #13 Audit entries immutable, 5-year retention, Broker sees own only | DB (privilege grants + RLS) | `audit_log_entries` — `INSERT`-only grant, RLS denies `UPDATE`/`DELETE` for every role; see architecture doc §16 |
| #14 Max 2 active users per client | DB (partial unique index) + NestJS | `CREATE UNIQUE INDEX ON profiles (client_id, role) WHERE is_active AND role IN ('CLIENT_PRIMARY','CLIENT_SECONDARY')` — see [§7.5](#75-profile-as-a-single-table-not-separate-contact-columns); NestJS gives a friendly error before hitting the constraint |
| #15 Client/asset must exist (and be ACTIVE) before a claim can reference it | NestJS + DB (FK) | FK guarantees existence; `recordStatus = ACTIVE` check is NestJS-level since `RecordStatus` isn't itself a DB gate on the FK |
| #16 Reminders — email-only, admin-configurable interval, repeat until resolved | NestJS (scheduled job) | `CompanySettings.defaultReminderIntervalDays`; job logic lives in `NotificationsModule`, not the database |
| #17 Reporting scoped by role | NestJS (Guards) + RLS | Same mechanism as #5, applied to `GeneratedReport` generation endpoints |
| #18 Report layout fidelity; values not formulas | Application (report-generation library) | Not a schema concern — see architecture doc §17 (automated fidelity test against Appendix H) |
| #19 Underwriting year rolls from inception, not calendar year | NestJS (write-time computation) | See [§3](#3-design-principle-where-a-calculated-value-is-computed) |
| #20 Loss ratio unavailable (not zero) without captured premium | Application (report query) | A `LEFT JOIN` from claims grouped by `underwritingYearStartDate` to `PolicyUnderwritingYear` — a missing row renders as unavailable by construction, since there's nothing to divide by |
| #21 Broker/Client comments distinct from communication log | Schema shape | `Claim.brokerComment`/`clientComment` vs. `CommunicationLogEntry` are separate models entirely |
| #22 Lodging user & channel recorded; auto-notify Client-Primary | NestJS | `Claim.lodgedByUserId`/`lodgementChannel` set at creation; `NotificationsModule` fires `BROKER_ASSISTED_LODGEMENT_CONFIRMATION` |
| #23 VAT manual, Net Claim = Gross − Excess + VAT | DB (generated column) + NestJS (input validation only) | `claims.net_claim_amount GENERATED ALWAYS AS (gross_claim_amount - excess_applied + vat_amount) STORED` |

## 7. Design Decisions & Assumptions Flagged for Confirmation

These are the places this schema had to make a call where the BRD's data dictionary didn't fully specify
the shape. None of them are hard to change later, but each should be confirmed with the Client/Naison or
decided by the team before implementation starts, consistent with this project's practice of tracking
open items transparently rather than assuming them away (see
[`open-questions.md`](../../02-requirements/open-questions.md)).

### 7.1 `PaymentFrequency` as an enum, not a reference table

data-requirements.md §12.1 labels this "Lookup | E.g. Yearly, Monthly" — illustrative, not an exhaustive
list. It's kept as a fixed enum (`MONTHLY`, `QUARTERLY`, `SEMI_ANNUALLY`, `ANNUALLY`, `OTHER`) because
nothing in the BRD suggests the Administrator needs to add new frequencies, and it's a low-stakes call to
promote to a reference table later (identical shape to `Insurer`) if that turns out to be wrong.

### 7.2 `ClaimStatusReportLabel` as a config layer on top of `ClaimStatus`, not a dynamic status list

The BRD explicitly makes the *label and colour* Administrator-configurable (FR-31), but several business
rules (repudiation-reason gating, closure-document gating, the AOL/excess-invoice branching) are hard-coded
against *specific* status values. Making the status list itself dynamic would put those business rules at
risk of referencing a status an Administrator has since renamed or removed. This schema keeps the 16 status
*codes* fixed (enum) and lets only their *display* wording/colour be edited — which also cleanly absorbs
[Q-003](../../02-requirements/open-questions.md#q-003-client-facing-claim-status-wording) (the exact
wording is still open) without needing a schema change once it's answered.

### 7.3 Generic document-type/stage/responsibility matrix folded into the `DocumentType` enum, not its own table

data-requirements.md §12.8 describes a generic matrix (document type → required stage → responsible party).
This schema does not turn that matrix into a queried reference table, because several of its entries
(`AOL_SIGNED`, `PROOF_OF_PAYMENT`, `REPUDIATION_LETTER`) are exactly the documents that gate claim-closure
business rules (#11) — i.e. the "stage" and "responsible party" columns are really just descriptive
context for a fixed set of document kinds the code already has to know about individually. The matrix
itself remains fully documented in §12.8 for build reference; this schema captures only the resulting
`DocumentType` enum values needed to enforce the rules that depend on them.

### 7.4 Insurer and financial terms live on `PolicySection`, not `Policy`; risk premium lives on `Policy`

The BRD is not fully explicit here, and this is the assumption most worth the Client/Naison confirming
directly:

- data-requirements.md §12.1 lists "Insurer(s)" as a Policy-level field ("one or more insurers on the
  policy"), but scope.md and §12.2 both describe claims as being made against "a specific policy, **section**,
  and... asset", and FR-25 says excess "defaults from the **policy section**". Since a commercial policy
  can plausibly carry different products (sections) underwritten by different insurers under one umbrella
  policy number, this schema places `insurerId`, `sumInsuredLimitOfIndemnity`, and the excess fields on
  `PolicySection` rather than `Policy` — the level at which FR-25 already says excess lives, and the level
  every claim actually references. A policy's "insurer(s)" for display purposes is then just the distinct
  set of its sections' insurers.
- By contrast, FR-16/FR-24 are explicit that risk premium and payment frequency are captured **"per policy
  and per underwriting year"** (not per section), so `PolicyUnderwritingYear` hangs off `Policy` directly.

If the Client intends one insurer strictly per policy number (not per section), this only requires moving
`insurerId` up one level — a small, low-risk schema change, not a redesign.

> **Tracked as** [Q-018](../../02-requirements/open-questions.md#q-018-is-the-insurer-and-excesssum-insured-set-per-policy-or-per-section-within-a-policy) — awaiting an answer from Aris Brokers.

### 7.5 `Profile` as a single table, not separate Primary/Secondary contact columns on `Client`

user-roles.md's five roles already include `CLIENT_PRIMARY`/`CLIENT_SECONDARY` as distinct role values, so
"Primary contact" vs. "Secondary contact" is represented by which role a `Profile` row has, not by two
nullable contact columns on `Client`. The 2-active-user-per-client limit (business rule #14) is then a
single partial unique index — `(client_id, role) WHERE is_active AND role IN (...)` — guaranteeing at most
one active Primary and one active Secondary per client at the database layer, in addition to the
friendlier NestJS-level validation error a user should actually see.

`Profile.id` is not Prisma-generated — it must equal the corresponding `auth.users.id`, kept in sync via a
Postgres trigger function on `auth.users` insert (the standard Supabase pattern: a `handle_new_user()`
function + trigger that inserts a matching `profiles` row). This is an implementation-phase detail, not a
schema decision, but is noted here since it explains why `Profile.id` has no `@default`.

`mfaEnrolledAt` and login history are **not** authoritative on `Profile` — Supabase's own `auth.users`/
`auth.mfa_factors` tables are the source of truth (NFR-01's actual enforcement point is the JWT's AAL claim,
per the architecture doc §7). `mfaEnrolledAt` exists purely as a convenience cache for an Administrator's
user-list view, avoiding an `auth` schema join on every list request; `last_login` is deliberately **not**
duplicated at all — read directly from `auth.users.last_sign_in_at` when needed (data-requirements §12.9
lists it, but duplicating a value Supabase already tracks authoritatively would only risk it going stale).

### 7.6 `CompanySettings` as an enforced singleton

FR-31 describes one settings area for the one broker firm — there is no multi-tenancy concept for the
broker organisation itself (unlike `Client`, of which there are ~14–15). The table is modelled as a single
row (`id` defaulting to `1`) with singleton enforcement recommended at two layers: application logic (no
"create" endpoint, only "update the one row") and, for defence in depth, a `CHECK (id = 1)` constraint
added in migration.

### 7.7 `Progress` interpretation: "Resolved" = `status = CLOSED` only

FR-30 defines Progress as "Outstanding (not yet approved) or Resolved (**resolved and closed**)". Read
literally, "resolved and closed" is one bundled condition, so this schema's generated-column logic (to be
added in migration) sets `progress = 'RESOLVED'` only when `status = 'CLOSED'`, and `'OUTSTANDING'`
otherwise — meaning a claim that has been `SETTLED`/`REPUDIATED`/`WITHIN_EXCESS` but not yet formally closed
(UC-07) still reports as Outstanding on the Summary Report tab. This reading should be confirmed against
the Client's actual reporting expectation before the report-generation logic is built, since the alternative
reading (any of the three decision outcomes counts as Resolved, independent of closure) would change the
Summary Report's counts materially.

> **Tracked as** [Q-019](../../02-requirements/open-questions.md#q-019-does-a-claim-count-as-resolved-once-the-insurers-decision-is-recorded-or-only-once-the-claim-is-formally-closed) — awaiting an answer from Aris Brokers.

### 7.8 Hosting/RLS/backup specifics remain deferred

Nothing in this schema resolves the items already flagged as open in the architecture document (§20.5) —
hosting/data residency, exact RLS policy SQL, backup RPO/RTO, and the two-way-email/Phase-2/product-workflow
items. This schema is designed to be agnostic to all of those: it works unchanged regardless of which way
each is eventually decided.

## 8. Migration Workflow Notes

- Prisma's schema DSL cannot express `GENERATED ALWAYS AS ... STORED` columns, `CHECK` constraints, or
  partial unique indexes directly. The recommended workflow is: run `prisma migrate dev --create-only` to
  generate the base migration from `schema.prisma`, then hand-edit the generated `migration.sql` to add:
  - the three generated columns (`late_reported`, `net_claim_amount`, `progress`) — §3, §6
  - the repudiation-reason `CHECK` constraint — §6, rule #8
  - the document ownership `CHECK` constraint on `documents` (exactly one of `claim_id`/`policy_id` set)
  - the partial unique index on `profiles (client_id, role)` — §7.5
  - the `CHECK (id = 1)` constraint on `company_settings` — §7.6
  - RLS `ENABLE` statements and policies (left for the implementation phase — see architecture doc §6/§8)
  - the `auth.users` trigger creating a matching `profiles` row, and the `profiles.id → auth.users.id`
    foreign key (Prisma cannot declare a cross-schema FK into `auth`, which it doesn't manage)
- This keeps the schema's declared shape (`schema.prisma`, reviewable and diffable) as the source of truth
  for structure, while the constraints that make it *behaviourally* correct are captured explicitly in
  version-controlled migration SQL rather than left as tribal knowledge.

---

*Next steps once this is reviewed: seed data for `Product`/`ProductChecklistItem` (28 of 29 products are
fully specified — Credit Insurance is blocked on [Q-002](../../02-requirements/open-questions.md#q-002-credit-insurance-document-list));
draft the actual RLS policy SQL per table; and confirm §7.4 and §7.7 with the Client before the first
migration is written against a real Supabase project.*
