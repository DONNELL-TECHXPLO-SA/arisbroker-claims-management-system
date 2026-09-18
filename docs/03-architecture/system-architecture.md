# System Architecture (Approved)

> **Status: APPROVED (2026-09-17)** — this is now the accepted Phase 1 architecture for the Claims
> Management Portal, approved by Donnell Naidoo (Lead Software Developer, TechXplo — solution design &
> development sign-off per [`../01-overview/stakeholders.md`](../01-overview/stakeholders.md)). It was
> derived directly from [`../02-requirements/`](../02-requirements/) (functional requirements FR-01–FR-33,
> non-functional requirements NFR-01–NFR-12, [`../02-requirements/data-requirements.md`](../02-requirements/data-requirements.md),
> [`../02-requirements/user-roles.md`](../02-requirements/user-roles.md) and
> [`../02-requirements/business-rules.md`](../02-requirements/business-rules.md)). No implementation has
> started yet. Approval of this document does **not** resolve the BRD's own open items (see
> [`../02-requirements/open-questions.md`](../02-requirements/open-questions.md)); this document says so
> explicitly wherever one of them bears on the design — see [§20.5 Deferred Decisions](#205-architectural-decisions-deferred-until-implementation-or-further-requirements),
> which remain open and should be revisited as each is answered, without requiring a re-approval of the
> overall architecture unless the answer changes a structural decision above.
>
> **Confirmed technology stack** (given, not chosen here): Next.js (frontend, two applications), NestJS
> (backend), Supabase (Postgres + supporting services), single monorepo.
>
> **Amendment (2026-09-17)**: outbound email delivery (FR-07, FR-20, FR-33) will use **EmailJS**, resolving
> what [§20.5 Deferred Decisions](#205-architectural-decisions-deferred-until-implementation-or-further-requirements)
> had previously flagged as an open implementation detail (the Microsoft Graph API/Outlook auth flow). This
> is a resolution of a previously-deferred item, not a change to an approved structural decision — see
> [§5](#5-nestjs-backend-architecture).

---

## Table of Contents

1. [Overall System Architecture](#1-overall-system-architecture)
2. [Monorepo Structure](#2-monorepo-structure)
3. [Admin Portal Architecture](#3-admin-portal-architecture)
4. [User Portal Architecture](#4-user-portal-architecture)
5. [NestJS Backend Architecture](#5-nestjs-backend-architecture)
6. [Supabase / Database Architecture](#6-supabase--database-architecture)
7. [Authentication & Authorisation](#7-authentication--authorisation)
8. [Role & Permission Model](#8-role--permission-model)
9. [Frontend ↔ NestJS Communication](#9-frontend--nestjs-communication)
10. [NestJS ↔ Supabase Communication](#10-nestjs--supabase-communication)
11. [Shared Packages](#11-shared-packages)
12. [Environment & Configuration Strategy](#12-environment--configuration-strategy)
13. [Document/File Storage](#13-documentfile-storage)
14. [API Structure](#14-api-structure)
15. [Security Considerations](#15-security-considerations)
16. [Audit Logging](#16-audit-logging)
17. [Testing Structure](#17-testing-structure)
18. [Development & Deployment Structure](#18-development--deployment-structure)
19. [Local Development Workflow](#19-local-development-workflow)
20. [What's Shared vs Isolated vs Deferred](#20-whats-shared-vs-isolated-vs-deferred-summary)

---

## 1. Overall System Architecture

Two Next.js frontends sit in front of one NestJS API, which is the only component that talks to Supabase
for business data. This is a deliberate simplification (NFR-12 asks for a maintainable, API-based
architecture, not a distributed one) — there is no microservice split, no message queue, and no separate
services beyond the three the Client's stack already names.

```
                    ┌─────────────────────┐        ┌─────────────────────┐
                    │   Admin Portal       │        │   User Portal        │
                    │   (Next.js)          │        │   (Next.js)          │
                    │   Administrator,     │        │   Client-Primary,    │
                    │   Manager, Broker    │        │   Client-Secondary   │
                    └──────────┬───────────┘        └──────────┬───────────┘
                               │  HTTPS (REST, JSON)             │
                               │  Bearer: Supabase JWT            │
                               └───────────────┬──────────────────┘
                                                ▼
                                     ┌─────────────────────┐
                                     │   NestJS API         │
                                     │   (shared backend)   │
                                     │   - business logic    │
                                     │   - authorisation      │
                                     │   - audit logging       │
                                     │   - report generation   │
                                     │   - notification engine  │
                                     └──────────┬──────────────┘
                                                │  service-role access
                                                ▼
                          ┌──────────────────────────────────────────┐
                          │                Supabase                   │
                          │  - Postgres (all business data, RLS on)   │
                          │  - Auth (MFA/TOTP, JWT issuance)          │
                          │  - Storage (documents, reports, logos)     │
                          └──────────────────────────────────────────┘
                                                │
                                                ▼ (outbound only)
                                            EmailJS API
                                     (FR-20 — email notifications)
```

**Both frontends authenticate directly against Supabase Auth** (login, MFA challenge, session/token
refresh) because that is Supabase's job and re-implementing it inside NestJS would duplicate a solved
problem. **Every other read or write of business data goes through the NestJS API** — neither frontend
queries Postgres or Supabase Storage directly for claims, clients, policies, documents, or reports. This
keeps all business logic, validation, and authorisation in one place (NFR-12), and means Supabase can be
swapped or extended later without touching either frontend.

## 2. Monorepo Structure

```
aris-claims-portal/
├── apps/
│   ├── admin/              # Next.js — Admin Portal
│   ├── portal/             # Next.js — User (Client) Portal
│   └── api/                 # NestJS — shared backend
│       └── prisma/           # schema.prisma + migrations (single source of truth for the DB schema)
├── packages/
│   ├── shared-types/        # DTOs, enums, Zod validation schemas used by both apps and the API
│   ├── ui/                   # Design tokens + primitive components (Button, Input, Table, StatusBadge…)
│   ├── utils/                 # Pure display/formatting helpers (currency, dates) — no business rules
│   ├── eslint-config/
│   └── tsconfig/
├── supabase/
│   ├── config.toml            # local emulation config (Supabase CLI) — Auth/Storage/Postgres for dev
│   └── seed/                   # reference data + sample per-role test users for local/staging seeding
├── docs/
├── turbo.json
├── pnpm-workspace.yaml
└── package.json
```

| Directory | Responsibility |
|---|---|
| `apps/admin` | Everything internal staff (Administrator, Manager, Broker/User) see and do. |
| `apps/portal` | Everything an SOE client contact (Client-Primary/Secondary) sees and does. |
| `apps/api` | All business logic, validation, authorisation, persistence, audit logging, report generation, and outbound email. The only component with the Supabase service-role key. |
| `packages/shared-types` | The contract between the three apps — request/response DTOs, enums (`ClaimStatus`, `DecisionOutcome`, `UserRole`, `DocumentType`, `LodgementChannel`, etc.), and Zod schemas used for both client-side form validation and (re-)validated server-side in NestJS. Single source of truth so the two frontends and the API can never drift on shape. |
| `packages/ui` | Design tokens (colour, spacing, type scale) and low-level primitives only. Deliberately does **not** contain page-level or workflow components — see [§20](#20-whats-shared-vs-isolated-vs-deferred-summary) on why Admin and Portal UX are not shared beyond this layer. |
| `packages/utils` | Pure, side-effect-free formatting helpers (e.g. rendering a currency value, formatting a date for display). Calculated business values themselves (underwriting year, net claim amount, late-reported flag, loss ratio) are computed once, in NestJS, and never recomputed client-side — see [§5](#5-nestjs-backend-architecture). |
| `supabase/` | Local developer emulation and reference/seed data only. Not the source of truth for the schema (Prisma is — see [§6](#6-supabase--database-architecture)). |

**Tooling**: pnpm workspaces + Turborepo for task orchestration (`turbo run dev|build|lint|test` across all
apps/packages with caching). This is a pragmatic, common pairing with Next.js and adds no infrastructure
beyond the repo itself.

## 3. Admin Portal Architecture

**Audience**: Administrator, Manager, Broker/User (all Aris Brokers-internal roles — see
[`../02-requirements/user-roles.md`](../02-requirements/user-roles.md)).

**Responsibilities** (mapped to functional requirements):

- Claim processing end-to-end: capture Insurer claim number (FR-06), record assessor details (FR-11),
  record the Insurer's decision and run the AOL/excess-invoice workflow (FR-12), close/reopen claims
  (FR-13), flag/motivate late-reported claims (FR-19).
- Client, Policy, Insurer & Asset management (FR-09, FR-21), including risk premium/payment frequency
  capture per underwriting year (FR-24).
- Broker-assisted claim lodgement on behalf of a Client (FR-33).
- Broker-Client communication log, from the Broker's side (FR-08).
- User & access management — Administrator only (FR-17).
- Reporting — generate/download the Claims History and Performance reports for any client in scope
  (FR-14, FR-15, FR-22).
- Audit trail viewer, scoped per role (FR-18).
- Company & Report Settings — Administrator only (FR-31).
- Product & document-requirement reference data maintenance (so Administrator can add/amend products or
  document checklists without a development release, per [`user-roles.md`](../02-requirements/user-roles.md)).

**Structure**: standard Next.js App Router application. Role- and scope-based UI gating (e.g. a
Broker/User only ever sees their assigned clients; Company & Report Settings routes render nothing but a
403 for non-Administrators) is enforced in Next.js middleware/route guards for UX purposes only — the
authoritative enforcement is always the NestJS API (see [§8](#8-role--permission-model)), since a
route guard in the frontend is not a security boundary.

## 4. User Portal Architecture

**Audience**: Client-Primary, Client-Secondary (external SOE client contacts).

**Responsibilities** (mapped to functional requirements):

- Claim self-service submission (FR-01), with the required-document checklist shown as advisory (FR-03).
- Document upload/download against a claim (FR-04).
- Claim status tracking dashboard (FR-05), including the Notifications & Reminders "banner" surface
  described in [`../02-requirements/process-flow.md`](../02-requirements/process-flow.md) (the reminder
  engine has no screen of its own — its only visible surface is a prompt on this dashboard).
- Client-Broker communication log, from the Client's side (FR-08).
- Uploading a signed Agreement of Loss / excess proof of payment where required (FR-12).
- Leaving a claim comment, including after closure (FR-27).
- Own-organisation reporting (FR-14, FR-15), read-only view of own policies.

**Structure**: a second, independent Next.js App Router application — not a set of routes bolted onto the
Admin app behind a role check. See [§20](#20-whats-shared-vs-isolated-vs-deferred-summary) for why this
separation matters (blast radius, deployment independence, and a materially simpler UX for a
non-technical, infrequent user per NFR-10).

## 5. NestJS Backend Architecture

The API is organised as one NestJS application with feature modules mirroring the domain, not a set of
microservices. Each module owns its controllers, services, and Prisma-backed repository logic for its
slice of the domain.

| Module | Responsibility | Key requirements |
|---|---|---|
| `AuthModule` | Verifies the Supabase-issued JWT on every request, extracts role/claims, enforces MFA (AAL2) before any business endpoint executes. | NFR-01, NFR-02 |
| `UsersModule` | User/account CRUD, role assignment, 2-user-per-client enforcement, activation/deactivation. | FR-17 |
| `ClientsModule` | SOE client records, Broker-to-Client assignment. | FR-09, FR-10 |
| `PoliciesModule` | Policy records, sections, risk premium/payment frequency per underwriting year. | FR-09, FR-24 |
| `AssetsModule` | Per-policy asset register. | FR-21 |
| `InsurersModule` | Insurer reference data. | §12.3 data requirements |
| `ClaimsModule` | The core lifecycle: registration, status transitions, late-reported flagging, Insurer claim-number linking, assessor capture, decision recording, AOL/excess workflow, closure/reopen, financial-value calculation (net claim, underwriting year). The largest module — several requirements converge here. | FR-01, FR-02, FR-05, FR-06, FR-11–FR-13, FR-19, FR-21, FR-23, FR-25, FR-26, FR-30, FR-33 |
| `DocumentsModule` | Document metadata + Supabase Storage signed URL issuance for upload/download. | FR-03, FR-04 |
| `CommunicationModule` | The claim-specific Client↔Broker message log (distinct from the two reportable comment fields, which live on `ClaimsModule`). | FR-08, FR-27 |
| `NotificationsModule` | Outbound email sending via **EmailJS** and the scheduled reminder engine (a background job, not a controller — see [§7](#7-authentication--authorisation) note on Module 6 in the process flow). | FR-07, FR-20, FR-33, NFR-11 |
| `ReportsModule` | Generates the two report workbooks byte-for-byte against the Client's template (FR-32), persists each generated instance immutably (FR-28), issues header/branding data from `SettingsModule`. | FR-14–FR-16, FR-22, FR-28, FR-29, FR-32 |
| `SettingsModule` | Company & Report Settings (Administrator-only). | FR-31 |
| `ProductConfigModule` | Product list and per-product/generic document-requirement reference data (admin-maintainable, not hardcoded — see [`../02-requirements/product-document-requirements.md`](../02-requirements/product-document-requirements.md)). | FR-03 |
| `AuditModule` | Cross-cutting: not called directly by feature modules but attached as a global interceptor/Prisma middleware so every mutation is captured consistently rather than relying on each module to remember to log. | FR-18, NFR-08 |

**ORM**: Prisma. It is the single source of truth for the schema (`apps/api/prisma/schema.prisma` +
migrations), gives strong TypeScript types shared naturally with `packages/shared-types`, and is a
well-trodden pairing with NestJS and Postgres/Supabase.

**Calculated values live here, once**: underwriting year (FR-23), net claim amount (FR-25), the
late-reported flag (FR-19), progress/Outstanding-Resolved (FR-30), and the loss ratio (FR-16) are all
computed in `ClaimsModule`/`ReportsModule` at write- or report-generation time, never recomputed
independently in either frontend. This avoids the two frontends silently disagreeing on a number that
appears on a signed-off report.

**Notifications & Reminders as a background job, not a screen**: per the BRD's own framing (see
[`../02-requirements/process-flow.md#architecture-note-notifications--reminders-module-6`](../02-requirements/process-flow.md#architecture-note-notifications--reminders-module-6)),
this runs as a scheduled job inside `NotificationsModule` using `@nestjs/schedule`, polling for trigger
conditions (outstanding documents, an unsigned AOL, no status change for the configurable interval) and
sending outbound email through the same channel as every other notification. It is deliberately kept
inside the single NestJS application rather than split into a separate worker service or a
Supabase/Postgres-side cron (`pg_cron`) — at this scale, a second execution environment for one background
job adds operational surface (a second thing to deploy, monitor, and keep in sync with business logic)
without a corresponding benefit, and it would fragment business logic across two languages/layers
(SQL functions vs NestJS), working against NFR-12 (maintainability).

**Email delivery via EmailJS**: every outbound email required by FR-07 (reminders), FR-08/FR-20
(communication-log delivery), and FR-33 (broker-assisted lodgement confirmation) is sent through
**EmailJS's REST API**, called server-side from `NotificationsModule` — never from either Next.js app.
EmailJS is configured with the Broker's own mailbox as its connected email service, so a message sent
through it still arrives addressed from, and any reply still lands in, the Broker's normal inbox —
satisfying FR-20's requirement without a Microsoft Graph API app-registration/consent flow against the
Broker's M365 tenant. This resolves what [§20.5](#205-architectural-decisions-deferred-until-implementation-or-further-requirements)
previously listed as an open implementation detail. EmailJS's Private Key (used to authenticate
server-to-server calls) is held only in the NestJS environment, per [§12](#12-environment--configuration-strategy) —
it is never the browser-side Public Key flow EmailJS is often used with from a frontend, since that would
put an email-sending credential inside a public bundle and would also mean email dispatch isn't going
through NestJS's authorisation/audit path. Every send attempt is still recorded in `NotificationLog`
(delivery status, failure reason) exactly as described for any notification channel — the choice of
provider doesn't change that requirement.

## 6. Supabase / Database Architecture

Supabase is used for exactly three things, deliberately not more:

1. **Postgres** — the single relational database for all business data (clients, policies, assets,
   claims, documents metadata, communication log, comments, audit log, settings, product/document
   reference data, generated-report metadata). Schema owned by Prisma migrations in `apps/api/prisma/`.
2. **Auth** — user identity, session/JWT issuance, and native MFA (TOTP) enrolment/challenge, satisfying
   NFR-01 without building custom MFA infrastructure. See [§7](#7-authentication--authorisation).
3. **Storage** — object storage for claim documents, company logos, and generated report workbooks (see
   [§13](#13-documentfile-storage)), with Supabase's built-in backup/point-in-time-recovery addressing
   NFR-07 rather than a bespoke backup pipeline.

**Not used, and explicitly excluded to avoid unnecessary complexity**:

- **Supabase Realtime** — no requirement calls for live/websocket updates; the communication log and
  status dashboard are refreshed on normal request/response, matching FR-08's description as a
  timestamped log rather than a live chat. Can be revisited if a future requirement genuinely needs it.
- **Supabase Edge Functions** — would split business logic across two runtimes/layers (Edge Functions vs
  NestJS). All business logic stays in NestJS.
- **`pg_cron`** — the reminder engine runs in NestJS (see [§5](#5-nestjs-backend-architecture)), not as a
  database-side scheduled job, for the same reason.

**Row-Level Security (RLS)**: RLS is enabled on every table, as **defense-in-depth**, even though in normal
operation only the NestJS API (using the Supabase service-role key) talks to Postgres and NestJS's own
Guards are the primary authorisation boundary (see [§8](#8-role--permission-model)). Given the multi-tenant
isolation requirement between SOE clients (NFR-02, business rules #5/14/17) and the regulatory sensitivity
of the data (FSCA/POPIA), a second, database-enforced layer that mirrors the same scoping rules is a
proportionate safeguard against a bug in the application-layer authorisation logic — this is not
over-engineering given what the data is and who it's about, but the exact policy-per-table SQL is an
implementation-phase task, not decided here. RLS policies are expected to derive the caller's role and
scope from JWT custom claims injected via a Supabase Auth Hook (see [§7](#7-authentication--authorisation)).

**Multi-tenancy model** (logical, confirming [`../02-requirements/data-requirements.md#1210-entity-relationship-diagram-logical`](../02-requirements/data-requirements.md#1210-entity-relationship-diagram-logical)):

- One `organisation`/`company_settings` row represents Aris Brokers itself (there is only one Broker firm
  in this system).
- `clients` (SOE) each carry `assigned_broker_user_id` — a single active assignment, consistent with FR-10
  ("each Client is assigned to one Broker/User").
- `users`/`profiles` extend `auth.users` with `role`, and either `client_id` (for Client-Primary/Secondary)
  or broker-internal status (Administrator/Manager/Broker-User have no `client_id`).
- `policies` → `clients` (many-to-one); `assets` → `policies` (many-to-one, zero-or-more, only where the
  product requires asset-level claiming); `claims` → `policies` + optionally `assets` + `clients`;
  `documents` → mostly `claims`, with the Policy Schedule Document the one type linking directly to
  `policies` instead (per the BRD's own ERD note).

## 7. Authentication & Authorisation

**Authentication** — Supabase Auth, directly from each frontend:

- Email + password login, followed by a mandatory MFA (TOTP) challenge before a session is granted
  "productive" access — satisfying NFR-01. Both apps use Supabase's own client SDK for the
  login/enrolment/challenge flow rather than custom-built MFA.
- A **Supabase Auth Hook ("Customize Access Token" hook)** — a Postgres function — injects the user's
  `role` and, where applicable, `client_id`/assigned-client scope into the issued JWT's custom claims at
  token-issuance time. This keeps NestJS Guards and Postgres RLS policies reading from the same source of
  truth without an extra database round-trip per request.
- NestJS verifies the JWT's signature (via Supabase's JWKS/JWT secret) on every request through a Passport
  JWT strategy, and additionally checks the token's Authenticator Assurance Level claim is `aal2` (MFA
  satisfied) before allowing any business endpoint to execute — an `aal1`-only session is rejected.

**Authorisation** — enforced primarily in NestJS, mirrored (not replaced) by Postgres RLS:

- A `RolesGuard` restricts each endpoint to the roles permitted by
  [`../02-requirements/user-roles.md`](../02-requirements/user-roles.md)'s access matrix (e.g. Company &
  Report Settings endpoints reject every role except Administrator).
- A `ScopeGuard`/ownership check enforces the "own scope" rules from the same matrix — a Broker/User may
  only act on claims/clients where `client.assigned_broker_user_id` matches them; a Client-Primary/
  Secondary may only act on their own `client_id`'s records; Administrator/Manager bypass scope checks
  entirely (full visibility).
- RLS policies in Postgres re-express the same rules as a second, independent layer (see
  [§6](#6-supabase--database-architecture)).

## 8. Role & Permission Model

The five roles and their access matrix are fully specified in
[`../02-requirements/user-roles.md`](../02-requirements/user-roles.md) and are **not redefined here** —
this section states only how that model is *implemented*, per the linking-not-duplicating convention this
docs set follows.

- Role is a fixed enum (`Administrator | Manager | BrokerUser | ClientPrimary | ClientSecondary`) stored on
  the user record and mirrored into the JWT (see [§7](#7-authentication--authorisation)).
- "Own scope" for a Broker/User is derived from `client.assigned_broker_user_id`, not a separate
  many-to-many assignment table — FR-10 describes a single active assignment per client, and the BRD's
  reassignment scenario (Manager/Administrator picking up a claim when the assigned Broker is unavailable)
  is handled by Manager/Administrator's full-visibility access rather than a temporary multi-assignment
  mechanism.
- "Own scope" for a Client-Primary/Secondary is simply their `client_id` — both roles have identical
  permissions (per [`user-roles.md`](../02-requirements/user-roles.md)), differing only in which one is
  the fixed addressee for generated reports and lodgement notifications (FR-29, FR-33) — an administrative
  flag on the user record (`isPrimaryContact`), not a permission difference.
- The maximum-2-active-users-per-client rule (business rule #14) is enforced in `UsersModule` at
  creation/activation time.

## 9. Frontend ↔ NestJS Communication

- **Protocol**: HTTPS/REST, JSON payloads, versioned under `/api/v1`. REST (not GraphQL) is the pragmatic
  choice — the domain is a set of well-defined resources (claims, clients, policies, documents) with no
  requirement calling for flexible/nested client-driven queries, and REST keeps the API simpler to secure,
  cache, and reason about at this scale.
- **Auth**: every request from either frontend carries the Supabase session's JWT as a `Bearer` token.
- **Validation**: request/response shapes are defined once in `packages/shared-types` (Zod schemas), used
  for client-side form validation in both Next.js apps and re-validated server-side in NestJS via the same
  schemas (never trust client-side validation alone).
- **File uploads/downloads**: the frontend never uploads a file's bytes to NestJS or fetches them from
  Storage directly. NestJS issues a short-lived signed Supabase Storage URL (upload or download), the
  frontend transfers the bytes directly to/from Storage, and then confirms completion to NestJS so the
  `DocumentsModule` can record the metadata (uploader, timestamp, claim link) required for FR-04's
  labelling requirement and the audit trail. This keeps large file transfers off the NestJS process
  entirely, which matters given NFR-04 flags document *volume* (not size) as the performance-sensitive
  dimension.

## 10. NestJS ↔ Supabase Communication

- **Postgres**: NestJS connects via Prisma using Supabase's connection-pooler endpoint (Supavisor), using
  the **service role** — i.e. NestJS is the only component with elevated database access, and RLS (see
  [§6](#6-supabase--database-architecture)) exists as a safeguard behind that, not as the primary gate.
- **Auth**: NestJS uses the Supabase Admin API only for account-lifecycle operations that must be
  driven server-side (e.g. Administrator deactivating a user, per FR-17) — never for reading another
  user's session.
- **Storage**: NestJS uses the Supabase Storage Admin API to generate signed upload/download URLs (see
  [§9](#9-frontend--nestjs-communication)) and, for report generation, to write the generated workbook
  directly into Storage server-side.

## 11. Shared Packages

| Package | Shared between | Contains | Explicitly does not contain |
|---|---|---|---|
| `shared-types` | Admin, Portal, API | DTOs, enums, Zod schemas | Any business logic — schemas validate shape, they don't decide whether a transition is allowed |
| `ui` | Admin, Portal | Design tokens, primitive components (buttons, inputs, tables, status badges, file uploader) | Page-level layouts, workflow components, or anything specific to one app's data model |
| `utils` | Admin, Portal, (partially API for display-only helpers) | Pure formatting helpers (currency, date display) | Calculated business values (underwriting year, net claim, loss ratio) — those are computed once in the API, never duplicated client-side |
| `eslint-config`, `tsconfig` | All apps/packages | Shared lint/TS config presets | — |

## 12. Environment & Configuration Strategy

- Three environments: **local** (Supabase CLI-emulated stack via Docker), **staging**, **production** —
  each a fully separate Supabase project (separate Postgres instance, Auth user pool, and Storage
  buckets), never sharing data.
- Each app has its own `.env` file with a committed `.env.example`. Secrets that must never reach a
  frontend bundle (Supabase service-role key, EmailJS Private Key/Service ID, database connection string)
  live **only** in `apps/api`'s environment. The two Next.js apps hold only the Supabase project URL and
  anon/public key (needed for direct Auth calls) plus the NestJS API's base URL.
- NestJS validates its environment at boot (`@nestjs/config` + a Zod schema) and fails fast if a required
  variable is missing, rather than surfacing a runtime error later.

## 13. Document/File Storage

Realises FR-03/FR-04 and the BRD's own framing (data-requirements.md §12.7 note) that documents are held
centrally rather than embedded in each record type, while still appearing as "belonging to" a Claim or
Policy in the UI.

- **Supabase Storage buckets**, logically separated: `claim-documents`, `policy-schedules`,
  `generated-reports`, `company-branding` (logos).
- Every stored file has a corresponding `documents` metadata row (Document ID, linked Claim/Policy,
  Document Type, Uploaded By, Upload Date/Time — per data-requirements.md §12.7) — the file in Storage and
  its metadata row are always created/confirmed together via `DocumentsModule`.
- Access is never via a public bucket URL — every download is a signed, short-lived URL issued by NestJS
  after an authorisation check, so a Client user cannot enumerate or guess their way to another
  organisation's documents even if Storage URLs were somehow observed.
- Generated reports (FR-28) are written once and never overwritten — regenerating for the same period
  creates a new object and a new metadata row, satisfying the "remains retrievable and unchanged
  thereafter" requirement.

## 14. API Structure

- Base path `/api/v1`, resource-oriented REST endpoints per module (e.g. `POST /claims`,
  `GET /claims/:id`, `PATCH /claims/:id/decision`, `POST /claims/:id/documents/upload-url`,
  `GET /clients/:id/reports/claims-history`).
- Consistent envelope for errors (code, message, field-level validation errors) so both frontends can
  render errors uniformly using shared types.
- Pagination/filtering on list endpoints (claims list, audit trail, communication log) via standard
  query parameters — no bespoke query language, consistent with keeping this a simple REST API.
- OpenAPI/Swagger generated directly from NestJS decorators, published to
  [`../05-api/`](../05-api/) once the API takes shape, so the contract is generated from code rather than
  hand-maintained separately.

## 15. Security Considerations

- **MFA everywhere** (NFR-01) — see [§7](#7-authentication--authorisation).
- **Least-privilege RBAC** (NFR-02) — see [§8](#8-role--permission-model), enforced twice (NestJS Guards +
  Postgres RLS).
- **Encryption in transit and at rest** (NFR-03) — HTTPS/TLS 1.2+ everywhere; Supabase encrypts Postgres
  and Storage at rest by default.
- **Multi-tenant data isolation** between SOE clients is the single most safety-critical property of this
  system given it's a broker platform serving competing/unrelated client organisations — enforced at both
  the API and database layer, never left to frontend logic alone.
- **Secrets never reach the frontend bundle** — see [§12](#12-environment--configuration-strategy). This
  includes the EmailJS Private Key — email is always sent server-side from `NotificationsModule`, never via
  EmailJS's browser-side (Public Key) flow.
- **Signed, short-lived URLs only** for document access — see [§13](#13-documentfile-storage).
- **Immutable audit trail** (NFR-08) — see [§16](#16-audit-logging).
- **Admin app is not "the same app with extra buttons"** — see [§20](#20-whats-shared-vs-isolated-vs-deferred-summary)
  on why it is a fully separate deployment, reducing the blast radius of any Portal-side vulnerability.
- A penetration test prior to go-live is recommended per NFR-03's verification method, and should be
  scheduled once the build is feature-complete — this is a project-management action, not an architecture
  decision, and is tracked in [`../09-project-management/risk-register.md`](../09-project-management/risk-register.md).

## 16. Audit Logging

Realises FR-18/NFR-08/NFR-09 (immutable, 5-year-minimum, filterable-by-Broker audit trail).

- Implemented as a **Prisma middleware** (`$use`) wrapping every create/update operation across the
  business tables, rather than requiring each module/handler to remember to log — this removes the
  single biggest risk to an audit trail's completeness (a developer forgetting to call it somewhere).
- Each entry captures: actor (user id + role), action, entity type + id, a diff of changed fields, and a
  timestamp — written to an append-only `audit_log` table.
- **Immutability enforced at the database layer, not just by convention**: the application's database role
  is granted `INSERT` only on `audit_log`; no `UPDATE`/`DELETE` grant exists for any role, and an RLS
  policy denies both regardless. This means immutability holds even against a bug in NestJS, not only
  against a well-behaved API.
- Visibility follows the role model (§8): Broker/User sees only their own actions/claims; Administrator/
  Manager see all, filterable by responsible Broker (FR-18).
- Retention: no delete path exists for `audit_log` rows within the 5-year window; a scheduled review (not
  an automated purge) is the recommended process once the exact retention/backup policy is agreed (see
  [Q-006](../02-requirements/open-questions.md#q-006-performance-availability-backup-and-recovery-targets)).

## 17. Testing Structure

| Layer | Tool | Scope |
|---|---|---|
| Unit | Jest (NestJS) | Business-rule logic in isolation: underwriting-year derivation (FR-23), net claim calculation (FR-25), late-reported flagging (FR-19), loss-ratio calculation (FR-16), progress classification (FR-30). |
| Unit | Jest + React Testing Library | Shared `ui` package components; app-level component logic in each Next.js app. |
| Integration/API | Jest + Supertest (NestJS e2e) | Role/scope enforcement (business rules #5, #14, #17), claim-status transition rules (business rules #6, #8–#11), against a real test Postgres instance (not mocked), so RLS and Prisma middleware are exercised too. |
| End-to-end | Playwright | Golden-path flows per app: Client submits a claim → uploads documents → tracks status (Portal); Broker processes a claim through to closure, generates a report (Admin). Mirrors [`../07-testing/test-cases.md`](../07-testing/test-cases.md) UC-01–UC-15. |
| Report fidelity | Jest, comparing generated workbook structure | Automated check that a generated workbook's tab names/order, header cell addresses, and column layout match the BRD's Appendix H exactly (FR-32) — this is a correctness requirement precise enough to assert on directly, not just to eyeball. |

CI runs lint, typecheck, unit, and integration tests on every pull request; end-to-end tests run against a
deployed preview environment before merge to `main`.

## 18. Development & Deployment Structure

- **NestJS API**: containerised (Dockerfile), deployable to any container host. Runs as a persistent
  process (not a serverless function) so the scheduled reminder job (§5) has a stable execution context.
- **Next.js apps**: two independent build/deploy pipelines (one per app), each capable of shipping without
  redeploying the other or the API — this is the direct benefit of keeping Admin and Portal as separate
  applications rather than one app with role-gated routes.
- **Database migrations**: Prisma migrations run as an explicit deploy step against the target Supabase
  project, before the new API version is switched into traffic.
- **CI/CD**: a single pipeline definition per app (lint → typecheck → test → build → deploy), triggered on
  merge to `main`, with preview deployments per pull request for both Next.js apps.
- **Hosting/data-residency is explicitly not decided here** — see
  [§19](#19-local-development-workflow) and [Q-008](../02-requirements/open-questions.md#q-008-hosting--data-residency)
  in [§20](#20-whats-shared-vs-isolated-vs-deferred-summary). The architecture above (containerised API,
  standard Postgres via Supabase, static-friendly Next.js apps) is deliberately hosting-agnostic so that
  decision can be made independently once data-residency requirements are confirmed, without redesigning
  anything described in this document.

## 19. Local Development Workflow

1. `supabase start` (Supabase CLI) spins up local Postgres, Auth, and Storage in Docker — no dependency on
   a live cloud project for day-to-day development.
2. `pnpm install` at the repo root (pnpm workspaces).
3. `prisma migrate dev` applies the schema to the local Supabase Postgres instance.
4. `pnpm run seed` loads reference data (the 29 products and their document requirements, the status/colour
   map, default settings) and a set of sample per-role test users (one of each of the five roles) so a
   developer can manually exercise every permission boundary locally.
5. `turbo run dev` starts all three apps concurrently (Admin on one port, Portal on another, NestJS API on
   a third), each pointed at the local Supabase instance.
6. Test users authenticate against local Supabase Auth exactly as they would in staging/production,
   including the MFA enrolment flow, so auth behaviour is never mocked away during development.

## 20. What's Shared vs Isolated vs Deferred (Summary)

### 20.1 Shared between Admin and User applications

- The NestJS API and its entire business-logic/authorisation/audit layer.
- `packages/shared-types` (DTOs, enums, Zod validation schemas) — the contract both apps and the API agree
  on.
- `packages/ui`'s design tokens and primitive components only (not page-level or workflow components).
- `packages/utils`'s pure display-formatting helpers.
- The authentication mechanism (Supabase Auth + JWT + MFA) and its custom-claims convention.
- Build/lint/test tooling and CI pipeline shape.

### 20.2 What must remain isolated between Admin and User applications

- **Separate Next.js applications and deployments**, not one app with role-gated routes — so a
  vulnerability, outage, or bad deploy in one never affects the other, and each can ship independently.
- **Separate page-level/workflow components** — the two audiences' UX needs are genuinely different
  (dense, multi-client operational tooling vs a simple, infrequent-use self-service dashboard, per
  NFR-10), so sharing beyond primitives would force a false compromise on both.
- **App-level login gating**: even though both apps authenticate against the same Supabase Auth user pool,
  each app must independently refuse to render for a role it isn't built for (the Admin app rejects
  Client-Primary/Secondary sessions; the Portal rejects internal roles) as a UX/defence-in-depth measure —
  the authoritative check remains the NestJS API, but a clear failure at the frontend avoids a confusing
  half-working experience for a misrouted session.
- Any environment secrets specific to one app's build.

### 20.3 What belongs in NestJS vs the Next.js applications

- **NestJS**: all business logic and validation, all authorisation decisions, all calculated values
  (underwriting year, net claim, loss ratio, late-reported flag, progress), all persistence, all audit
  logging, report generation, outbound email and the reminder engine, all Supabase service-role access.
- **Next.js (both apps)**: presentation, routing, forms and client-side validation (mirroring, not
  replacing, server-side validation), calling the NestJS API for every business read/write, and calling
  Supabase Auth directly only for login/MFA/session management.

### 20.4 What should be handled directly by Supabase

- Authentication, session management, and MFA (TOTP) enrolment/challenge.
- Postgres as the relational data store (schema owned by Prisma).
- Object storage for documents, logos, and generated reports, accessed only via signed URLs issued by
  NestJS.
- Built-in backup/point-in-time recovery, as the default answer to NFR-07 pending the exact RPO/RTO
  target (Q-006).
- Row-Level Security as the second authorisation layer described in [§6](#6-supabase--database-architecture)/[§8](#8-role--permission-model).

### 20.5 Architectural decisions deferred until implementation or further requirements

These are flagged, not resolved, because the BRD itself leaves them open — see
[`../02-requirements/open-questions.md`](../02-requirements/open-questions.md) for full context on each.

| Deferred item | Depends on | Why it doesn't block this architecture |
|---|---|---|
| Hosting provider & region / data residency | [Q-008](../02-requirements/open-questions.md#q-008-hosting--data-residency) | The design above (containerised NestJS, standard Postgres, static-friendly Next.js) works under any hosting answer; committing to a specific provider now would be guessing at a decision the Client hasn't made. |
| Exact uptime SLA, backup frequency, RPO/RTO | [Q-006](../02-requirements/open-questions.md#q-006-performance-availability-backup-and-recovery-targets), NFR-06/NFR-07 | Supabase's default backup/PITR capability is the pragmatic starting point; the specific retention window is a target to configure once agreed, not a structural decision. |
| Concurrent-user / document-volume benchmarks | [Q-006](../02-requirements/open-questions.md#q-006-performance-availability-backup-and-recovery-targets), NFR-04 | The signed-URL direct-to-Storage upload pattern (§13) is designed to scale with volume; a specific load-test target is needed to confirm it's sufficient, but doesn't change the pattern chosen. |
| Exact RLS policy set per table | §6, §8 | The *approach* (RLS as defense-in-depth, mirroring NestJS scope rules, driven by JWT custom claims) is decided; the literal SQL per table is implementation work once the schema is finalised. |
| Two-way email sync (inbound replies into the portal) | [Q-004](../02-requirements/open-questions.md#q-004-two-way-email-synchronisation-feasibility) | Out of Phase 1 scope. If approved later, it adds an inbound-processing capability to `NotificationsModule`/`CommunicationModule` without requiring a redesign of the modules described here. |
| Microsoft 365 integration scope beyond outbound email; potential insurer integration | [Q-005](../02-requirements/open-questions.md#q-005-microsoft-365-integration-scope-phase-2) | Explicitly Phase 2. The module boundaries here (a distinct `NotificationsModule`, a versioned `/api/v1` REST surface) are exactly what NFR-12 asks for to make such additions possible without re-architecture — but the scope itself isn't defined yet, so nothing further is designed against it now. |
| Which mailbox EmailJS is connected to, and its sending-volume plan/limits | Broker's own mailbox/EmailJS account setup | The provider and module boundary (`NotificationsModule` → EmailJS, §5) are decided; connecting the Broker's actual mailbox as the EmailJS email service, and confirming its plan covers expected reminder/notification volume (NFR-04's volume concern applies to outbound email too), is a build-phase setup task, not an architecture choice. |
| Product-specific claims workflow variations | [Q-001](../02-requirements/open-questions.md#q-001-which-of-the-29-products-follow-the-standard-claims-workflow-vs-requiring-product-specific-variations) | The document *requirements* per product are already modelled as admin-maintainable reference data (`ProductConfigModule`), so that part is future-proofed. Any per-product *workflow/state-machine* variation (beyond documents) is not designed here, since the BRD doesn't yet confirm whether any product needs one. |
| Whether a "responsible claims handler" field is needed beyond the fixed Broker assignment | [Q-010](../02-requirements/open-questions.md#q-010-responsible-claims-handler-field-for-continuity-planning) | A small additive schema change (a nullable field + minor `ClaimsModule` logic) if confirmed — doesn't affect the architecture. |
| Whether the 2-user-per-client limit needs an exception mechanism for larger clients | [Q-009](../02-requirements/open-questions.md#q-009-client-user-limit-edge-cases) | Current enforcement point (`UsersModule`) is the right place for this regardless of the answer. |

---

*Next steps once this document is reviewed: split out `tech-stack.md` (with specific package/library
versions), `data-flow.md`, and `integrations.md` per this folder's suggested structure if the team wants
finer-grained documents; begin `../04-design/database/README.md` (physical schema, informed by
[`../02-requirements/data-requirements.md`](../02-requirements/data-requirements.md)); and open the first
`adr/` entries for any of the choices above the team wants to record as formally decided.*
