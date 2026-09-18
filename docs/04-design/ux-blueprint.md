# Aris Brokers Claims Management Portal — UX Blueprint

> Living document. Sections 1-6 authored by UX Research (product/user/role/journey/task discovery).
> Later sections (Information Architecture, Navigation, Portal Layouts, Screen Inventory, Final Blueprint)
> are appended by the UX Architect and UI Designer in later passes — do not assume they exist yet.
>
> Source grounding: `docs/01-overview/*`, `docs/02-requirements/*`, `docs/03-architecture/system-architecture.md`
> (BRD v0.7, 17 Sep 2026; architecture approved 2026-09-17). No visual/UI design content appears below —
> that is explicitly deferred to the UI Designer's later pass. Where the BRD is silent or open, this is
> flagged inline as **[OPEN]** rather than resolved by assumption.

---

## Document Status

This document is now the **complete, consolidated UX blueprint** from all three specialists in this
project's pre-visual UX discovery process: UX Research (Sections 1–6) → UX Architecture (Sections 7–22,
including the previously-missing Section 13, Dashboard Architecture) → UI Design's Screen Inventory,
Decision Record, and Open-Item Consolidation (Sections 23–26). It contains **no visual/UI design content**
(no colours, typography, spacing tokens, or mockups) by design — that work begins from this document as its
input, prioritised by the Critical-tier screens identified in Section 23 and the sequencing set out in
Section 26. Nothing in Sections 1–22 has been altered or contradicted below; open items and recommendations
raised throughout are carried forward, not silently resolved.

---

## 1. Product UX Summary

### 1.1 The problem being solved

Aris Brokers runs its entire commercial-lines claims lifecycle today by email, phone, and WhatsApp, with
documents scattered across a OneDrive-like repository ("Lucid"). This produces three concrete, named
failure modes (per `vision.md`):

- **No visibility** — SOE client contacts cannot see claim progress and may see no change in status for
  weeks.
- **No traceability** — there is no consolidated, timestamped, auditable record of what happened to a
  claim, when, and by whom.
- **No scalability** — the process cannot grow with the client base, and several SOE clients now require
  their broker to run a dedicated digital claims platform as a condition of the relationship.

The portal's job is to digitise **lodgement → tracking → documents → insurer decision → settlement →
closure** for Phase 1, while broker-to-insurer correspondence deliberately stays on email (this is a
scope boundary, not a gap — see `scope.md`).

### 1.2 Who it's for

Two audiences, one shared backend, two separate applications (architecturally decided, not a UX choice —
see `03-architecture/system-architecture.md` §1–4):

- **Aris Brokers internal staff** (Administrator ×1, Manager ×1, Broker/User ×~10) — the **Admin Portal**.
  A dense, operational, multi-client tool used daily, by people who already understand claims.
- **SOE client contacts** (Client-Primary and Client-Secondary, up to 2 per client org, ~14–15 orgs, ~28–30
  accounts) — the **User/Client Portal**. An infrequent-use, low-tech-literacy self-service dashboard.
  NFR-10 explicitly flags these users as having limited familiarity with claims portals and needing
  minimal training — this is a hard usability constraint on the Client Portal, not a nice-to-have.

### 1.3 Business objectives (why this exists)

From `vision.md`, in the Client's own stated priority order:

1. Reduce reliance on manual, email-based claims handling (operational efficiency).
2. Give clients real-time, self-service status visibility (replacing "no change for weeks").
3. Strengthen FSCA compliance via a defensible, timestamped, 5-year-retained audit trail.
4. Support client retention/competitiveness — SOE clients are asking for this platform to exist.
5. Centralise documents/correspondence per claim, off email and Lucid.
6. Enable accurate, self-service client reporting without manual compilation.
7. Position for Phase 2 digital transformation (M365 integration, potential insurer integration) —
   explicitly deferred, not designed against now.

### 1.4 User objectives (why users show up)

- **Client contacts**: report a loss quickly with minimal system knowledge, know where their claim stands
  without phoning the broker, get paid/settled, and (occasionally) pull a report for their own management.
- **Brokers**: process a queue of claims across many clients efficiently, keep the insurer conversation
  moving (via email, but tracked on the claim), keep clients informed without individually emailing each
  one, and stay audit-clean.
- **Manager**: oversight across all brokers/clients, ability to pick up dropped claims, reporting for
  client relationship management.
- **Administrator**: keep the system, users, product/document reference data, and company/report branding
  correctly configured; the only role with no Phase-1-imposed restriction.

### 1.5 Most important outcomes

Ranked by what the BRD treats as non-negotiable (High-priority FRs, Objective 1–3):

1. A claim can be lodged (self-service or broker-assisted) and reach the Broker's queue reliably.
2. A client can see current status without contacting the Broker (this is the single most-cited pain
   point being solved — see 1.1).
3. Every material action is captured in an immutable audit trail.
4. The insurer-decision → settlement → closure sub-flow is unambiguous and cannot be short-circuited
   (e.g. cannot close a Settled claim without Proof of Payment; cannot save Repudiated without a reason).
5. The two report workbooks reproduce the Client's existing templates byte-for-byte, so clients trust the
   numbers on sight.

### 1.6 Core modules/features and how they relate

The BRD's own module framing (Section 4, and mirrored in the NestJS module list) maps directly onto UX
concerns:

| Module (BRD framing) | What it is, UX-wise | Depends on |
|---|---|---|
| Claim Registration | Entry point for both lodgement paths (self-service, broker-assisted) | Client/Policy/Asset data must exist first |
| Document Checklist & Management | Advisory checklist generated per product/claim type; upload/download | Claim exists; product reference data configured |
| Claims Tracking / Status | The client-facing dashboard promise (Objective 2); also the broker's queue view | Status stage set (open item, Q-003) |
| Insurer Liaison Recording | Broker captures insurer claim number, assessor details — the claim record's "mirror" of an offline email conversation | Claim submitted to insurer |
| Insurer Decision & Settlement (AOL / excess invoice) | The highest-stakes sub-flow: three decision outcomes, two settlement sub-paths | Assessment complete (offline) |
| Claim Closure & Archival | Terminal state; comments-only afterward; 5-year retention | Outcome-specific closure documents present |
| Client-Broker Communication Log | Claim-scoped conversational thread, distinct from reportable comment fields | Claim exists |
| Client & Policy Management | Reference data: clients, policies, sections, assets, insurers, risk premium | Prerequisite to almost everything else |
| User & Access Management | Administrator-only; account lifecycle, role assignment, 2-user-per-client rule | — |
| Reporting (Claims History, Performance) | Two downloadable workbooks reproducing client templates exactly | All claim/policy/financial data present and correct |
| Audit Trail | Cross-cutting, not a workflow of its own — a lens onto every other module | Everything else (it observes, doesn't originate) |
| Notifications & Reminders | Background service, **no dedicated screen** — its only UI surface is a banner/prompt on the Client dashboard | Outbound email only, per FR-07 |
| Company & Report Settings | Administrator-only configuration that feeds report branding/legends | — |

### 1.7 Mission-critical vs. secondary workflows

**Mission-critical** (Phase 1 fails without these, High-priority FRs):

- Claim submission — both self-service (UC-01) and broker-assisted (UC-15)
- Document upload cycle (UC-02)
- Claim status tracking (UC-03)
- Process/forward claim to insurer (UC-04)
- Record insurer decision (UC-05)
- AOL / settlement sub-flow (UC-06)
- Claim closure (UC-07)
- Report generation (UC-08)
- Client & policy/asset record maintenance (UC-09)
- User & access management (UC-10)

**Secondary but still required** (Medium/Low-priority FRs — important, could trail go-live slightly if
forced to, per the BRD's own priority definitions):

- Late-reported flagging (UC-11) — cross-cutting advisory flag, not a screen of its own
- Client-Broker communication log (UC-12)
- Automated reminders (UC-13) — background only, no screen
- Audit trail viewer (UC-14)
- Assessor detail capture (FR-11) — three fields, accountability only

### 1.8 Ambiguities and unresolved items (do not silently resolve — flagged per BRD's `open-questions.md`)

These bear directly on UX/IA decisions the Architect will need to make and should be treated as
**pending, not decided**:

- **Q-003 — exact client-facing status wording.** The 16-stage list in `process-flow.md` is a *proposal*.
  The IA/navigation work that follows this document should not hard-code these labels as final; treat
  them as a working list.
- **Q-001 — product-specific workflow variations.** Whether any of the 29 products need a different claim
  flow (beyond a different document checklist) is unconfirmed. This document assumes one generic workflow
  across all products, per the architecture's own position (`ProductConfigModule` only varies documents,
  not the state machine) — but this is provisional pending the Client's answer.
- **Q-002 — Credit Insurance has no document list yet** (28 of 29 products documented). The document
  checklist screen must degrade gracefully (empty/placeholder state) for this one product until supplied.
- **Q-009 — 2-user-per-client limit edge cases.** Larger SOE clients wanting more than 2 stakeholders is
  an open exception-handling question; the User Portal's access model should not assume this limit is
  permanently fixed at exactly 2.
- **Q-010 — "responsible claims handler" field** for internal continuity, separate from the fixed
  Broker-to-Client assignment, is unconfirmed. If added later, it would appear somewhere in the Admin
  claim record but is not designed here.
- **Q-019 — when does a claim count as "Resolved"** on the Performance Report's Outstanding/Resolved
  split — at insurer decision, or only at formal closure? Affects report content, not navigation, but the
  Architect/Designer should not assume closure-only without flagging it back.
- **NFR-04, NFR-06, NFR-07 (Q-006)** — performance, uptime, and backup targets are undefined. Not a
  screen-design concern directly, but bears on whether any "processing/uploading" UI needs to account for
  large document volumes gracefully (NFR-04 explicitly calls out *volume*, not size, as the risk).
- **NFR-10 (usability) is itself an open item** — there is no confirmed usability target beyond "should be
  validated through UAT." This document treats NFR-10 as a firm design constraint on the Client Portal
  regardless, since it is the BRD's own stated rationale for a *separate*, simpler application.

---

## 2. User Types

Five roles exist (per `user-roles.md`), mapping to two applications. Client-Primary and Client-Secondary
are **administratively distinct but permission-identical** — this document does not invent a permissions
difference the BRD does not specify; the only distinction is that Primary is the fixed addressee for
generated reports and the broker-assisted-lodgement notification email (FR-29, FR-33).

### 2.1 Administrator

| Aspect | Detail |
|---|---|
| **Who** | Aris Brokers internal, ~1 person (proposed incumbent: David). Full system control. |
| **App** | Admin Portal |
| **Responsibilities** | User/account lifecycle and role assignment; client/policy/asset record maintenance; product & document reference-data configuration; Company & Report Settings (the *only* role with this access); claim reopen/amend (the *only* role permitted); reporting for any client; full audit trail visibility. |
| **Most common tasks** | User management (onboarding new staff/clients), settings maintenance, spot-checking claims across brokers. |
| **Highest-frequency tasks** | Likely lower day-to-day claim-processing volume than a Broker/User — this role is more custodial/configurational than transactional. |
| **Highest-risk tasks** | Reopening a closed claim (only role that can — a compliance-sensitive action against a 5-year-retained record); deactivating a user; changing Company & Report Settings (affects every generated report thereafter). |
| **Pain points (current state)** | Same as Broker (see 2.3) plus: no single place today to manage products/document lists — that knowledge currently lives informally / in Lucid folder structure. |
| **Can view/create/edit** | Everything in Phase 1 scope. |
| **Cannot access** | Nothing is functionally restricted for this role in Phase 1 (per `user-roles.md`) — the only "cannot" is actions reserved to actors outside the system (e.g. actual assessor appointment, which is the Insurer's function, never the portal's). |
| **What they should see immediately after login** | A cross-broker operational overview — likely something combining: claims needing attention across all brokers (e.g. stuck/aging claims), any pending user-access requests, and quick links to Settings and User Management. Exact home-screen composition is an Architect decision; this document only establishes that "everything, unfiltered" is the correct default scope for this role. |
| **Success looks like** | The system stays correctly configured (users, products, settings) with minimal manual intervention; compliance reviews (audit trail, retention) never surface a gap. |

### 2.2 Manager

| Aspect | Detail |
|---|---|
| **Who** | Aris Brokers internal, ~1 person (Naison, Claims Manager, is the named primary business contact — the BRD does not confirm Naison is also the system's Manager-role user, but this is the closest fit). Oversight role. |
| **App** | Admin Portal |
| **Responsibilities** | Full visibility and action rights across *all* clients/brokers (used specifically to pick up a claim when its assigned Broker is unavailable — FR-10); client/policy record creation/edit; reporting for any client; view-only on other brokers' user accounts ("view own team"). |
| **Most common tasks** | Cross-broker oversight, reassignment/pickup of stalled claims, reporting for client relationship reviews. |
| **Highest-frequency tasks** | Monitoring claim status across the whole book — this is fundamentally a "watch everything" role rather than a "process my queue" role. |
| **Highest-risk tasks** | Picking up and acting on a claim outside their normal working knowledge of that client (context-switching risk); closing a claim for a client they don't normally handle. |
| **Pain points (current state)** | Currently has no consolidated view of claim health across all ~10 brokers/~14-15 clients other than asking individually or checking Lucid/email threads — this is precisely the "no consolidated audit/visibility" gap named in `vision.md`. |
| **Can view/create/edit** | All claims (any client), Client & Policy records, reports (any client), user accounts (view-only), audit trail (view-only, all). |
| **Cannot access** | Create/edit/deactivate users (Administrator-only); reopen a closed claim (view-only on this action, unlike Administrator); Company & Report Settings. |
| **What they should see immediately after login** | A portfolio-level view: claims across all brokers/clients, likely surfaced by exception (aging, stuck, or unassigned-attention claims) rather than a flat list — this role's whole reason for existing is oversight-by-exception, not per-client processing. |
| **Success looks like** | No claim silently stalls because "the Broker is out" — Manager can always step in with full context; reporting for client conversations is fast to produce. |

### 2.3 Broker (User)

| Aspect | Detail |
|---|---|
| **Who** | Aris Brokers internal, ~10 people. Day-to-day claims handlers, each with a fixed set of assigned SOE clients (single active assignment per client, per FR-10 / architecture §6). |
| **App** | Admin Portal |
| **Responsibilities** | The core operational role: register/process claims for assigned clients, manage documents, capture insurer claim number and assessor details, record insurer decisions, run the AOL/excess-invoice cycle, close claims, maintain client/policy data for assigned clients, generate reports for assigned clients, communicate with clients. |
| **Most common tasks** | Reviewing a claims queue; uploading/checking documents against the checklist; updating status as things progress; corresponding with clients. |
| **Highest-frequency tasks** | Document handling and status updates are likely the single highest-volume, highest-frequency interaction, since every claim passes through multiple document/status touches over its lifecycle, and this Broker is managing many claims across many clients concurrently. |
| **Highest-risk tasks** | Recording the insurer decision (financially and legally consequential — repudiation reason is mandatory for a reason); managing the AOL cycle (client-facing legal document); closing a claim (irreversible for this role — only Administrator can reopen). |
| **Pain points (current state)** | This is *the* role the whole system exists to relieve: email/phone/WhatsApp intake creates rework and lost context; Lucid has no structure tying documents to claims to clients; clients calling/emailing "what's the status" is a constant interruption with no self-service alternative today; no audit trail today means disputes/queries require manually reconstructing an email trail. |
| **Can view/create/edit** | Everything above, but scoped strictly to their assigned clients (own scope, "O" in the access matrix) — except viewing (not editing) other brokers' client/policy records read-only. |
| **Cannot access** | Claims/clients not assigned to them; user management; reopening a closed claim; Company & Report Settings. |
| **What they should see immediately after login** | Their own claims queue — almost certainly organised by what needs action (documents outstanding, awaiting their input, recently updated by a client) rather than a flat alphabetical list, since this role manages *many* claims across *many* clients simultaneously (this is where bulk/triage-style UI patterns matter most — see §5). |
| **Success looks like** | Nothing falls through the cracks across a multi-client caseload; clients stop calling for status updates because they can see it themselves; every insurer-facing email has a corresponding, findable record on the claim. |

### 2.4 Client-Primary

| Aspect | Detail |
|---|---|
| **Who** | The first of up to 2 permitted contacts per SOE client organisation (~14-15 orgs). Low-to-moderate technical literacy assumed (NFR-10). |
| **App** | User/Client Portal |
| **Responsibilities** | Submit claims; upload supporting documents; track status; communicate with the assigned Broker; upload signed AOL / excess proof of payment where required; leave comments (including post-closure); generate reports for their own org. |
| **Most common tasks** | Checking claim status (this is explicitly the objective the whole Portal exists to serve for this user — Objective 2). |
| **Highest-frequency tasks** | Status-checking is likely far more frequent than claim submission itself, given claims are infrequent events for any single client org but the anxiety/need-to-know persists throughout a claim's life. |
| **Highest-risk tasks** | Submitting a claim with wrong/incomplete policy, section, or asset selection (a claim cannot be submitted without these — business rule #2); missing the 30-day reporting window (flagged, not blocked, but risks insurer rejection); signing and correctly uploading the AOL. |
| **Pain points (current state)** | Precisely the vision.md problem statement: no visibility (may see no change for weeks), no way to know if a document they sent by email actually reached the right person/file, has to phone/email the Broker for basic status. |
| **Can view/create/edit** | New claims and documents (own org's claims); reports for own org; comments (including post-closure). Documents: "E" (create/read/update, not delete) per the access matrix. |
| **Cannot access** | Any other client organisation's data; claim status/decision updates; claim closure; closed-claim edits (comments only); user management; audit trail. |
| **What they should see immediately after login** | A simple claims list/dashboard for their own org, with the Notifications & Reminders "banner" surface prominently visible (this is literally the *only* place that background reminder service has a screen — see 1.6) — e.g. "You have an unsigned Agreement of Loss awaiting action" as the first thing seen, not buried. |
| **Success looks like** | Never has to phone the Broker to ask "what's happening with my claim"; can complete a submission without training; trusts the numbers on their downloaded report. |

### 2.5 Client-Secondary

| Aspect | Detail |
|---|---|
| **Who** | The second (optional) of up to 2 permitted contacts for the same SOE client organisation — e.g., the Primary contact's manager. |
| **App** | User/Client Portal |
| **System permissions** | **Identical to Client-Primary** — per `user-roles.md`, the BRD is explicit that "the distinction between Primary and Secondary is administrative... not a difference in system permissions." This document does not invent one. |
| **Administrative distinction only** | Primary is the fixed addressee for generated reports and the broker-assisted-lodgement confirmation email (FR-29, FR-33) — Secondary is not addressed by these automated communications even though they can perform every action Primary can, including generating their own on-demand reports. |
| **Everything else** | Identical to 2.4 in tasks, pain points, access, and success criteria. |

### 2.6 Cross-role notes

- **Data isolation is the single most safety-critical UX property** across all client-facing surfaces —
  every list, search, and report must never leak another organisation's data, per NFR-02 and business
  rules #5/14/17. This is elaborated in §3.
- **MFA is mandatory for every role** (NFR-01) — this is a universal first-login/every-login experience,
  not role-specific, and should be designed once, consistently, across both apps.

---

## 3. Roles & Permissions (UX Implications)

This section does not re-derive the access matrix (that is `user-roles.md`'s job) — it translates the
*existing* matrix into interface behaviour the UX Architect will need to design against.

### 3.1 Dynamic navigation by role

Both apps already exclude entire roles by design (Admin Portal never renders for Client-Primary/Secondary
sessions and vice versa — architecture §20.2), so **within** each app, navigation should still vary by
role rather than showing-then-disabling:

- **Admin Portal**: Company & Report Settings and User & Access Management should not appear in Broker
  navigation at all (not greyed out) — these are Administrator-only per the matrix, and a Broker has no
  legitimate reason to know the affordance exists. Manager should see everything Broker sees plus
  cross-client visibility, but not Settings/User Management.
- **User Portal**: navigation is identical for Client-Primary and Client-Secondary (permissions are
  identical) — no role-based nav variation needed within this app at all, only the org-scoping described
  in 3.3.

### 3.2 Disabled/hidden affordances vs. no-permission states

Two different situations need two different treatments, and the BRD's own action verbs point to which is
which:

- **Hide entirely** where a role has "N" (no access) for a whole module — e.g. a Client user should never
  see a "Close Claim" button anywhere, not a disabled one, because closure is not a concept they take
  action on at all (business rule #12: comments only, post-closure).
- **Show but constrain** where a role has partial/conditional access on an action they otherwise
  legitimately interact with — e.g. a closed claim is still fully *visible* to a Client user (view-only),
  but every edit affordance except "leave a comment" should be absent from that view, not disabled with a
  tooltip. The comment box itself should remain genuinely live and inviting, since FR-27/business rule #21
  specifically preserve this as an ongoing channel (e.g. for subrogation or excess-refund updates) even on
  a "closed" record.
- **Reject with a clear reason, not a dead end**, for actions blocked by *business rules* rather than
  role — e.g. attempting to close a Settled claim without Proof of Payment on file (FR-13/UC-07's defined
  failed end condition), or saving a Repudiated decision without a reason (FR-12). These are not
  permission failures; the UI should communicate *what specific requirement is missing* and where to
  supply it, not a generic "action not allowed."

### 3.3 Record-level scoping UX (the Broker "own clients" pattern)

This is the pattern most likely to be gotten wrong in the Admin Portal, so it's worth stating plainly:

- A **Broker/User's** claims list, client list, and search should **only ever return their assigned
  clients** — there is no "all clients, greyed out" state for a Broker; other clients simply do not
  appear in their lists, search results, or claim-creation client-picker.
- The **exception** is Client & Policy Management, where a Broker has view-only visibility of *other*
  brokers' clients (per the matrix: "E (own clients), view others"). This means the Admin Portal's
  client list is not identically scoped everywhere — a Broker's *Claims* view is strictly own-clients-only,
  while their *Client/Policy directory* view includes everyone but marks/limits what's editable. The
  Architect should treat these as two different list behaviours, not one scoping rule applied uniformly.
- **Manager and Administrator** see everything unscoped in every module — their lists/search should never
  silently filter; this is the mechanism by which a claim gets picked up when its Broker is unavailable
  (FR-10), so the "all clients" view for these two roles is a functional requirement, not just a
  convenience.
- **Client-Primary/Secondary** are scoped to their own organisation everywhere, with no exceptions and no
  "view others read-only" carve-out (unlike Broker) — a client user should never see any indication that
  other client organisations exist in the system at all.

### 3.4 Approval/review workflow UX

Two workflows in this system function as gated review/approval sequences and deserve explicit UX
attention because each has a defined "cannot proceed without X" rule baked into the BRD itself (not
something the UX layer invents):

**Insurer-decision recording (UC-05)** — a forced-choice, three-outcome decision:
- The three outcomes (Settled / Repudiated / Within Excess) are mutually exclusive and each has different
  required follow-on fields — this reads as a branching form, not a single free-text status update.
  Selecting "Repudiated" should immediately surface the mandatory Repudiation Reason field as part of the
  same action, since the system blocks saving without it (UC-05's defined failed end condition) — the UX
  should make this requirement visible *before* a save attempt fails, not as an error message after.
- "Settled" branches again by settlement method (Cash / Repair / Replacement), each triggering a different
  downstream sub-flow (AOL vs. excess invoice) — this branching should be explicit and visible at the
  point of decision, not discovered later.
- Every path in this decision is high-stakes and irreversible-by-normal-means (later correction is "via an
  audit-logged edit by an authorised user only") — a confirmation step before committing the decision is
  warranted, distinct from a routine save.

**Claim closure (UC-07)** — a gate, not a button:
- Closure has outcome-specific prerequisites (Proof of Payment for Settled; Repudiation Reason on file for
  Repudiated) that the system validates and refuses if missing. The UX implication: the "Close Claim"
  action should show *why* it can't proceed (naming the missing item) rather than a disabled button with
  no explanation — this mirrors 3.2's "reject with a clear reason" pattern, and is the single clearest
  case of it in the whole workflow set.
- Because closure changes what a Client user can subsequently do to the record (locks to view-only +
  comments), and is retained for 5 years regardless of outcome, this is a good candidate for an explicit
  confirmation step for the Broker/Administrator performing it — final decision on modal vs. inline
  confirmation is left to the Architect (see §5's page/modal reasoning), but the *workflow* itself is a
  deliberate gate, not a passive status change.

### 3.5 What "no permission" should look like, concretely

Given the compliance-driven B2B nature of this system, a no-permission state is not a generic 403 page —
it should be legible to a non-technical user (Client side, NFR-10) and to an internal user who may
genuinely need to request access (Broker side). The Architect should design a distinct pattern for:
(a) a role attempting to reach a URL for a module hidden from their nav (should barely be reachable given
3.1, but must degrade gracefully if it happens), versus (b) a scoped user reaching a *record* that exists
but isn't theirs (e.g. a Broker guessing another client's claim URL) — this second case should read as
"not found," not "forbidden," to avoid confirming to an unauthorised user that the record exists at all
(a standard multi-tenant data-isolation practice, consistent with NFR-02's isolation intent even though
the BRD doesn't specify HTTP semantics).

---

## 4. User Goals

Synthesised from §2's per-role analysis, framed as what each role is actually trying to achieve (not what
the system lets them do):

| Role | Primary goal | Secondary goals |
|---|---|---|
| Administrator | Keep the system correctly configured so nothing blocks brokers or clients | Compliance readiness (audit trail integrity); occasional cross-client intervention (reopen) |
| Manager | Ensure no claim, anywhere in the book, silently stalls | Produce reporting for client relationship management; pick up claims when a Broker is out |
| Broker/User | Move every assigned claim toward resolution without anything falling through the cracks | Reduce client "what's the status" interruptions; maintain a clean, defensible record per claim |
| Client-Primary | Know where my claim stands, right now, without phoning anyone | Submit a claim correctly the first time; get settled/paid; produce my own reports |
| Client-Secondary | Same as Client-Primary (identical permissions) | Same as Client-Primary |

**Cross-cutting goal shared by every role**: trust that the record is accurate and complete — this is the
BRD's Objective 3 (regulatory compliance / audit trail) expressed as a user-level goal, not just a
compliance checkbox. Every role, in a different way, is trying to avoid being caught out by "the system
doesn't show what actually happened."

---

## 5. Core Tasks

Task-analysis matrix for the major workflows, synthesising Phase 5 findings. "Complexity" reflects step
count and branching per the use cases; "automation" reflects what architecture already computes
server-side (system-architecture.md §5) versus what genuinely requires human judgement.

| Task | Primary role(s) | Frequency | Complexity | Required info | Optional info | Automatable | Confirmation needed? | Page or modal/drawer (rationale, not final decision) |
|---|---|---|---|---|---|---|---|---|
| Submit new claim (self-service) | Client-Primary/Secondary | Low per-client, but the system's core intake event | Medium-high (multiple mandatory fields, conditional asset selection, checklist generation) | Policy, section, (conditional) asset, date/location of loss, narrative, claim type | Photographs, incident/police report at lodgement | In-house claim number generation; document checklist generation; underwriting-year derivation; late-reported flag | No (submission itself is not destructive) but the 30-day late flag warning should be surfaced inline before submit | Dedicated page — too many fields and conditional branches (policy→section→asset) for a modal, and this is the client's primary reason for being in the app |
| Lodge claim on behalf of client (broker-assisted) | Broker/User, Administrator | Regular — the BRD explicitly notes intake today is phone/email/WhatsApp, implying a meaningful share of claims will start this way | Medium-high, same as above plus client selection and lodgement-channel recording | Same as self-service + which Client the claim is for | Same | Same automations + lodgement channel flag + automatic Client-Primary notification email | No client confirmation step required (FR-33 is explicit on this) | Dedicated page, same reasoning as above |
| Upload supporting documents | Client (both), Broker | High — recurring throughout a claim's life, often multiple documents at once | Low-medium per upload, but checklist context adds structure | File(s), which checklist item (or ad-hoc) | Label/description | Checklist-item status flip to "Received"; audit log entry | No | Likely a drawer/panel attached to the claim view rather than a separate page — this is a repeated, in-context action, not a destination in itself (Architect to confirm) |
| Track claim status | Client (both) | Very high — likely the single most frequent action in the whole system for this role | Low (read-only) | — | — | Status stage, stage history/timeline all system-maintained | N/A | Dedicated page/dashboard — this is the primary reason a Client user opens the app at all |
| Process/forward claim to insurer (capture insurer claim no. + assessor detail) | Broker | Medium-high — every claim passes through this once, but a Broker manages many concurrently | Medium | Insurer claim number (mandatory to progress status); assessor detail (3 fields, optional) | — | Status progression gating (cannot move to "Submitted to Insurer" without insurer number) | No | Inline on the claim record — small field set, frequent touch, doesn't warrant leaving the claim view |
| Record insurer decision | Broker | Medium — once per claim, but consequential | High (three-way branch, each with different required fields — see §3.4) | Outcome (Settled/Repudiated/Within Excess) + outcome-specific fields | Supporting notes | None — this is inherently a human judgement call transcribing an offline insurer communication | **Yes** — irreversible-by-normal-means, financially consequential | Dedicated page or full-width panel — the branching complexity (3.4) argues against a compact modal |
| Manage AOL cycle (cash settlement) | Broker (issuing), Client (signing) | Only on cash-settled claims | Medium (upload → notify → client signs/uploads → broker confirms) | Unsigned AOL document; signed AOL document | — | Status progression on "signed AOL received" | Implicit — client is signing a legal document, but that confirmation happens outside the portal (physical/e-signature); the *upload* action itself doesn't need extra portal-side confirmation | Document action on the claim record (drawer/inline), not a standalone page — it's one step within claim tracking |
| Manage excess invoice cycle (repair/replacement) | Broker (issuing), Client (paying) | Only on repair/replacement-settled claims | Medium | Excess invoice; client's proof of payment | — | None | No | Same pattern as AOL — inline/drawer on the claim |
| Close claim | Broker/Administrator/Manager | Medium — once per claim, terminal | Medium (validation-gated, see §3.4) | Outcome-specific closure document already on file | — | Validation of closure prerequisites (system checks, doesn't require re-entry) | **Yes** — terminal, 5-year-retained, changes client-facing behaviour | Inline action with a confirmation step, on the claim record — doesn't warrant its own page |
| Reopen closed claim | Administrator only | Rare/exceptional | Low action, high sensitivity | Reason (implied, not explicitly required by BRD — **flag to Architect**) | — | None | **Yes**, strongly — the only role/action that can undo a compliance-retained terminal state | Confirmation-gated inline action |
| Generate & download report | Client, Broker, Manager | Low-medium, likely occasional/monthly-quarterly rather than daily | Medium (period + scope selection, then generation) | Report type, reporting period (defaults to current underwriting year), scope (consolidated vs. single policy) | — | Entire workbook compilation, all calculated fields (loss ratio, underwriting year, net claim, progress classification) — this is the most heavily automated task in the system | No (generation is non-destructive; each regeneration creates a new stored instance rather than overwriting — FR-28) | Dedicated page (Reports section) — this is a distinct destination with its own selection form, not an in-context action |
| Maintain client/policy/asset records | Administrator, Broker (own clients) | Low-medium — onboarding-driven, then occasional updates (renewals) | High — nested structure (client → policy → section → asset), many fields, financial data (risk premium) | Client details, policy number/period/insurer/section(s)/excess, assets (where product requires) | Policy schedule document upload | None — this is foundational data entry, not calculable | No routine confirmation, but deleting/deactivating a record should confirm | Dedicated pages, likely a multi-step or tabbed structure given the nesting depth (Architect's call) |
| Manage user accounts & access | Administrator only | Low frequency (onboarding/offboarding-driven) | Medium (role assignment + validation of 2-user-per-client rule) | User details, role | — | 2-user-per-client validation; MFA enrolment invitation trigger | Yes for deactivation (removes access immediately) | Dedicated page (User Management) |
| Client-broker communication | Client, Broker | Medium-high, ongoing per active claim | Low (message + submit) | Message text | — | Timestamping, email delivery trigger | No | Inline panel/thread on the claim record — conversational, contextual, not a separate destination |
| View audit trail | Administrator, Manager, Broker (own actions only) | Low frequency, spike-driven (investigation, compliance review, dispute) | Low (read/filter only) | — | Date range, action type filters | Entirely system-generated | N/A | Dedicated page/panel accessible from the claim or client record, with export |
| Late-reported flagging (cross-cutting) | System-triggered; Broker adds motivation | Automatic, whenever the 30-day threshold is breached | Low | Written motivation (Broker) | — | The flag calculation and threshold check itself | No (advisory only, never blocks) | Not a task/page of its own — a persistent visual flag on the claim wherever it appears, plus an inline field for the motivation text |

### 5.1 Where mistakes are most likely

Directly relevant given NFR-10 (non-technical client users) and the compliance stakes of getting this
wrong:

- **Client users selecting the wrong policy/section/asset at submission** — the system rule that "an item
  not on the asset register is not covered" (business rule #2) means a wrong selection isn't just
  cosmetic; it can misrepresent coverage. The submission flow should make current, valid policy/asset
  context very hard to get wrong (e.g. only showing assets that actually exist under the selected
  policy/section) rather than relying on the user to know their own fleet/asset register by heart.
- **Missing the practical difference between the Communication Log (FR-08) and the "leave a comment"
  field (FR-27)** — these are two genuinely different, similarly-shaped features on the same claim (one
  conversational/timestamped-thread, one a single reportable field that also survives closure). Client
  users are the ones most likely to reach for the wrong one, since Brokers will learn the distinction
  operationally but a client visits rarely (NFR-10). This is a naming/placement problem for the Architect
  to solve deliberately, not something to leave to be "discovered" via two similarly-labelled buttons.
- **A Broker attempting to progress status without the insurer claim number** — this is business-rule
  blocked already (FR-06), but the UI should explain *why* progression is blocked at the point of attempt,
  not just refuse silently.
- **Uploading the unsigned AOL instead of confirming the signed one was received (or vice versa)** — both
  versions are retained and the state machine cares which is which; mislabelling at upload is a plausible
  error given documents often look similar to a non-specialist. Clear labelling/prompting at each specific
  upload step (not a generic "upload document" affordance) mitigates this.
- **A Client mistaking a Repudiated/Within Excess notification for something requiring their action** —
  both outcomes are things that happen *to* the client with no portal task attached; a client unfamiliar
  with the process (NFR-10) may look for a "next step" that doesn't exist. Clear "no action needed" framing
  helps here.

### 5.2 Where bulk actions matter

The Broker role is explicitly managing **many claims across many clients** concurrently (~10 brokers
covering ~14-15 clients between them, with no stated 1:1 ratio) — this is the primary place bulk/triage UI
patterns matter:

- A Broker's claims queue benefits from filtering/sorting by what needs attention (documents outstanding,
  awaiting Broker action, recently updated by client, approaching/past the 30-day late-reporting window)
  rather than presenting a flat, undifferentiated list.
- Bulk document-checklist review across several claims at once (e.g. "which of my claims still have
  outstanding documents") is a plausible efficiency need, though the BRD does not explicitly request a
  bulk-action *button* (e.g. bulk-close, bulk-remind) — this document flags the *queue/triage view* need
  from the task analysis above, without inventing bulk mutation actions the BRD never asked for.
- Manager's cross-broker oversight view has the same "many records, need to see what needs attention"
  shape, at one level higher (across brokers rather than across clients).
- Client users, by contrast, have no bulk-action need at all — a client org has relatively few concurrent
  claims and the UX should stay simple and singular (NFR-10), not borrow Admin-Portal density patterns.

---

## 6. Major User Journeys

Each journey follows the use case's own structure (entry point, goal, steps, decisions, required info,
system feedback, errors, alternative paths, completion state), prioritised per the brief. Journeys are
written role-by-role where a workflow genuinely differs by actor (e.g. self-service vs. broker-assisted
lodgement), and once where it doesn't.

### 6.1 Journey: Submit a new claim — Client Self-Service (UC-01)

- **Entry point**: Client logs in (MFA) to the User Portal; selects "New Claim" from their dashboard.
- **Goal**: Get a loss/incident recorded against the correct policy so the claims process begins.
- **Pre-conditions**: Client authenticated; at least one active policy (and asset, if required) already
  loaded against their org.
- **Steps**: select policy/section → (conditional) select the specific asset → enter loss details (date,
  location, narrative, claim type) → attach photos/incident/police report if available → submit.
- **System-side, automatic**: validates mandatory fields; checks date of loss against the 30-day rule;
  generates the in-house claim reference number (client cannot edit this — business rule #3); generates
  the document checklist for the selected claim type/product; sets status to "Submitted"; notifies the
  assigned Broker.
- **Decisions the user makes**: which policy/section; which asset (if applicable); claim type.
- **Required info**: policy, section, (conditional) asset, date of loss, location, narrative, claim type.
- **Optional info**: photographs, incident/police report at point of lodgement.
- **System feedback**: on-screen confirmation with the claim reference number; if the 30-day threshold is
  breached, a visible "Late Reported" warning is shown at submission, not buried later (UC-11).
- **Possible errors**: incomplete mandatory fields; session timeout; system error — user is prompted to
  correct and resubmit (does not lose entered data, per UC-01's failed-condition framing, though the BRD
  doesn't specify draft-save behaviour explicitly — **flag to Architect** as a gap worth closing).
- **Alternative path**: no active policy/asset loaded — the client cannot reach this journey at all until
  Administrator/Broker onboarding (UC-09) has occurred; this should read as a clear "no policy on file,
  contact your Broker" state, not a broken form.
- **Completion state**: claim exists with status "Submitted," checklist generated, Broker notified; client
  lands on (or is directed to) the claim's tracking view.

### 6.2 Journey: Lodge a claim on behalf of a Client — Broker-Assisted (UC-15)

- **Entry point**: Broker/Administrator logs into Admin Portal (MFA); selects "New Claim."
- **Goal**: Record a loss reported by phone/email/in-person on the Client's behalf, with full traceability
  of who lodged it.
- **Pre-conditions**: Broker is assigned to the Client (or is Administrator, unrestricted); policy/asset
  data exists for that Client.
- **Steps**: select the Client (scoped to the Broker's assigned clients, or unrestricted for
  Administrator) → select policy/section/asset → enter loss details on the Client's behalf → submit.
- **System-side, automatic**: same validations/generations as 6.1, plus: records the lodging user's
  identity/role distinct from the Client the claim is registered against; sets lodgement channel =
  "Broker-Assisted"; automatically emails the Client-Primary contact confirming the claim was lodged, with
  a summary and a link — **no client confirmation step is required before the claim proceeds** (FR-33 is
  explicit on this — the UX should not add a confirmation gate that the BRD deliberately omits).
- **Decisions the user makes**: which Client (if Broker, limited to their assignment); everything else as
  in 6.1.
- **Possible errors**: Broker attempting to lodge for a Client not assigned to them (should be prevented at
  the client-picker level per §3.3, not caught as a late validation error); otherwise as 6.1.
- **Completion state**: identical to 6.1, plus lodgement channel recorded and Client-Primary notified by
  email; Broker/Administrator sees on-screen confirmation with the reference number.

### 6.3 Journey: Upload supporting documents (UC-02)

- **Entry point**: either party opens an existing claim; sees the document checklist.
- **Goal**: Get a required (or ad-hoc-requested) document attached to the claim.
- **Steps**: select the outstanding checklist item (or "Add document" for an ad-hoc request) → select
  file(s) → upload.
- **System-side, automatic**: validates file type/size (and virus-scan, if configured — not specified by
  BRD as a firm requirement, **flag as an implementation detail, not a UX decision**); stores the document;
  links it to the claim and checklist item; marks the item "Received"; logs to audit trail.
- **Required info**: the file itself; which checklist item it satisfies (or that it's an ad-hoc addition).
- **Possible errors**: unsupported format, size limit, connectivity failure — client is told to retry, with
  the specific failure reason (not a generic error).
- **Critical business rule reflected in UX**: the checklist is advisory — the interface must never present
  an outstanding checklist item as a blocker to anything else the user might want to do on the claim (e.g.
  should not disable "view status" or grey out the rest of the claim while documents are pending).
- **Completion state**: document stored, checklist item shows "Received," audit entry created.

### 6.4 Journey: Track claim status (UC-03)

- **Entry point**: Client logs in, navigates to "My Claims" (likely the default landing view given §2.4).
- **Goal**: See current status without contacting the Broker.
- **Steps**: view claims list with current status per claim → select a specific claim → view full stage
  history/timeline and outstanding items → optionally add a note/query, or view/download documents.
- **Business rules reflected**: client sees only their own org's claims; closed claims are view-only; the
  status shown must reflect the latest Broker update (i.e., no separate "client view" lag — one source of
  truth).
- **Possible errors**: no claims found (new client org, nothing lodged yet) or system unavailable — both
  need a clear, non-alarming message (this is the highest-frequency journey for this role, so its failure
  states matter disproportionately).
- **Completion state**: client has current, accurate status and history for the claim they came to check.

### 6.5 Journey: Insurer-decision + settlement sub-flow (UC-05 → UC-06 → UC-07)

This is the highest-stakes chained journey in the system and is written as one continuous flow because
the BRD itself treats it that way (UC-07's pre-conditions explicitly depend on UC-05/UC-06's outcomes).

- **Entry point**: Broker has received the Insurer's decision by email (offline) and opens the claim to
  record it.
- **Goal**: Correctly capture the insurer's outcome, drive the correct settlement sub-path, and eventually
  close the claim once everything required is on file.
- **Decision point 1 — outcome**: Settled / Repudiated / Within Excess (mutually exclusive, see §3.4).
  - **Repudiated** → mandatory Repudiation Reason (blocks save without it) → upload repudiation letter →
    Client notified → proceeds directly to closure readiness.
  - **Within Excess** → system records no-insurer-liability position → Client notified (bears the cost) →
    proceeds directly to closure readiness.
  - **Settled** → decision point 2.
- **Decision point 2 — settlement method** (Settled only): Cash vs. Repair/Replacement.
  - **Cash**: Broker uploads unsigned AOL → Client notified → Client downloads, signs (electronically or
    by print-and-sign), uploads signed copy → Broker sees "Received," forwards to Insurer by email
    (offline) → Insurer pays, issues Proof of Payment (offline) → Broker uploads Proof of Payment, shares
    with Client → **no invoicing arises on a cash settlement** (a rule the UI should not contradict by
    ever surfacing an invoice-related field on this path).
  - **Repair/Replacement**: Broker issues excess invoice to Client → Client uploads proof of payment of the
    excess → Broker records that proof of payment was forwarded to the service provider → **system
    notifies the Client only; it never corresponds with the service provider** (a boundary the UI should
    make legible — there is no "notify service provider" affordance because that party has no portal
    presence at all).
- **Reminder loop**: at any point where a required item (signed AOL, proof of payment) is outstanding, the
  Notifications engine emails the responsible party on a recurring basis until resolved or status changes
  — this loop has no dedicated screen; its only visible trace is the outstanding-item state on the claim
  itself and the Client dashboard banner (§1.6).
- **Closure gate (UC-07)**: Broker/Administrator selects "Close Claim" once outcome-specific requirements
  are met (Proof of Payment for Settled; Repudiation Reason on file for Repudiated). System validates and
  either proceeds or clearly names what's missing (§3.4).
- **Completion state**: status = "Closed"; record view-only to Client users (comments still enabled);
  Client notified of closure; retained a minimum of 5 years with full audit history.
- **Alternative/error paths**: Client disputes the AOL amount or doesn't sign — Broker liaises offline,
  status remains "Awaiting Signed AOL," flagged for follow-up (this is a legitimate, expected stall state,
  not a system error — the UI should represent it as a normal, named status rather than an anomaly).

### 6.6 Journey: Claim closure (as a standalone Broker/Administrator action)

Already covered structurally in 6.5's closure gate — called out separately here per the brief's
prioritisation, with emphasis on its role-specific entry point: Administrator/Manager can close **any**
client's claim; Broker only their own assigned clients (business rule #11). Only Administrator can later
reopen (business rule #12) — this reopen action is rare, high-sensitivity, and should require explicit
confirmation (§5's task matrix).

### 6.7 Journey: Generate & download reports (UC-08)

- **Entry point**: any eligible role navigates to "Reports."
- **Goal**: Produce an accurate, correctly-scoped workbook without manual compilation (Objective 6).
- **Steps**: select report type (Claims History / Performance) → select reporting period (defaults to
  current underwriting year, any arbitrary range permitted) → select scope (consolidated across all
  policies, or one named policy) → system compiles → download.
- **Scoping by role**: Client → own org only; Broker → assigned clients only; Manager/Administrator → any
  client (business rule #17).
- **System-side, automatic**: every calculated field (underwriting year, loss ratio, net claim, progress
  classification) is computed server-side, never in either frontend — the report the user sees is
  guaranteed consistent with the claim record itself.
- **Possible errors**: no data for the selected criteria — an empty-state message, not an error (UC-08's
  own framing).
- **Persistence**: every generated report is stored against the client record with its generation date,
  generating user, and period covered, and remains retrievable unchanged (FR-28) — regenerating for the
  same period creates a new instance rather than overwriting. The UX implication: a report history/log per
  client is a legitimate, expected feature, not an edge case.
- **Completion state**: workbook downloaded and also durably stored/retrievable later.

### 6.8 Journey: Client-Broker communication (UC-12)

- **Entry point**: either party opens the claim, selects "Add Message/Note."
- **Goal**: Exchange claim-relevant information with a retained, timestamped record.
- **Steps**: type message → submit → system timestamps, stores against the claim, notifies the recipient
  by outbound email → recipient views/responds in-portal, building a retained log.
- **Distinct from**: the separate Broker/Client "comment" fields (FR-27) — see §5.1's flagged confusion
  risk; this journey is the conversational thread, not the reportable single-field comment.
- **Completion state**: message visible to both parties in-portal and delivered to the Client's external
  email inbox.

### 6.9 Journey: User & access management (UC-10)

- **Entry point**: Administrator selects "New User" (or edits an existing one).
- **Goal**: Grant or adjust access correctly, first time, without violating the 2-user-per-client rule.
- **Steps**: capture user details → assign role → system validates role rules (including the client-user
  cap) → system sends an invitation to set up credentials and enrol in MFA → access scope applied per the
  matrix → action logged to audit trail.
- **Possible errors**: attempting to exceed the 2-user limit per client, or assign conflicting roles —
  rejected with a validation message (this is a genuine business-rule wall, not a soft warning, unless
  Q-009 changes it).
- **Completion state**: user exists with correct role/scope; MFA enrolment enforced on first login.

### 6.10 Late-reported flagging (UC-11) — cross-cutting concern

Not a standalone journey with its own entry point — this triggers automatically at claim submission or
whenever the date of loss is captured/edited, on both the self-service (6.1) and broker-assisted (6.2)
lodgement journeys. Once triggered, the "Late Reported" flag and its explanatory message ("the Insurer may
reject the claim") must remain **visible at every subsequent stage** of the claim, to both Broker and
Client, per UC-11 — this is a persistent badge/indicator requirement across every other journey in this
document wherever that specific claim appears (claims lists, the claim detail view, reports), not a
one-time warning shown only at submission.

---

*End of UX Research sections (1–6). The sections below (7–22) are the UX Architect's Information
Architecture, Navigation, Portal Layout, Core UI Pattern, States, Responsive, Accessibility and Design
System Foundation passes, grounded directly in the BRD facts read above plus the approved
`03-architecture/system-architecture.md`. Where the BRD is silent, this is a labelled **recommendation**,
not an established requirement — the UI Designer inherits these flags rather than a false sense of
certainty. Section 13 (Screen Inventory) is intentionally skipped here — it is the UI Designer's pass.*

---

## 7. Admin Information Architecture

### 7.1 Major domains / modules

The Admin Portal's domains map directly onto the NestJS feature modules already approved in
`system-architecture.md` §5 — this is not a coincidence to design around but a convenience: the module
boundaries the backend already committed to are a sound basis for the frontend's information architecture
too.

| Domain | Submodules / concerns | Primary role(s) |
|---|---|---|
| **Claims** | Claims queue/list; claim detail (status & timeline, documents, insurer & assessor info, decision & settlement, financials, communication, comments, activity); late-reported flag (cross-cutting) | Broker (own clients), Manager/Administrator (all) |
| **Clients & Policies** | Client directory; client detail (org info, contacts); policies (period, insurer, sections, excess); assets (per policy); risk premium/payment frequency (per policy per underwriting year); policy schedule documents | Administrator (all), Broker (own clients edit, others view) |
| **Reports** | Report generation (Claims History, Performance); report history/log per client (FR-28, immutable, re-downloadable) | Client-scoped by role (own/assigned/all) |
| **Audit Trail** | Global filterable log (by broker, date, action type); contextual "Activity" view scoped to one claim/client record | Administrator/Manager (all), Broker (own actions/claims) |
| **Users & Access** | User list; create/edit user + role assignment; 2-user-per-client validation (client-side accounts only); deactivate | Administrator only (Manager: view own team, read-only) |
| **Product & Document Configuration** | 29-product list; per-product document checklist reference data (admin-maintainable per `ProductConfigModule`) | Administrator only |
| **Company & Report Settings** | Company details, FSP licence, logo, disclaimer text, VAT rate label, default reminder interval, status→label→colour mapping (FR-31) | Administrator only |

### 7.2 Relationships between modules

Claims are the hub the other domains hang off: a **Claim** belongs to one **Policy** (and, where the
product requires it, one or more **Assets**), which belongs to one **Client**; every Claim carries its own
**Documents**, **Communication Log** entries, **Comment** fields (Broker + Client, FR-27), and generates
**Audit** entries as a side effect of every other action on it (the Audit module is a lens, not an
originator — Researcher §1.6). **Reports** read across Claims/Policies/Clients but don't modify them.
**Users** carry the `assigned_broker_user_id` relationship that scopes almost everything else (architecture
§6). **Product & Document Configuration** and **Company & Report Settings** are pure reference data feeding
Claims (checklist generation) and Reports (branding/status legend) respectively, and have no relationship
to each other.

### 7.3 Page hierarchy & content hierarchy

```
Dashboard (role-varying home)
├── Claims
│   ├── Claims List (queue view — scoped by role)
│   └── Claim Detail
│       ├── Overview (status, timeline, key parties — primary content)
│       ├── Documents (checklist + uploads — secondary)
│       ├── Insurer & Assessor (claim no., assessor fields — secondary)
│       ├── Decision & Settlement (branching form + AOL/excess sub-flow — secondary, high-stakes)
│       ├── Financials (gross/excess/VAT/net — secondary)
│       ├── Communication (message thread — tertiary, contextual)
│       ├── Comments (Broker/Client comment fields — tertiary)
│       └── Activity (claim-scoped audit view — tertiary)
├── Clients & Policies
│   ├── Client List
│   └── Client Detail
│       ├── Organisation & Contacts
│       ├── Policies → Sections → Assets (nested)
│       ├── Claims (this client's claims, filtered view of the global Claims list)
│       └── Activity (client-scoped audit view)
├── Reports
│   ├── Generate Report (type, period, scope)
│   └── Report History (per client, immutable instances)
├── Audit Trail (global, filterable)
├── Users & Access [Administrator; Manager view-only]
├── Product & Document Configuration [Administrator]
└── Company & Report Settings [Administrator]
```

Content hierarchy on the claim detail page (the single most important page in the Admin Portal, given §5's
task matrix): **primary** = status + who/what/when identifying info; **secondary** = documents, insurer
info, decision/settlement, financials (the working content a Broker touches daily); **tertiary** =
communication, comments, activity (present, visible, but not what a Broker opens the claim *for*).

### 7.4 Navigation hierarchy, global vs. contextual

- **Global** (always visible, role-gated per Researcher §3.1): Dashboard, Claims, Clients & Policies,
  Reports, Audit Trail, [Administrator: Users, Product Config, Settings]. Global action: **New Claim**
  (broker-assisted lodgement, UC-15), global search (§10).
- **Contextual** (appears only within a record): Process to Insurer, Record Decision, Manage AOL/Excess,
  Close Claim, Add Document, Add Comment/Message — all live on the claim detail page, never in global nav,
  since they only make sense against one specific record.

### 7.5 Where related records connect

The claim detail page's header/breadcrumb area is the connective tissue: it names and **links** the Client
(→ Client Detail), the Policy (→ Policy record), and the Asset(s) if applicable (→ Asset entry within that
policy) — a Broker should never need to leave the claim to confirm "which client/policy/asset is this,
again," but should be able to jump to that record's own detail page in one click when they need the fuller
context (e.g. checking the policy's other sections before recording a decision).

### 7.6 Where audit/history appears

Two places, deliberately, not one: (1) a **global Audit Trail page**, filterable by broker/date/action type,
for compliance review and dispute investigation (FR-18) — this is where an Administrator/Manager goes to
answer "show me everything that happened to X" across records; (2) a **contextual Activity tab** on each
claim and client record, pre-filtered to that one record, for a Broker mid-task who wants "what changed on
this claim recently" without leaving it. Both read from the same immutable `audit_log` (architecture §16) —
this is a view distinction, not a data distinction.

### 7.7 Where settings live

**Company & Report Settings** and **Product & Document Configuration** are both Administrator-only, both
pure reference/configuration data, and both live as standalone top-level nav items — not nested under a
generic "Settings" umbrella with sub-tabs, because they serve genuinely different purposes (report
branding/legend vs. claim-checklist reference data) and conflating them under one menu would obscure that
distinction for the one role who uses both. **[Recommendation]**: if the reference-data set grows in Phase 2
(e.g. more configuration areas), reassess whether a shared "Configuration" parent nav grouping earns its
keep — not warranted at Phase 1's two-item scale.

---

## 8. User Information Architecture

### 8.1 Major domains

Deliberately shallow and few, reflecting NFR-10 and the low-frequency, low-tech-literacy audience — this is
not a smaller version of the Admin IA, it is a different shape entirely, prioritised around the Client's
actual goals (Researcher §2.4, §4): know where my claim stands, submit correctly, get settled, occasionally
report.

| Domain | Submodules / concerns |
|---|---|
| **Dashboard / My Claims** | Claims list for own org; the reminder-banner surface (the *only* screen the Notifications engine has, architecture §4) |
| **Claim Detail** | Status & Timeline; Documents; Communication (messages with Broker); Comment (single field, survives closure); Settlement (AOL/excess sub-flow, appears only when relevant) |
| **New Claim** | Submission wizard (§14/§15) |
| **Reports** | Own-org report generation + history |
| **Profile** | MFA management, contact details (view-only or Administrator-edited — BRD doesn't specify; **[Recommendation]**: view-only, since user management is Administrator-only per the access matrix — a Client user changing their own contact details would otherwise create a side-channel around FR-17) |
| **Support/Help** | **[Gap flagged — see §12.7]** |

### 8.2 Page hierarchy

```
Dashboard (claims list + reminder banner)
└── Claim Detail
    ├── Status & Timeline (primary — the reason this app exists, Researcher §1.5/§2.4)
    ├── Documents (upload/download against checklist)
    ├── Communication (message thread with assigned Broker)
    ├── Comment (single reportable field)
    └── Settlement (AOL / excess invoice sub-flow — appears conditionally)
New Claim (wizard)
Reports (generate + history)
Profile
```

### 8.3 Content hierarchy

**Primary**: current status, plainly stated, first thing seen. **Secondary**: outstanding action needed
from the client (surfaced via the dashboard banner and repeated on the claim itself, e.g. "Sign your
Agreement of Loss"). **Tertiary**: document list, message thread, comment box — present and easy to find,
but not what pulls the user into the app.

### 8.4 Navigation hierarchy, global vs. contextual

Global nav is the entire nav — 3–4 items (Dashboard, New Claim, Reports, Profile). There is no meaningful
"contextual vs. global" distinction to design for at this shallow a depth; every contextual action (Upload,
Message, Comment, Sign/Upload AOL) lives directly on the one claim detail page a user is ever looking at.

### 8.5 Where related records connect

A Client user never navigates to a separate "Policy" or "Asset" record — those exist only as read-only
context *within* claim submission (policy/section/asset pickers) and, optionally, a read-only reference on
the claim detail ("this claim is against Policy #X, Section Y"). There is no client-facing Policy/Asset
directory page — the BRD gives Client users no create/edit rights on policy data (`user-roles.md`: "V (own
org)" only), so a dedicated browsable Policy module would be over-building relative to what this role
actually does with that data.

### 8.6 Where audit/history appears

It doesn't, as a dedicated feature — Client-Primary/Secondary have **no** audit trail access at all (`N` in
the access matrix). Their own "history" need is fully satisfied by the claim's Status & Timeline view,
which is the client-facing projection of the same underlying event stream the Admin side calls "audit," not
a separate feature to design.

### 8.7 Where settings live

There are no client-facing settings beyond Profile/MFA — no company-wide, no report, no product
configuration concept exists for this role, and none should be invented.

---

## 9. Sitemap

### 9.1 Admin Portal sitemap

| Route (indicative) | Page | Roles | Purpose |
|---|---|---|---|
| `/dashboard` | Dashboard | All (content varies by role, §11.2) | Role-appropriate operational overview |
| `/claims` | Claims List | All (scoped: Broker=own clients; Manager/Admin=all) | Queue/triage view |
| `/claims/new` | New Claim (broker-assisted lodgement) | Broker, Administrator | UC-15 |
| `/claims/:id` | Claim Detail (Overview) | Scoped per above | Central claim workspace |
| `/claims/:id/documents` | Documents tab | Scoped per above | Checklist + uploads |
| `/claims/:id/decision` | Decision & Settlement tab | Scoped per above | UC-05/06/07 |
| `/claims/:id/communication` | Communication tab | Scoped per above | UC-12 |
| `/claims/:id/activity` | Activity tab | Scoped per above | Claim-scoped audit view |
| `/clients` | Client List | All (scoped) | Directory |
| `/clients/:id` | Client Detail | All (scoped; Broker edits own, views others) | Org/contacts |
| `/clients/:id/policies` | Policies (→ Sections → Assets) | All (scoped) | Nested reference data |
| `/reports` | Generate Report | All (scoped) | UC-08 |
| `/reports/history` | Report History | All (scoped) | FR-28 |
| `/audit` | Audit Trail (global) | Administrator/Manager (all), Broker (own) | FR-18 |
| `/users` | User Management | Administrator (full); Manager (view-only) | UC-10, FR-17 |
| `/settings/products` | Product & Document Configuration | Administrator | `ProductConfigModule` |
| `/settings/company` | Company & Report Settings | Administrator | FR-31 |

### 9.2 User Portal sitemap

| Route (indicative) | Page | Purpose |
|---|---|---|
| `/dashboard` | Dashboard (My Claims + reminder banner) | Default landing page, UC-03 |
| `/claims/new` | New Claim (wizard) | UC-01 |
| `/claims/:id` | Claim Detail (Status & Timeline) | UC-03 |
| `/claims/:id/documents` | Documents | UC-02 |
| `/claims/:id/communication` | Communication (messages) | UC-12 |
| `/claims/:id/settlement` | Settlement (AOL/excess) — conditional, appears only if relevant | UC-06 |
| `/reports` | Generate & download reports | UC-08 |
| `/profile` | Profile / MFA | NFR-01 |

### 9.3 What's conceptually shared (though never code-shared)

Per architecture §20.2, `packages/ui` deliberately stops at primitives — there are **no shared page-level
components** between the two apps. But several **concepts** must stay conceptually identical even though
each app implements its own version, because the same underlying record is being described to two
different audiences:

- **Claim detail concept** — both apps have "a page about one claim," but Admin's is dense/multi-tab and
  User's is simple/linear (§11 vs §12). The underlying status stages, decision outcomes, and settlement
  states must render *consistently* (same meaning, same colour convention per §22) even though the layouts
  differ completely.
- **Document concept** — "upload/label/see checklist status" exists in both apps, implemented
  independently, but the checklist-item states (Outstanding/Received) must mean the same thing everywhere.
- **Status & colour concept** — the status→colour mapping is sourced from one place (Company & Report
  Settings, FR-31) and must appear identically-coloured wherever a status badge renders, in either app and
  in the downloaded reports (§22 elaborates).
- **Comment vs. Communication Log distinction** — this exists in both apps (Broker side and Client side)
  and must be labelled/placed consistently in both, precisely because it's the single most likely point of
  user confusion per the Researcher's §5.1 flag — the two apps solving this differently would make the
  confusion worse, not better.

---

## 10. Navigation Architecture

Both portals are evaluated independently against the same criteria set (modules, depth, task frequency,
roles, density, screen size, scalability, cognitive load, discoverability) — the answer is deliberately
**not** the same for both, because the inputs aren't the same.

### 10.1 Admin Portal navigation decision

| Criterion | Admin Portal reality |
|---|---|
| Number of modules | 7 top-level domains (§7.1), several role-conditional |
| Depth | 3–4 levels (module → list → detail → tab) |
| Task frequency | Very high, daily, per-user across many concurrent records |
| Roles | 3, with materially different nav visibility (§3.1) |
| Information density | High — tables, nested records, branching forms |
| Screen size | Desktop-primary (§20) |
| Scalability | Client base expected to grow (NFR-05) — module count is more likely to grow than shrink |
| Cognitive load tolerance | Higher — expert daily users who already understand claims |
| Discoverability need | Moderate — trained users, but Administrator-only items must stay legitimately hidden from Broker (§3.1) |

**Decision: persistent left sidebar (module-level) + top bar (search, profile) + contextual tabs within
record detail + breadcrumbs for nested drill-down.** Reasoning against the alternatives, since a sidebar
should never be a default assumption:

- **Top nav alone** — rejected. 7 modules plus role-conditional items (Users, Product Config, Settings)
  would either overflow a horizontal bar or force a "More" overflow menu, which actively hides the very
  items (Administrator-only settings) that need to stay *findable for the role that has them* while
  *invisible to roles that don't* — a sidebar's vertical list scales to this count without crowding, and
  role-based item removal doesn't reflow the whole bar unpredictably the way it can in a horizontal nav.
- **Tabs alone (no persistent nav)** — rejected as the top-level pattern. Tabs work well *within* a claim
  (§7.3) where the set of views is fixed and small, but using tabs for top-level modules would mean the
  user always has to return to one "home" surface to switch domains, which fights the "many claims across
  many clients, high frequency" usage pattern (Researcher §5.2) where a Broker needs fast, direct access to
  Claims vs. Clients vs. Reports without an intermediate stop.
- **Hybrid without a sidebar (top nav + tabs only)** — rejected for the same overflow reason as top-nav
  alone, just deferred one level.
- **Contextual nav only** (no persistent global nav, navigation driven entirely by in-page links) —
  rejected: this suits a very shallow app (see the User Portal decision below) but not one with 7+ modules
  and 3 roles needing different subsets of them; users would lose their bearings.
- **Search-as-primary-nav** (a search-first app with no browsable structure) — rejected as the *primary*
  pattern, but **global search is still warranted as a secondary accelerator** (§10.3): a Broker managing
  many claims across many clients benefits from jumping straight to a claim by reference number or a client
  by name, without browsing the list first.
- **Mobile nav (bottom bar/hamburger)** — not the primary pattern given desktop-primary usage (§20), but
  the sidebar should collapse to an icon rail or hamburger below tablet width as a *responsive fallback*,
  not as the intended day-to-day experience.
- **Breadcrumbs** — warranted specifically for the Clients & Policies nested structure (Client → Policy →
  Section → Asset) and for claim ↔ client/policy cross-links (§7.5), where a user can arrive several levels
  deep and needs a clear way back up, distinct from the sidebar's module-level orientation.

### 10.2 User Portal navigation decision

| Criterion | User Portal reality |
|---|---|
| Number of modules | 4 (§8.1) |
| Depth | 2 levels (list → detail) |
| Task frequency | Low/infrequent per user, but high anxiety/need-to-know in the moment (Researcher §2.4) |
| Roles | 1 functional role (Primary/Secondary are permission-identical) — no nav variation needed at all (§3.1) |
| Information density | Low — a handful of claims per org |
| Screen size | Mobile is a first-class, plausibly primary context (§20) |
| Scalability | Module count unlikely to grow materially — this app's job is narrow by design |
| Cognitive load tolerance | Low — NFR-10 is explicit that this must require minimal training |
| Discoverability need | High per-item, but low item-count makes this easy to satisfy without density |

**Decision: a minimal persistent nav (3–4 items) — top nav on larger screens, bottom nav on mobile — with
no sidebar, no tabs at the navigation level, and no breadcrumbs.** Reasoning against the alternatives:

- **Sidebar** — explicitly rejected, and not just "not chosen by default": a sidebar signals "this app has
  many sections to manage," which is the wrong message for a 4-item, occasional-use tool, and a sidebar's
  persistent screen real-estate is disproportionately expensive at mobile widths where this app must excel
  (§20). Borrowing the Admin Portal's density pattern here would directly contradict NFR-10.
  is exactly the "borrow Admin-Portal density patterns" mistake the Researcher's §5.2 warns against for
  this audience.
- **Tabs at the top level** — rejected for the same reason a sidebar is: 4 items don't need a structural
  navigation pattern at all; a simple link list/nav bar is sufficient and less visually heavy.
- **Breadcrumbs** — rejected: at 2 levels of depth (list → detail), a simple "Back to My Claims" affordance
  communicates the same thing with far less visual overhead than a breadcrumb trail, which is a pattern
  built for deeper hierarchies than this app has.
- **Contextual nav (in-page links only, no persistent nav)** — rejected: even at this shallow depth, a
  first-time non-technical user (NFR-10) benefits from a small, always-visible, unchanging set of options
  (Dashboard/New Claim/Reports/Profile) rather than having to infer where things are from page content —
  predictability matters more than minimalism here.
- **Search-nav** — rejected as a built feature: per Researcher §5.2, a client org has relatively few
  concurrent claims, so a dedicated search feature would be solving a volume problem this audience doesn't
  have; simple list filter/sort (§17) is sufficient.
- **Mobile nav** — **adopted deliberately and prominently**: a bottom nav bar (thumb-reachable, a familiar
  pattern from consumer apps this audience already uses, e.g. banking apps) is the right choice specifically
  *because* this audience is non-technical and mobile-plausible — this is the one place in either portal
  where a "consumer-app-familiar" pattern is the correct call rather than something to avoid, precisely
  because it lowers cognitive load for the target user rather than adding decoration for its own sake.

### 10.3 Summary comparison

| Pattern | Admin Portal | User Portal |
|---|---|---|
| Sidebar | Yes (primary nav) | No |
| Top nav | Yes (search, profile, secondary) | Yes (primary nav on desktop/tablet) |
| Bottom/mobile nav | Responsive fallback only | Yes, primary on mobile |
| Tabs | Yes, within claim/client detail | Minimal — only within Settlement/Documents sections if needed |
| Breadcrumbs | Yes, for nested Client→Policy→Asset and claim cross-links | No |
| Contextual nav | Yes, extensively (claim actions) | Yes, but the whole app is nearly all "contextual" given shallow depth |
| Global search | Yes (claim ref, client, policy) | No — list filter/sort only |

---

## 11. Admin Portal Layout

### 11.1 Login & auth

Standard email + password, followed by the mandatory MFA (TOTP) challenge (NFR-01) — implemented via
Supabase Auth's own SDK per architecture §7. First-login includes MFA enrolment. No app-specific variation
from the User Portal's underlying mechanism, though the two apps implement their own screens independently
(architecture §20.2) — consistency here is a design intent, not a shared component.

### 11.2 Dashboard (role-varying home)

| Role | Home content, per Researcher §2.1–§2.3 |
|---|---|
| Administrator | Cross-broker operational overview: claims needing attention across all brokers, pending access items, quick links to Settings/User Management |
| Manager | Portfolio-level exception view: aging/stuck/unassigned-attention claims across all brokers/clients |
| Broker | Own claims queue, organised by need-for-action (documents outstanding, awaiting Broker input, recently updated by client, approaching/past the 30-day late-reporting window) — **not** a flat alphabetical list |

### 11.3 Primary/secondary nav, global search

Primary nav = sidebar per §10.1. Secondary nav = tabs within claim/client detail (§7.3). Global search
(top bar) resolves a claim reference number, client name, or policy number to the relevant detail page,
scoped server-side by the same role/scope rules as every list (§3.3 of the Researcher's blueprint) — a
Broker's search must never surface another Broker's client, even by exact reference-number match.

### 11.4 Notifications surface — [Recommendation, closing a real gap]

The architecture is explicit: notifications are **outbound email only**, and the only confirmed in-app
banner surface anywhere in the system is the Client dashboard (`process-flow.md` Module 6 note;
architecture §4). Nothing in the BRD gives internal staff an in-app notification surface at all. This
leaves a real situational-awareness gap for Administrator/Manager/Broker, who otherwise would only learn
"something needs attention" by opening their email. **Recommendation**: do not invent a notification bell
or centre (that would contradict the architecture's deliberate "no in-app notification channel" position) —
instead, satisfy the same underlying need through **default, need-for-action-ordered views** (§11.2's
dashboard, and the Claims List's default sort) so that opening the app itself *is* the situational-awareness
mechanism, without adding a feature the architecture didn't plan for. This is a UX answer to an
architectural constraint, not a request to change the architecture.

### 11.5 Profile / account, Settings

Standard profile menu: name, role, MFA management, logout. **Company & Report Settings** — a separate,
Administrator-only top-level nav item (§7.7), never visible to other roles (§3.1's "hide entirely" pattern).

### 11.6 Module pages

| Module/Page | Purpose | Primary user | Primary task | Key info | Primary action(s) | Secondary action(s) | Related info | Brief states |
|---|---|---|---|---|---|---|---|---|
| Claims List | Triage/queue | Broker (own), Manager/Admin (all) | Find the claim needing attention | Ref, client, status, late flag, last updated | Open claim | Filter, sort, New Claim | — | Empty (no claims), loading (skeleton), error (retry) |
| Claim Detail — Overview | Central workspace | Broker | Assess current state, decide next action | Status, timeline, client/policy/asset links | Update status-driving fields | Navigate to tabs | Client/Policy/Asset (linked, §7.5) | Loading, error, closed-view-only (§19) |
| Claim Detail — Documents | Track checklist | Broker, Client (via own app) | Upload/confirm docs | Checklist items, Received/Outstanding | Upload, mark ad-hoc item | Download | Audit entry per upload | Empty (no checklist yet — degraded state for Credit Insurance, Q-002), partial |
| Claim Detail — Decision & Settlement | Record insurer outcome, run AOL/excess cycle | Broker | Capture decision, drive settlement | Outcome, branching fields, AOL/PoP status | Save decision (confirmation-gated), upload AOL/invoice | View history of prior edits | Financials tab | Validation-blocked (missing reason), long-running (awaiting client) |
| Claim Detail — Financials | Record claim value | Broker | Capture gross/excess/VAT | Gross, excess, VAT, net (calculated) | Save | — | Feeds Reports | Validation errors |
| Claim Detail — Communication | Talk to client | Broker, Client | Exchange claim-relevant messages | Timestamped thread | Send message | — | Distinct from Comment field (§9.3) | Empty (no messages yet) |
| Claim Detail — Activity | Audit context | All (scoped) | See what changed | Actor, action, timestamp, diff | Filter | Export | Feeds global Audit Trail | Empty |
| Clients & Policies — List | Directory | Administrator (all), Broker (own edit/others view) | Find/maintain client | Name, assigned Broker, policy count | Open client | Filter | — | Empty (new install) |
| Clients & Policies — Detail | Maintain nested reference data | Administrator, Broker (own) | Add/edit policy/section/asset | Nested Client→Policy→Section→Asset | Add policy/section/asset | Upload policy schedule | Claims for this client | Validation (nested field errors) |
| Reports — Generate | Produce workbook | Client, Broker, Manager/Admin (scoped) | Get an accurate workbook without manual compilation | Type, period, scope | Generate | — | — | Empty (no data for criteria — not an error, per UC-08) |
| Reports — History | Retrieve past reports | Same as above | Re-download a prior report | Generation date, generating user, period | Download | — | FR-28 immutability | Empty (first-ever generation) |
| Audit Trail | Compliance review, dispute investigation | Administrator/Manager (all), Broker (own) | Reconstruct what happened | Actor, action, entity, diff, timestamp | Filter, export | — | — | Empty (filtered to nothing) |
| Users & Access | Onboarding/offboarding | Administrator | Grant/adjust access correctly | User, role, client link (if Client role) | Create/edit user, deactivate (confirmation-gated) | — | 2-user-per-client validation | Validation (limit exceeded) |
| Product & Document Configuration | Keep checklists current without a dev release | Administrator | Add/amend product document lists | 29 products, per-product checklist | Edit checklist | — | Feeds claim checklist generation | Empty (Credit Insurance, Q-002) |
| Company & Report Settings | Keep report branding/legend correct | Administrator | Configure once, used everywhere | Company details, disclaimer text, status→colour map | Save | — | Feeds every generated report + in-app status badges (§22) | Validation |

### 11.7 The insurer-decision / closure approval-style flow, on this page

Already established structurally by the Researcher (§3.4, §6.5) and given its UI-pattern resolution in
§14 below — summarised here as an Admin Portal layout decision: it lives **inline on the Claim Detail
page's Decision & Settlement tab**, not as a separate page and not as a modal, because it is not a
one-shot action but an ongoing, stateful sub-flow that needs to remain visible in the claim's context over
days or weeks (§14.4 elaborates the full reasoning).

### 11.8 Bulk actions — explicitly not invented

Per the Researcher's §5.2, the BRD never requests a bulk-mutation action (bulk-close, bulk-remind,
bulk-reassign). **Decision**: the Admin Portal's claims list gets rich **filtering/sorting/triage views**
(§16) to satisfy the genuine "many claims across many clients" need, but **no bulk-select checkboxes or
bulk-action toolbar** are designed here. Adding them would be scope invention against a domain where every
mutation (a decision, a closure) is individually consequential and audit-significant — a bulk "close 12
claims" affordance would actively work against the deliberate one-claim-at-a-time gating the BRD designed
into UC-05/UC-07.

---

## 12. User Portal Layout

### 12.1 Login & auth

Same underlying mechanism as §11.1 (Supabase Auth + mandatory MFA), but every screen involved must read as
simple and reassuring, not technical — plain-language copy, large touch targets, no jargon ("enter the code
from your authenticator app," not "TOTP challenge").

### 12.2 Onboarding — [Recommendation]

Client users are provisioned by an Administrator/Broker (UC-10), not self-registered, so "onboarding" here
means: first login → set password → MFA enrolment → a brief, dismissible orientation pointing at "New
Claim" and "My Claims" (2–3 short callouts, not a forced multi-screen tour). This is a recommendation to
satisfy NFR-10's "minimal training" goal, not a BRD-specified feature — flagged accordingly so the UI
Designer knows it can be trimmed or expanded based on UAT feedback (NFR-10's own stated verification
method).

### 12.3 Home / dashboard

The claims list for the user's own org, **with the reminder-banner surface placed above the list, not
below it** — this is the single confirmed in-app surface for the entire Notifications & Reminders engine
(architecture §4), and per Researcher §2.4 it should be "the first thing seen," e.g. "You have an unsigned
Agreement of Loss awaiting action," styled as a persistent, non-dismissible-until-resolved banner (§18/§19).

### 12.4 Primary nav

Per §10.2: Dashboard, New Claim, Reports, Profile — top nav (desktop/tablet) or bottom nav (mobile, §20).

### 12.5 Tasks, mapped to where they live

| Task | Where |
|---|---|
| Submit claim | `/claims/new` wizard (§14.2) |
| Upload docs | Documents section on Claim Detail |
| Track status | Status & Timeline on Claim Detail (the default view when opening a claim) |
| Communicate with broker | Communication section on Claim Detail — visually and functionally distinct from Comment (§9.3, §12.6) |
| Sign/upload AOL or excess PoP | Settlement section on Claim Detail — appears only when the claim's decision outcome makes it relevant (§14.4) |
| Leave a comment | Single field at the foot of Claim Detail, remains available after closure (FR-27) |
| Download reports | `/reports` — separate from any individual claim, since reports are org-scoped, not claim-scoped |

### 12.6 Resolving the FR-08/FR-27 confusion risk — [Recommendation]

The Researcher flagged (§5.1) that the Communication Log (FR-08, a conversational thread) and the "leave a
comment" field (FR-27, a single reportable field that survives closure) are easy for an infrequent,
non-technical visitor to conflate. **Recommendation**: give them deliberately different visual treatments
and positions, not just different labels — the Communication thread lives mid-page as an ongoing
conversation panel (message bubbles or a simple list, with a "Send" action), while the Comment field lives
alone at the very foot of the page, framed as a single note rather than a conversation (e.g. a one-line
prompt like "Add a note to this claim" with no reply affordance). Position and shape, not just wording,
should carry the distinction, since NFR-10's audience visits rarely enough that they can't be expected to
have learned a subtle naming difference from a prior visit.

### 12.7 Support/Help — [Gap flagged]

The BRD defines no help/support module for the User Portal at all. Given NFR-10's explicit concern about
non-technical, infrequently-training users, leaving this entirely unaddressed is a risk. **Recommendation
(minimal, proportionate)**: a persistent "Need help? Contact your Broker" affordance sourced from the
Broker's contact details already held in Company & Report Settings (FR-31) — not a ticketing system, live
chat, or knowledge base, which would be disproportionate for ~28–30 total client-side accounts who each
have a named, known Broker relationship already. A short static "How claims work" reference page is a
reasonable stretch addition if UAT (NFR-10's own verification method) surfaces a need for it, but should not
be assumed necessary up front.

### 12.8 Records/data, forms, history/activity

Already covered in §8: no client-facing Policy/Asset directory (read-only context inside the submission
wizard only), no separate history feature beyond the claim's own Status & Timeline.

### 12.9 Logout / session behaviour

Standard logout. Session-timeout and any auth-related messaging must be plain-language for this app
specifically (e.g. "Your session has ended for your security — please log in again," never a technical
"401" or "token expired" string) — see §19 for the full states treatment; this is called out here because
it is a layout-visible moment (a full-screen or banner message a returning user will actually read).

---

## 13. Dashboard Architecture

### 13.0 Why this section carries more weight than usual, and what it must not become

Per §11.4 and §18, the architecture makes a deliberate choice: notifications are **outbound email only**,
with no in-app notification centre anywhere in the system (architecture §4). That leaves the dashboard as
the **only** in-app situational-awareness surface for Administrator, Manager, and Broker — and, for
Client-Primary/Secondary, additionally the default landing screen and the sole home of the one confirmed
notification surface in the entire product (the reminder banner, §12.3). This section specifies what
belongs on each role's dashboard and, just as importantly per §22.7's "no in-app data visualisation"
decision, **what does not**: loss ratios, claims-history aggregates, and other analytical/reporting content
belong on the Reports pages (§16), not here. The operative test throughout this section: a dashboard widget
answers *"what needs my attention right now"*; a report answers *"what happened, in aggregate, over a
period, for the record."* Conflating the two would duplicate the `ReportsModule`'s server-side calculation
work (architecture §5) in a second, unreviewed surface, and would undermine the "clients trust the numbers
on sight" objective (Researcher §1.5) by giving two surfaces two separate chances to disagree.

### 13.1 Administrator dashboard

- **Cross-broker "needs attention" queue**, unscoped (Administrator sees everyone, per §3.3): claims with
  documents outstanding past a configurable staleness point, claims awaiting an Insurer decision for
  unusually long, and newly late-reported claims — built from the same need-for-action filter/sort logic
  already defined for the Broker's own claims list (§16), just without the ownership scope applied.
- **Small numeric count tiles only where the number is itself actionable** and links straight into a
  pre-filtered Claims List (e.g. "7 claims with documents outstanding >14 days") — never a vanity total
  ("312 claims processed this year" is Reports content, per §13.4 below, not a dashboard tile).
- **Quick links** to Users & Access, Product & Document Configuration, and Company & Report Settings
  (§7.7) — surfaced here because this is the one role that routinely visits these configuration screens,
  and burying them behind the sidebar alone adds friction to a genuinely frequent Administrator task
  (Researcher §2.1).
- **Explicitly not invented**: any "pending access request" or approval-queue widget. The BRD defines no
  user self-registration or access-request workflow — UC-10 is entirely Administrator-initiated — so no
  such surface should appear on this dashboard.

### 13.2 Manager dashboard

- **Portfolio-level exception view**, one scope wider than a single Broker's: the same
  aging/stuck/late-reported filters as §13.1, but sortable/groupable **by Broker**, so the Manager can
  answer "which Broker's queue needs me to step in" (FR-10's pickup scenario) at a glance — this is the
  dashboard's single most important job for this role (Researcher §2.2).
- **No separate claim-processing surface here** — clicking through from any exception item lands the
  Manager in the ordinary (unscoped) Claims List/Claim Detail, identical to any other role's. The
  dashboard's only job is to point at where attention is needed, not to duplicate the claims workspace.
- **Same "no vanity metrics" rule as §13.1** — claim-count trends, loss ratios, and portfolio performance
  remain Reports content (§16), even though the Manager is also a heavy Reports consumer for
  client-relationship purposes (Researcher §2.2) — those are two different surfaces for two different jobs.

### 13.3 Broker dashboard

- For a Broker, "the dashboard" and "my claims list, sorted by need for action" are effectively **the same
  surface** (§11.2, §16), not two things to reconcile. On login, a Broker lands on their own queue, already
  sorted: documents outstanding → awaiting Broker action (e.g. an Insurer decision ready to record) →
  recently updated by the Client (unread message/comment) → approaching/past the 30-day late-reporting
  window → everything else.
- A thin summary strip above the list (count tiles, same "actionable number" rule as §13.1) is a reasonable
  enhancement — e.g. "3 claims need documents," "1 claim late-reported this week" — but must never become a
  second, disconnected surface from the sorted list itself; both should point at the same underlying
  filtered view.
- An **assigned-client summary** (how many clients, how many open claims each) is genuinely dashboard-worthy
  for this role, since it is a live operational fact a Broker managing many clients concurrently needs at a
  glance (Researcher §2.3, §5.2) — it should read as a small list/tile set, never a chart (§22.7).

### 13.4 What is explicitly *not* dashboard content, for any internal role

| Belongs on the dashboard (live, actionable, derivable from a filter/sort/count of the existing claims table) | Belongs on Reports instead (aggregate, calculated, for the record) |
|---|---|
| Claims needing action today | Loss ratio by class of insurance |
| Count of late-reported claims this week | Claims-history totals for an underwriting year |
| Which Broker's queue is stalling (Manager) | Broker/portfolio performance over a period |
| Assigned-client summary (Broker) | Outstanding/Resolved split (Performance Report, pending Q-019) |

Everything in the left column is answerable from the *current state* of the claims table with a simple
filter/sort/count (§16) — no new calculation engine required. Everything in the right column requires the
`ReportsModule`'s period-scoped, calculated-field compilation (underwriting year derivation, loss ratio, net
claim — architecture §5) and is explicitly out of scope for live, in-app rendering per §22.7's "no in-app
charts" decision. **If a number can only be produced by running a report, it is report content, not
dashboard content** — this is the test to apply to any future dashboard-widget proposal.

### 13.5 Client-Primary/Secondary dashboard

- The landing screen after login is the reminder-banner surface (§12.3, §18) at the very top — non-
  dismissible until the underlying item is resolved — followed by the org's claims list (cards, §16), each
  card showing reference, status badge, last update, and a one-line call-to-action **only** when the
  Client genuinely has something to do.
- When nothing needs the Client's attention, the banner area should not simply disappear into an unexplained
  gap — a brief, calm positive state (e.g. "Nothing needs your attention right now") is preferable to
  silence, consistent with §18's reassurance-oriented tone for this app.
- **No count tiles, no default filters, no "portfolio" framing at all** — a client org's claim volume is low
  enough (Researcher §5.2) that a plain list *is* the entire dashboard; adding Admin-style summary tiles
  here would be exactly the "borrow Admin-Portal density" mistake §10.2 warns against.
- A prominent **New Claim** action lives on the dashboard itself, not only in the nav — claim submission and
  status-checking (satisfied by the list itself) are the only two reasons this screen exists.

### 13.6 First-time / empty dashboard states

- **Internal roles**: a brand-new account, or a system with very few claims, should read as a calm, positive
  "nothing currently needs attention" state — not a broken or empty-looking screen (extending §19's
  empty-state distinction to the dashboard specifically).
- **Client**: a brand-new client org with no claims yet should see a clear "no claims yet — submit your
  first claim" prompt, distinct from "you have claims, but none currently need your attention" (§19).

---

## 14. Core Page Patterns

The table below ties each real task from the Researcher's §5 task matrix to a UI pattern, with the
reasoning made explicit rather than assumed — per the brief, no pattern is chosen "because that's how it's
usually done."

| Task | Pattern | Reasoning |
|---|---|---|
| Client claim submission (UC-01) | **Multi-step wizard** | Multi-step, conditional (policy→section→asset), occasional-use for this specific user (NFR-10) — breaking it into steps with per-step validation reduces the chance of an error discovered only at the end, which matters most for a user who visits rarely and has no institutional memory of the form |
| Broker-assisted lodgement (UC-15) | **Single scrollable page with clear sections**, not a strict wizard | Same underlying data as UC-01, but performed by a *frequent, trained* user — wizard step-friction is a cost with no corresponding benefit for an expert user processing many of these; a well-sectioned single page is faster for repeat use while still grouping fields logically |
| Claims list (Admin) | **Table** | High-density, high-volume, sortable/filterable — the canonical case for a table over cards |
| Claims list (Client) | **Cards/list, not a table** | Low volume per org (a handful of claims), non-technical audience (NFR-10) — a dense table is an Admin-density pattern that actively works against this app's simplicity goal |
| Claim detail (Admin) | **Detail view with tabs** (not accordion) | A Broker needs to jump laterally between Documents/Financials/Communication/Decision rapidly without losing place — tabs support quick lateral movement; accordion suits content that's optional/rarely opened, which none of these sections are |
| Claim detail (Client) | **Single scrollable page with clear sections**, minimal/no tabs | Shallow content (§8.3) doesn't need tab-level navigation; a linear scroll matches how little content there actually is and avoids introducing a UI concept (tabs) a rare visitor has to learn |
| Insurer decision recording (FR-12) | **Branching form with progressive disclosure** | Three mutually exclusive outcomes, each with different mandatory fields — selecting an outcome should immediately reveal its required fields (Researcher §3.4), not surface them as a post-save error |
| Settlement / AOL / excess sub-flow | **Inline panel/section on the Claim Detail page** (not a page, not a modal, not a drawer) | See §14.4 below — this is one of the most complex journeys and deserves its own reasoning |
| Client-Broker communication (UC-12) | **Inline panel/thread on the claim record** | Conversational, contextual, ongoing — not a destination in its own right |
| Document upload | **Contextual drawer/panel attached to the claim**, not a separate page | Recurring, in-context action tied to a specific checklist item — a full page-navigation round-trip for every upload would add friction to the single highest-frequency touch in a claim's life (Researcher §5) |
| Report generation | **Dedicated page** (selection form → generate → download) | A distinct destination with its own inputs (type/period/scope), not an in-context action — matches Researcher §5's own categorisation |
| Client/Policy/Asset maintenance | **Tabbed/nested structure**, dedicated pages | High field count, genuine nesting depth (Client→Policy→Section→Asset) — this is foundational data entry, not a quick action, and benefits from the same lateral-navigation argument tabs win elsewhere |
| Claim closure | **Inline action + confirmation dialog**, not a page | Terminal but single-step once prerequisites are met; the *complexity* is in the validation gate (§3.4), not in data entry, so it doesn't need a page's worth of space |
| Reopen closed claim | **Confirmation-gated inline action** | Rare, high-sensitivity, one field's worth of complexity (a reason) — a full page would overstate its size while a bare button would understate its sensitivity |
| User management | **Dedicated page**, standard create/edit form | Low frequency, but structured enough (role + validation) to warrant its own destination |
| Audit trail | **Table with filters + export** | Read-only, filter-heavy — the canonical table use case |

### 14.1 Forms & data entry — general principles

See §15.

### 14.2 Tables & data management — general principles

See §16.

### 14.3 Search & filtering — general principles

See §17.

### 14.4 Resolving the settlement/AOL/excess sub-flow's UI pattern

This is explicitly one of the most complex journeys in the system (Researcher §6.5), so the pattern
decision is argued fully rather than asserted:

- **Not a modal**: a modal implies a short-lived, single-sitting interaction. This sub-flow is the opposite
  — it spans days or weeks (Broker uploads AOL → waits for the Client to sign and re-upload → forwards to
  Insurer offline → waits for payment/PoP), with a genuine "reminder loop" (`process-flow.md` step 9/16)
  running in the background the whole time. A modal that a user opens, closes, and has to remember to
  reopen is the wrong metaphor for a stateful, long-running process.
- **Not a drawer**: a drawer is better suited to a transient, single-step action layered on top of a list
  (e.g. document upload, §14 above) — it still implies "temporary overlay," and this sub-flow's state
  (Awaiting Signed AOL, Awaiting Excess Invoice/PoP) needs to be visible *as part of* the claim's ongoing
  status picture, not tucked behind an overlay a user has to know to reopen.
- **Not a separate page**: fragmenting this into its own destination would sever it from the claim's
  Overview/Timeline/Documents context it's causally tied to (the outcome recorded in Decision & Settlement
  directly determines what appears here) — a user (especially a Client, per NFR-10) shouldn't need to
  understand that "Settlement" is conceptually a different place from "my claim."
- **Decision: a persistent inline panel/section on the Claim Detail page** (a "Settlement" tab on the Admin
  side, a "Settlement" section on the linear Client side), which **appears conditionally** — hidden entirely
  until a Settled outcome with a settlement method is recorded, then persists showing live state (e.g.
  "Awaiting Signed AOL," "Excess Invoice Issued — Awaiting Proof of Payment") until resolved. This mirrors
  how the Researcher's §6.10 already treats the Late Reported flag: a persistent, contextual indicator
  rather than a one-time interaction.

---

## 15. Forms & Data Entry

- **Claim submission wizard (Client)**: Step 1 Policy/Section → Step 2 Asset (shown only where the product
  requires it — progressive disclosure driven by product configuration, not a fixed step count) → Step 3
  Loss details (date, location, narrative, claim type) → Step 4 Attachments (optional) → Step 5 Review &
  Submit, with the 30-day late-reporting warning surfaced on the Review step if triggered — visible before
  submission, never discovered only after (Researcher §6.1).
- **Draft persistence — [Recommendation closing a flagged gap]**: the Researcher explicitly flagged (§6.1)
  that the BRD doesn't specify draft-save behaviour on a failed submission. Recommend the wizard persist
  entered data across a session-timeout or system-error interruption by default — this is a small,
  low-risk addition that directly closes a named gap rather than scope creep, and matters disproportionately
  for a non-technical, infrequent user (NFR-10) who would otherwise have to re-enter everything.
- **Insurer decision form (Admin)**: single page/panel, an outcome selector (Settled/Repudiated/Within
  Excess) with progressive disclosure of each outcome's required fields (Repudiation Reason textarea
  appearing the instant "Repudiated" is chosen; settlement-method sub-selector appearing under "Settled") —
  a direct implementation of FR-12's branching and the Researcher's §3.4 reasoning.
- **Client/Policy/Asset maintenance (Admin)**: nested tabbed structure for ongoing editing of an existing
  record (Client tab → Policies tab → per-policy Sections/Assets); a guided, sequential flow for *first-time*
  creation of a new Client (since the nesting has a natural creation order: Client → Policy → Section →
  Asset), distinct from the tabbed pattern used once the record exists.
- **Risk premium & payment frequency (FR-24)**: a small inline form embedded within the relevant policy's
  own record (per policy, per underwriting year) — not a separate page, matching its low complexity in the
  Researcher's §5 task matrix.
- **Validation conventions (both apps)**: inline, field-level validation; business-rule blocks (e.g.
  missing Insurer claim number, missing Repudiation Reason) explain *what's missing and why*, never a
  generic "invalid" message — this is the same "reject with a clear reason" principle the Researcher
  established in §3.2/§3.4, extended here to ordinary form validation.
- **Mandatory vs. optional**: visually distinguished consistently across both apps (see §22's form
  hierarchy principle).

---

## 16. Tables & Data Management

- **Admin Claims List** — columns: Claim Ref, Client, Status (badge, using the shared status→colour mapping
  from §22), Broker (Manager/Administrator view only), Late-Reported flag (persistent, per UC-11), Last
  Updated. Default sort for a Broker: need-for-action first (documents outstanding, awaiting Broker input,
  recently updated by client, approaching/past the 30-day window); Manager/Administrator get an unscoped,
  freely-sortable list, since full unfiltered visibility is itself a functional requirement for them
  (Researcher §3.3).
- **Filters**: status stage, client, broker (Manager/Administrator only), late-reported flag, date range.
- **Pagination, not infinite scroll** — the architecture's own API structure (`system-architecture.md` §14)
  explicitly paginates list endpoints via query parameters; infinite scroll would fight that contract and
  would also undermine the stable, referenceable ordering a Broker or auditor benefits from ("page 2 of the
  filtered list," not an ever-shifting feed).
- **No bulk-select/bulk-action toolbar** — per §11.8, deliberately not designed, since the BRD never
  requests bulk mutation and this domain's mutations are individually consequential.
- **Client Portal "claims list"** — a **card/list layout, not a table** (§14), each card showing: reference
  number, status badge, last update, and a one-line call-to-action only when the client has something to
  do (e.g. "Sign your Agreement of Loss") — otherwise no CTA at all, to avoid implying action is needed when
  none is (Researcher §5.1's flagged risk of a client "looking for a next step that doesn't exist" on
  Repudiated/Within Excess outcomes).
- **Audit Trail table** — read-only, filterable (date range, action type, and by responsible Broker for
  Administrator/Manager per FR-18), with export — matching the Researcher's §5 task-matrix note ("with
  export").
- **Inline editing** used for small, single-field updates (Insurer claim number, the Comment field) rather
  than a full edit-page round-trip; **full edit forms** used for structurally complex records (Client,
  Policy, Asset) given their field count and nesting (§15).

---

## 17. Search & Filtering

| Surface | Pattern | Reasoning |
|---|---|---|
| Admin global search (top bar) | Resolve claim ref / client name / policy number → detail page, scoped by role | Many claims across many clients (Researcher §5.2) justifies a fast jump-to mechanism beyond browsing lists |
| Admin Claims List | Filter (status, client, broker, late flag, date range) + sort | The triage need is real and named (Researcher §5.2); this is where it's actually satisfied, not via bulk actions |
| Admin Clients & Policies List | Filter (assigned broker, name) | Directory lookup, lower frequency than Claims |
| User Portal | List filter/sort only (status, date) — **no dedicated search feature** | Per Researcher §5.2, a client org has relatively few concurrent claims; building a search feature here solves a volume problem this audience doesn't have, and would add UI surface NFR-10 doesn't call for |
| Reports (both apps) | Not search — a generation-input form (type, period, scope) | This is claimed by the Researcher's §6.7 as a distinct, form-driven destination, not a search/filter surface |

---

## 18. Notifications & Feedback

- **Success feedback**: a transient confirmation (e.g. toast or inline banner) *plus* a persistent state
  change (status badge, checklist item flipping to "Received") — the confirmation should never be the only
  evidence something worked, since a user who navigates away shouldn't have to wonder whether their action
  "stuck."
- **Error feedback**: field-level inline where the error is attributable to one field; a page-level banner
  for system/connectivity failures — both always name the specific cause (per §3.2/§3.4's established
  principle), never a generic failure message.
- **Long-running operations**: report generation (and potentially bulk document processing, given NFR-04's
  volume concern) should show a visible pending/progress state ("Generating your report — this may take a
  moment") rather than a blocking full-page spinner with no feedback, since NFR-04 explicitly flags volume
  as the performance risk to design around even before benchmarks are confirmed.
- **Admin situational-awareness pattern**: per §11.4, the "Needs Attention" default-sorted dashboard/queue
  views are the intentional stand-in for a notification centre the architecture doesn't provide — this is
  restated here because it is fundamentally a feedback-pattern decision (how does a Broker learn something
  changed) as much as a layout one.
- **Client dashboard banner**: the one genuine notification surface in the whole system (architecture §4).
  It should be styled as a persistent, non-dismissible-until-resolved card/banner — not a toast, since a
  toast disappears and this content must survive across sessions until the underlying item (e.g. an
  unsigned AOL) is actually resolved.
- **Plain-language error/session messaging on the Client Portal specifically** (§12.9, §19) — feedback
  copy is a first-class UX concern for this app given NFR-10, not an afterthought for the UI Designer to
  patch in later.

---

## 19. States & Edge Cases

Grounded in the domain's actual constraints — several of these states are not generic UI boilerplate but
direct consequences of specific business rules (`business-rules.md`).

| State | Treatment | Grounding |
|---|---|---|
| Loading | Skeleton tables/detail panels (Admin); simple spinner/placeholder (Client, lower density) | — |
| Empty — no records yet | Distinguish "new client/org, nothing here yet" from "filtered to nothing" — different messages | UC-08's own framing: "no data" is a message, not an error |
| Empty — Credit Insurance checklist | Degrade gracefully to a placeholder/blank checklist state, not an error | Q-002 — 1 of 29 products has no document list yet |
| Error (system/connectivity) | Specific reason + retry, never generic | §3.2/§3.4 principle, extended everywhere |
| Success | Toast/banner + a persistent state change (§18) | — |
| Partial data | Never treat an incomplete checklist as blocking anything else on the claim | Business rule #4 — advisory, not blocking |
| No permission — hidden nav | A role never sees an affordance for a module it has no access to at all | Researcher §3.2 "hide entirely" |
| No permission — scoped record mismatch | Reads as **"not found," not "forbidden"** | Researcher §3.5 — avoids confirming a record's existence to an unauthorised user, consistent with NFR-02's isolation intent |
| No permission — business-rule block | Names the specific missing requirement (e.g. "Proof of Payment required before closing") | Researcher §3.2/§3.4 |
| Expired session | Plain-language on Client Portal ("please log in again"); can be more technical/terse on Admin | NFR-10 |
| Failed submission | Data is not lost — persist as a draft | §15's recommendation, closing the Researcher's flagged UC-01 gap |
| Validation errors | Inline, field-level, specific | — |
| Duplicate records — [Recommendation] | A soft warning (e.g. duplicate policy number for the same client) rather than a hard block | BRD doesn't define this; consistent with the domain's own "advisory, not blocking" cross-cutting theme (`business-rules.md`) |
| Missing info (outstanding checklist item) | Persistent, non-blocking indicator only | Business rule #4 |
| Long-running operations | Visible progress state, not a blocking spinner | NFR-04 (volume) |
| Destructive actions | **There are almost none in this domain** — audit trail is append-only/immutable (business rule #13), closed claims are never deleted (only locked to view-only + comments, business rule #12). The closest analogue is user deactivation, which should be framed as "deactivate" (with its access-removal consequence explained), never as "delete," since the record persists. | NFR-08/09, FR-13, `business-rules.md` #12–13 |
| Unsaved changes | Warn before navigating away from an in-progress submission/edit; pairs with the draft-persistence recommendation | §15 |
| First-time vs. returning (Client) | First-time gets the brief orientation (§12.2); returning lands directly on the dashboard with the reminder banner surfaced first | NFR-10 |

### 19.1 "Closed claim, view-only" as its own cross-cutting state

Called out explicitly because it recurs across nearly every screen a claim appears on, not just its detail
page: once closed (FR-13), a claim is **view-only to Client users except the Comment field** (business rule
#12) and **editable only by an Administrator** (the reopen action) for internal roles. Both apps should
render a consistent, unmissable "Closed" treatment (e.g. a status band across the top of the claim detail
page) — implemented independently per app (no shared component, architecture §20.2), but structurally
identical in *what it communicates*: this record is locked to a specific, named set of remaining actions,
not a dead end. On the Admin side, Broker/Manager/Administrator still see full historical data and audit
context on a closed claim; only the mutation affordances (beyond comments, and beyond Administrator's
reopen) disappear.

---

## 20. Responsive Strategy

The two portals warrant genuinely different investment levels per breakpoint — treating them identically
would either under-serve the Client Portal's real mobile use case or over-invest in mobile polish the Admin
Portal's audience and tasks don't call for.

### 20.1 Admin Portal — desktop/laptop-primary

| Breakpoint | Treatment |
|---|---|
| Desktop (1280px+) | Full experience: sidebar expanded, multi-column claim detail, dense tables | 
| Laptop (1024–1280px) | Same structure, slightly tighter spacing; sidebar may default to collapsed/icon-only | 
| Tablet (768–1024px) | Sidebar collapses to icon rail or hamburger; claim detail tabs stack single-column where side-by-side panels no longer fit; tables gain horizontal scroll for lower-priority columns rather than hiding data silently | 
| Mobile (<768px) | **Reduced but functional fallback, not a fully optimised experience** — this is a deliberate, justified choice, not neglect: the BRD gives no indication Brokers need field/mobile access, the core tasks (dense tables, multi-field branching forms, nested record maintenance) do not translate well to phone width, and the ~12 internal users work from a workstation daily |

**Justification**: an internal ops tool used all day by people at a desk, doing tasks (reviewing tables,
filling branching forms, maintaining nested records) that are inherently poorly suited to a phone screen —
investing equally in a rich mobile Admin experience would spend effort the actual usage pattern doesn't
reward.

### 20.2 User Portal — mobile-first, genuinely

| Breakpoint | Treatment |
|---|---|
| Mobile (<768px) | **Primary target.** Bottom nav (§10.2), single-column card-based claims list, single-column claim detail with sections stacking vertically, wizard steps simplified to one field-group per screen, large touch targets (§21) |
| Tablet (768–1024px) | Same simple structure, more breathing room; top nav replaces bottom nav |
| Desktop/laptop | Still fully functional (some client contacts will use a laptop), but not the primary design target — the same simple, low-density layout is used, just with more whitespace, rather than introducing Admin-style density at wider widths |

**Justification**: a non-technical SOE client contact checking on a claim is a highly plausible
phone-first moment — it is, in fact, precisely the scenario the whole Portal exists to serve (Objective 2,
"no visibility... may see no change for weeks," Researcher §1.1/§2.4). NFR-10's usability constraint and
the low, infrequent task volume both point the same direction: invest in a excellent simple mobile
experience rather than a feature-complete desktop one with mobile as an afterthought.

### 20.3 What collapses/moves per breakpoint (summary)

| Element | Admin behaviour | User Portal behaviour |
|---|---|---|
| Primary nav | Sidebar → icon rail → hamburger | Top nav → bottom nav (mobile) |
| Claim detail layout | Multi-column/tabbed → stacked tabs → scrollable stacked (mobile fallback) | Always single-column, stacked sections |
| Lists | Table → table with horizontal scroll | Cards, always |
| Forms | Multi-column → single column | Always single column, one field-group per step on mobile |

---

## 21. Accessibility Requirements

The BRD states no formal accessibility target — this is an **open item**, not a decision made elsewhere in
this document set. **Recommendation**: adopt **WCAG 2.1 AA** as the baseline for both apps. This is a
sensible floor given: the platform is a regulated financial-services B2B tool (FSCA-governed, per NFR-09);
it serves external, non-technical users with an explicit usability constraint (NFR-10); the client base is
expected to grow (NFR-05), potentially bringing organisations with their own accessibility obligations; and
WCAG 2.1 AA is the de facto standard baseline for exactly this profile of application. This recommendation
should be confirmed with the Client alongside NFR-10's own open verification step (UAT), not treated as
settled.

| Concern | Requirement |
|---|---|
| Keyboard navigation | Every interactive element reachable/operable by keyboard; tab order matches visual/step order, especially through the branching insurer-decision form and the submission wizard |
| Focus management | Move focus to newly revealed conditional fields (e.g. Repudiation Reason appearing) so keyboard/screen-reader users aren't left behind; trap focus within confirmation dialogs for irreversible actions (Close Claim, Reopen) |
| Screen-reader considerations | Status badges must carry accessible text, not colour alone; the persistent Late-Reported flag (UC-11) must be announced by assistive tech wherever it appears, not just visually rendered |
| Colour contrast | WCAG AA minimum (4.5:1 body text); status badge colours (§22) must meet contrast against their background — colour alone must never be the only signal for a Repudiated/Settled/Within Excess distinction, given the compliance stakes of that outcome |
| Touch targets | Minimum ~44×44px, especially critical for the mobile-first User Portal (§20.2) |
| Form accessibility | Visible, associated labels (never placeholder-only) — critical given NFR-10's audience; error messages programmatically associated with their field, not conveyed by colour/icon alone |
| Error communication | Never colour-only; explicit text naming the problem and the fix, consistent with the "reject with a clear reason" pattern already established (§3.2/§3.4) |
| Semantic structure | Proper heading hierarchy per page; each tab panel on the Admin claim detail page needs its own accessible landmark/heading, not just a visual tab label |
| Responsive text | Rem-based scaling so browser zoom / user font-size preferences work, not fixed pixel sizes |
| Reduced motion | Respect `prefers-reduced-motion` for status transitions, banner appearances, and wizard step transitions, with a non-animated fallback |

---

## 22. Design System Foundations

Non-visual foundations only — layout/spacing/typography *hierarchy principles*, not literal tokens, fonts,
or colours (that is the UI Designer's pass, per the process this team follows).

### 22.1 Layout & grid, spacing, typography hierarchy (principles)

- **Admin Portal**: denser grid concept (more columns/panels available concurrently), tighter spacing scale
  appropriate for expert daily users scanning many records quickly, and a deeper typographic hierarchy (more
  heading/label levels, since nested tabs/panels genuinely need to distinguish more levels of content).
- **User Portal**: simpler, mostly single-column layout concept, more generous spacing (breathing room
  supports NFR-10's simplicity goal rather than working against it), and a flatter typographic hierarchy
  (2–3 levels: page title, section, body — this app's content genuinely doesn't need more).
- Both apps should share the *underlying scale logic* (e.g. a consistent base spacing unit and a consistent
  type-scale ratio) even though the two apps apply that logic at different densities — this keeps them
  feeling like they belong to the same product family without forcing identical layouts onto genuinely
  different audiences.

### 22.2 Component hierarchy

`packages/ui` primitives (buttons, inputs, tables, status badges, file uploader — architecture §11) sit
beneath app-specific page-level components, which each app builds independently (architecture §20.2). No
page-level or workflow component is shared, by design — this is a repeated architectural decision this
document does not second-guess, only builds on.

### 22.3 Button hierarchy

- **Primary** — one clear primary action per screen/section (Submit Claim, Record Decision, Close Claim).
- **Secondary** — routine, low-stakes actions (Cancel, Save Draft, Upload Document).
- **Tertiary/text-link** — navigational or low-commitment actions (View details, Download).
- The visually "highest-commitment" tier should be reserved for the genuinely irreversible/high-stakes
  actions the Researcher already identified (§3.4, §6.5): Record Decision, Close Claim, Reopen — these
  should visually read as more consequential than a routine Upload or Save, not merely differ in colour by
  convention.

### 22.4 Form hierarchy

Mandatory vs. optional fields visually distinguished consistently across both apps; conditional/branching
fields (FR-12's decision form, product-conditional asset selection) visually nested/indented under the
selection that triggered them, so the branching relationship is legible at a glance rather than only
inferred from behaviour.

### 22.5 Status/feedback patterns — must not be invented separately from the reports

This is the section most likely to be gotten wrong if treated as a fresh design decision, so it is stated
plainly: the BRD **already defines** a status→label→colour concept, administrator-configurable, that
directly feeds the report status-colour legend (FR-29's "status colour legend"; FR-31's "mapping of claim
status to report status label and colour"). **The in-app status badge (claims list, claim detail, client
dashboard, in both apps) must reuse this exact same mapping — sourced from Company & Report Settings data —
rather than an independently invented in-app colour scheme.** If the two disagreed, a Broker or Client could
see a claim rendered one colour in the app and a different colour on their downloaded report for the
identical status, directly undermining the BRD's own stated objective that clients "trust the numbers on
sight" (Researcher §1.5). Concretely: the `packages/ui` `StatusBadge` primitive should be driven by the
same settings-sourced mapping the `ReportsModule` already uses (architecture §5), not a separate hardcoded
per-app palette.

### 22.6 Navigation patterns

Recap of §10: sidebar + tabs + breadcrumbs + search for Admin; minimal top/bottom nav, no tabs/breadcrumbs/
search for User — restated here only as a design-system cross-reference, not re-argued.

### 22.7 Data visualisation principles

**Explicitly out of scope for Phase 1.** Per the approved architecture, reports are downloadable Excel-style
workbooks, not in-app report viewers — there is **no requirement for in-app charts or data visualisation**
of loss-ratio/claims-history data anywhere in the BRD. If a future dashboard summary needs a quick
"at-a-glance" figure (e.g. a Manager's exception view showing "12 claims outstanding"), that should be a
simple numeric/count tile, not a chart — charts should not be added spontaneously just because loss-ratio
and claims-history data exists in the system; that data's presentation surface is the workbook, by design.

### 22.8 Density

Deliberately divergent by app (Admin = high, User = low), mirroring the two-app architecture's own stated
rationale (NFR-10, architecture §20.2) — this divergence is intentional and should not be "fixed" toward
consistency in a later design pass.

### 22.9 Responsive behaviour

Recap of §20 — Admin desktop-primary with a functional-but-reduced mobile fallback; User Portal genuinely
mobile-first.

### 22.10 Interaction principles — avoid generic AI-SaaS patterns

**Avoid generic AI-SaaS patterns — unnecessary cards, excessive rounding, decorative icons, meaningless
gradients; every component should have a functional reason to exist.** This is a regulated B2B operational
tool used by insurance professionals and their commercial clients, not a consumer app chasing delight for
its own sake. Icons, where used, should support scanning of status/action meaning (e.g., a
document-outstanding indicator), never appear purely for decoration. This principle applies with different
intensity per app: the User Portal can justify slightly more visual warmth/reassurance than the Admin
Portal, in direct service of NFR-10's goal of reducing anxiety for a non-technical user checking on
something that matters to their business — but "warmer" still means *purposeful* (e.g. a clear, calm status
statement), not decorative. The Admin Portal should stay closer to a plain, functional, data-dense
operational tool throughout. Neither app should reach for decoration without a function it serves.

---

*End of the UX Architect's sections (7–22, plus the now-complete Section 13). The sections below (23–26)
are the UI Designer's Screen Inventory, Decision Record, and Open-Item Consolidation, closing out the
pre-visual UX discovery process. No visual/UI design content (colours, typography, mockups) appears below —
that remains explicitly out of scope for this document by design.*

---

## 23. Complete Screen Inventory

Built directly from the Sitemap (§9), the Admin/User Portal Layouts (§11–12), the Dashboard Architecture
(§13), and the Journeys (§6) — no screen below is invented beyond what those sections already establish,
and every screen those sections require is represented. Route-level tabs (e.g. Claim Detail's Documents/
Decision & Settlement/Communication tabs) are listed as their own rows because each is independently
routed (§9.1/§9.2) and independently task-relevant; confirmation dialogs and conditional panels (e.g.
Close Claim, the Settlement sub-flow) are listed as their own rows because the brief calls them out
explicitly, even though several are inline elements rather than full page navigations (§14).

### 23.1 Admin Portal

| Screen | Portal | User(s) | Purpose | Primary Task | Key Information | Primary Action | Secondary Actions | States |
|---|---|---|---|---|---|---|---|---|
| Login | Admin | All | Authenticate before any use | Enter credentials | Email/password fields | Log in | Forgot password | Loading, error (invalid credentials) |
| MFA Enrolment | Admin | All (first login) | Satisfy NFR-01's mandatory MFA | Set up authenticator | QR/setup code | Confirm code | Cancel/retry | Error (invalid code) |
| MFA Challenge | Admin | All (subsequent logins) | Verify identity each session | Enter TOTP code | Code field | Verify | Resend | Error, loading |
| Dashboard | Admin | Administrator, Manager, Broker (content varies, §13) | Situational awareness on login (no in-app notification centre exists, §11.4) | See what needs attention now | Needs-attention counts/list, scoped per role | Open a flagged claim | Quick links (Settings/Users for Admin; broker filter for Manager) | Empty ("nothing needs attention," §13.6), loading |
| Claims List | Admin | Broker (own), Manager/Admin (all) | Find the claim needing attention | Scan/filter/sort the queue | Ref, client, status badge, late flag, broker, last updated | Open claim | Filter, sort, New Claim | Empty, loading (skeleton), error |
| New Claim (broker-assisted lodgement) | Admin | Broker, Administrator | Lodge a claim reported by phone/email/in person (UC-15) | Complete the sectioned lodgement form | Client, policy/section/asset, loss details | Submit | Cancel | Validation, late-reported warning, success (reference number shown) |
| Claim Detail — Overview | Admin | Broker (own), Manager/Admin (all) | Assess current state, decide next action | Read status/timeline, jump to tabs | Status, timeline, linked client/policy/asset (§7.5) | Navigate to a tab | — | Loading, error, closed-view-only banner (§19.1) |
| Claim Detail — Documents (checklist view) | Admin | Broker (own), Manager/Admin (all) | Track/confirm the document checklist | Upload, mark ad-hoc item | Checklist items, Received/Outstanding | Upload document | Download, add ad-hoc item | Empty (Credit Insurance, Q-002), partial (never blocking, business rule #4), loading |
| Claim Detail — Insurer & Assessor | Admin | Broker (own), Manager/Admin (all) | Process the claim to the Insurer (UC-04) | Capture insurer claim no. + assessor detail | Insurer claim number, assessor name/contact/date | Save | — | Validation-blocked (status can't progress without insurer no., FR-06) |
| Claim Detail — Decision & Settlement (incl. AOL/excess sub-flow panel) | Admin | Broker (own), Manager/Admin (all) | Record the Insurer's decision; run the settlement sub-flow (UC-05/06) | Select outcome, branch, upload AOL/invoice/PoP | Outcome, branching fields, live settlement state (e.g. "Awaiting Signed AOL") | Save decision (confirmation-gated) | Upload AOL/invoice/PoP | Validation-blocked (missing reason/PoP), long-running (awaiting client), conditional-hidden until a Settled outcome is recorded (§14.4) |
| Claim Detail — Financials | Admin | Broker (own), Manager/Admin (all) | Capture claim value | Enter gross/excess/VAT | Gross, excess, VAT, net (calculated) | Save | — | Validation errors |
| Claim Detail — Communication | Admin | Broker (own), Manager/Admin (all) | Exchange claim-relevant messages with the Client (UC-12) | Send/read message | Timestamped thread | Send message | — | Empty (no messages yet) |
| Claim Detail — Comments | Admin | Broker (own), Manager/Admin (all) | Leave a single reportable note (FR-27) | Add/read comment | Single field, distinct from Communication (§9.3) | Add comment | — | Available post-closure |
| Claim Detail — Activity | Admin | Broker (own actions/claims), Manager/Admin (all) | See what changed on this record | Filter/scan claim-scoped audit | Actor, action, timestamp, diff | Filter | Export | Empty |
| Close Claim (confirmation) | Admin | Broker (own), Manager/Admin (all) | Terminate the claim once prerequisites are met (UC-07) | Confirm closure | Outcome-specific prerequisite check | Confirm close | Cancel | Validation-blocked (names the missing item, §3.4), success |
| Reopen Claim (confirmation) | Admin | Administrator only | Correct a closed record (business rule #12) | Confirm reopen | Reason (flagged, §5 task matrix) | Confirm reopen | Cancel | Success |
| Clients & Policies — List | Admin | Administrator (all), Broker (own edit/others view) | Find/maintain a client | Scan/filter directory | Name, assigned broker, policy count | Open client | Filter, New Client | Empty (new install) |
| New Client (creation flow) | Admin | Administrator, Broker (own) | Onboard a new SOE client org | Sequential Client → Policy → Section → Asset entry (§15) | Org details, policy/section/asset fields | Save & continue | Save & add another policy | Validation (nested field errors) |
| Client Detail — Organisation & Contacts | Admin | Administrator (all), Broker (own edit/others view) | Maintain org-level record | Edit org/contact fields | Org name, contacts | Save | — | Validation |
| Client Detail — Policies, Sections & Assets | Admin | Administrator (all), Broker (own edit/others view) | Maintain nested policy structure incl. risk premium (FR-24) | Add/edit policy/section/asset | Nested Client→Policy→Section→Asset, risk premium per underwriting year | Add/edit policy | Upload policy schedule | Validation, empty (no policies yet) |
| Client Detail — Claims (filtered) | Admin | Administrator (all), Broker (own) | See this client's claims in context | Scan filtered claims | Same fields as Claims List, scoped to one client | Open claim | Filter | Empty |
| Client Detail — Activity | Admin | Administrator (all), Broker (own) | See what changed on this client's record | Filter/scan client-scoped audit | Actor, action, timestamp, diff | Filter | Export | Empty |
| Reports — Generate | Admin | Broker (assigned), Manager/Admin (any) | Produce an accurate workbook without manual compilation (UC-08) | Select type/period/scope, generate | Type, period, scope | Generate | Download | Empty (no data for criteria, not an error), long-running (progress state) |
| Reports — History | Admin | Same as above | Re-download a prior report (FR-28) | Find and re-download | Generation date, generating user, period | Download | — | Empty (first-ever generation) |
| Audit Trail (global) | Admin | Administrator/Manager (all), Broker (own actions) | Compliance review, dispute investigation (FR-18) | Filter, review, export | Actor, action, entity, diff, timestamp | Filter | Export | Empty (filtered to nothing) |
| Users & Access — List | Admin | Administrator (full), Manager (view-only) | See who has access | Scan user list | User, role, client link (if Client role) | Open user | Filter | Empty (unlikely) |
| Users & Access — Create/Edit User | Admin | Administrator only | Grant/adjust access correctly, first time (UC-10) | Capture details, assign role | User details, role, client link | Save (triggers MFA-enrolment invitation) | Deactivate (confirmation-gated) | Validation (2-user-per-client limit exceeded, FR-17) |
| Product & Document Configuration | Admin | Administrator only | Keep checklists current without a dev release | Edit per-product document list | 29 products, per-product checklist | Save | — | Empty (Credit Insurance, Q-002) |
| Company & Report Settings | Admin | Administrator only | Configure report branding/status legend once, used everywhere (§22.5) | Edit company/report settings | Company details, disclaimer text, status→colour map | Save | — | Validation |
| 403 / No-Permission | Admin | Any (edge case) | Degrade gracefully if a hidden-module URL is reached | Read message, return | Reason (module unavailable to this role) | Return to Dashboard | — | Should be rarely reachable given §3.1's nav hiding |
| 404 / Not Found (incl. scoped record mismatch) | Admin | Any (edge case) | Avoid confirming existence of an out-of-scope record | Read message, return | Generic "not found" | Return to Claims/Clients | — | Reads as not found, never "forbidden" (§3.5) |
| System Error / Unavailable | Admin | Any (edge case) | Communicate a system/connectivity failure clearly | Retry | Specific failure reason | Retry | — | — |

### 23.2 User (Client) Portal

| Screen | Portal | User(s) | Purpose | Primary Task | Key Information | Primary Action | Secondary Actions | States |
|---|---|---|---|---|---|---|---|---|
| Login | User | Client-Primary/Secondary | Authenticate before any use | Enter credentials | Email/password | Log in | Forgot password | Loading, error, plain-language copy (§12.1) |
| MFA Enrolment | User | Client-Primary/Secondary (first login) | Satisfy NFR-01's mandatory MFA, in plain language | Set up authenticator | QR/setup code | Confirm code | Cancel/retry | Error (invalid code), reassuring copy |
| MFA Challenge | User | Client-Primary/Secondary | Verify identity each session | Enter code | Code field | Verify | Resend | Error, loading |
| First-time Orientation | User | Client-Primary/Secondary (first login) | Reduce training need (NFR-10) | Dismiss brief callouts | 2–3 short callouts (New Claim, My Claims) | Dismiss/finish | Skip | Shown once (§12.2, recommendation) |
| Dashboard (My Claims + reminder banner) | User | Client-Primary/Secondary | Know where a claim stands without phoning the Broker (Objective 2) | Scan claims, see outstanding action | Reminder banner, claims list (ref, status, last update, CTA) | Open a claim / New Claim | — | Empty (no claims yet), positive "nothing needs attention" state (§13.6) |
| New Claim (submission wizard) | User | Client-Primary/Secondary | Report a loss correctly the first time (UC-01) | Step through policy/section/asset/loss-details/attachments/review | Policy, section, (conditional) asset, date/location, narrative, claim type, attachments | Submit | Save & continue later (draft persistence, §15) | Late-reported warning (Review step), validation, draft-persisted on interruption |
| Claim Detail — Status & Timeline | User | Client-Primary/Secondary | See current status and history (UC-03) | Read status/timeline | Status, stage history, outstanding items | Navigate to Documents/Communication/etc. | — | Loading, error, plain-language |
| Claim Detail — Documents | User | Client-Primary/Secondary | Satisfy the document checklist (UC-02) | Upload documents | Checklist items, Received/Outstanding | Upload document | Download own uploads | Empty (Credit Insurance), partial (never blocking, §19) |
| Claim Detail — Communication | User | Client-Primary/Secondary | Exchange messages with the assigned Broker (UC-12) | Send/read message | Timestamped thread | Send message | — | Empty (no messages yet) |
| Claim Detail — Comment | User | Client-Primary/Secondary | Leave a single reportable note, incl. post-closure (FR-27) | Add/read comment | Single field | Add comment | — | Available post-closure |
| Claim Detail — Settlement (AOL/excess sub-flow panel) | User | Client-Primary/Secondary | Sign/upload the AOL, or pay and prove the excess (UC-06) | Download/sign/upload AOL; upload proof of payment | Live settlement state (e.g. "Awaiting Signed AOL") | Upload signed AOL / Upload proof of payment | Download unsigned AOL/invoice | Conditional-hidden until relevant (§14.4), long-running |
| Client Claim Detail — Closed (view-only variant) | User | Client-Primary/Secondary | Reference a resolved claim; leave a closing comment if needed | Read record, optionally comment | Full history, status = Closed | Add comment | View/download documents | Locked read-only banner (§19.1), no other mutation affordances |
| Reports — Generate & Download | User | Client-Primary/Secondary | Produce an own-org report without manual compilation (UC-08) | Select type/period, generate, download | Type, period | Generate | Download / re-download from history | Empty (no data for criteria), long-running |
| Profile / MFA | User | Client-Primary/Secondary | Manage own MFA; view contact details | Manage MFA enrolment | MFA status, contact details (view-only, §8.1 recommendation) | Reset MFA | Logout | — |
| Help/Support affordance | User | Client-Primary/Secondary | Get help without a full support system (§12.7 recommendation) | Read Broker contact details | Broker name/contact (sourced from Company & Report Settings) | Contact Broker (mailto/tel) | — | Minimal, persistent — not a full page |
| Session Expired | User | Client-Primary/Secondary | Communicate a timeout in plain language | Log in again | Plain-language message | Log in again | — | §12.9/§19 |
| 403 / No-Permission | User | Any (edge case) | Degrade gracefully; should be barely reachable | Read message, return | Reason | Return to Dashboard | — | Rare, given a single functional role (§3.1) |
| 404 / Not Found | User | Any (edge case) | Avoid confirming existence of another org's record | Read message, return | Generic "not found" | Return to Dashboard | — | Reads as not found (§3.5) |
| System Error / Unavailable | User | Any (edge case) | Communicate a failure in plain language | Retry | Plain-language failure reason | Retry | Contact Broker | — |

### 23.3 Screen tier classification

- **Critical** (the core Phase-1 claim lifecycle — Login/MFA, submission, tracking, decision, closure):
  Admin — Login, MFA Enrolment, MFA Challenge, Claims List, New Claim, Claim Detail (Overview, Documents,
  Insurer & Assessor, Decision & Settlement, Financials), Close Claim. User — Login, MFA Enrolment, MFA
  Challenge, Dashboard, New Claim, Claim Detail (Status & Timeline, Documents, Settlement), Client Claim
  Detail — Closed.
- **Supporting** (used regularly but not the core lifecycle itself — communication, reporting, directory
  browsing): Admin — Dashboard, Claim Detail (Communication, Comments, Activity), Clients & Policies List,
  Client Detail (Organisation & Contacts; Claims; Activity), Reports (Generate, History). User — First-time
  Orientation, Claim Detail (Communication, Comment), Reports, Profile, Help/Support affordance.
- **Administrative** (operational record-keeping that keeps the business running, not claim-lifecycle
  actions): Admin — New Client, Client Detail — Policies/Sections/Assets, Audit Trail, Users & Access
  (List, Create/Edit).
- **Configuration** (Administrator-only reference data, low frequency, feeds other screens): Admin —
  Product & Document Configuration, Company & Report Settings.
- **Rare-edge-case** (should be reached infrequently by design, but must degrade gracefully): Admin —
  Reopen Claim, 403, 404, System Error/Unavailable. User — Session Expired, 403, 404, System
  Error/Unavailable.

---

## 24. Architectural Decisions + Rationale

| Decision | Alternatives | Chosen Approach | Reason |
|---|---|---|---|
| Admin navigation | Sidebar / Top / Hybrid | Persistent left sidebar (module-level) + top bar (search, profile) + contextual tabs within record detail + breadcrumbs for nested drill-down | Per §10.1: 7+ modules, several role-conditional, high daily task frequency, desktop-primary use. A sidebar scales to this module count without crowding and role-based item removal doesn't reflow the whole nav unpredictably the way a horizontal bar's overflow menu would — and an overflow menu would actively hide the Administrator-only items (Settings, User Management) that need to stay findable for the role that has them. |
| User navigation | Sidebar / Top / Bottom / Hybrid | Minimal persistent nav (3–4 items): top nav on desktop/tablet, bottom nav on mobile — no sidebar, no tabs at the nav level, no breadcrumbs | Per §10.2: only 4 modules, 2 levels deep, low/infrequent task frequency but a mobile-plausible, low-tech-literacy audience (NFR-10). A sidebar would signal "many sections to manage," the wrong message for a narrow, occasional-use tool; bottom nav on mobile is a deliberate exception to "avoid consumer-app patterns" because it lowers cognitive load for exactly this audience. |
| Record details | Page / Drawer / Modal | Dedicated page with tabs (Admin Claim/Client Detail); single scrollable page with sections (Client claim detail) | Per §7.3/§8.2/§14: a claim is the central, information-dense workspace a Broker returns to repeatedly (multi-tab, lateral movement needed) and the primary content a Client user opens the app for at all (linear, shallow content). Neither density profile fits a drawer's "temporary overlay" or a modal's "short single-sitting interaction" metaphor. |
| Forms | Page / Modal / Wizard | Multi-step wizard for infrequent, non-technical submission (Client self-service, UC-01); single scrollable sectioned page for frequent, trained users (Broker-assisted lodgement, UC-15; Client/Policy/Asset maintenance) | Per §14/§15: per-step validation in a wizard protects an infrequent, non-technical user (NFR-10) from an error discovered only at the end; that same step-friction is a pure cost with no benefit for an expert user performing the task often. |
| Tables | Table / Cards / List | Table (Admin Claims List, Audit Trail); cards/list (Client Portal claims list) | Per §14/§16: high-density, sortable/filterable data suits a table for expert daily users scanning many records; a low-volume, non-technical audience (NFR-10) is better served by cards, since a dense table is an Admin-density pattern that works against this app's simplicity goal. |
| Search | Global / Contextual / Both | Global search (top bar, Admin only, scoped by role); list filter/sort only on the User Portal, no dedicated search feature | Per §17: a Broker managing many claims across many clients needs a fast jump-to mechanism beyond browsing; a client org's low concurrent-claim volume (Researcher §5.2) means a search feature would solve a volume problem this audience doesn't have. |
| Settlement/AOL/excess sub-flow | Page / Modal / Drawer / Inline panel | A persistent inline panel/section on the Claim Detail page (a "Settlement" tab on Admin, a "Settlement" section on the linear Client layout), appearing conditionally once a Settled outcome with a settlement method is recorded | Per §14.4: this sub-flow spans days or weeks with a background reminder loop running throughout — a modal or drawer's "temporary overlay, close and reopen" metaphor is wrong for a stateful, long-running process that must stay visible as part of the claim's ongoing status picture; a separate page would sever it from the causally-linked Decision & Settlement context. |
| Settings | Central / Contextual / Both | Two standalone top-level nav items (Product & Document Configuration; Company & Report Settings), not a shared "Settings" umbrella with sub-tabs | Per §7.7: both are Administrator-only, pure reference/configuration data, but serve genuinely different purposes (claim-checklist data vs. report branding/legend); conflating them under one menu would obscure that distinction for the one role who uses both. [Recommendation: reassess a shared "Configuration" parent grouping if the reference-data set grows in Phase 2 — not warranted at Phase 1's two-item scale.] |
| Notifications | In-app centre / Email-only / Hybrid | Email-only, per the approved architecture — with the Client dashboard reminder banner as the one confirmed in-app surface, and need-for-action-ordered dashboards/queues (§13) as the internal-role equivalent | Per §11.4/§13/§18: the architecture already decided outbound-email-only with no in-app channel (architecture §4). Rather than inventing a notification bell/centre that would contradict that decision, the UX answer is default views ordered by what needs attention, so opening the app itself is the situational-awareness mechanism. |
| Status/feedback colour source | Independently designed per surface / Reused from a single settings-sourced mapping | Reused: the in-app `StatusBadge` (claims list, claim detail, client dashboard, both apps) is driven by the same Company & Report Settings status→colour mapping the `ReportsModule` already uses for the report legend | Per §22.5: prevents a claim rendering one colour in-app and a different colour on its downloaded report for the identical status, which would directly undermine "clients trust the numbers on sight" (Researcher §1.5). |
| Bulk actions on claims | Bulk-select/bulk-action toolbar / Filtering & sorting only | Rich filtering/sorting/triage views on the claims list; no bulk-select checkboxes or bulk-mutation toolbar | Per §5.2/§11.8/§16: the BRD never requests a bulk-mutation action, and every mutation in this domain (a decision, a closure) is individually consequential and audit-significant — a bulk "close 12 claims" affordance would work against the deliberate one-claim-at-a-time gating built into UC-05/UC-07. |
| In-app data visualisation (charts) | Charts/graphs for loss ratio, claims-history trends, etc. / No in-app charts, simple actionable count tiles only where a dashboard number is warranted | No in-app charts; reports remain downloadable Excel-style workbooks (architecture); dashboards may show simple, actionable numeric tiles only (§13.4) | Per §22.7 and the approved architecture: reports are downloadable workbooks, not in-app viewers, and there is no BRD requirement for in-app data visualisation anywhere in the system — adding charts "because the data exists" would contradict this deliberate position. |
| Closed-claim record treatment | Delete/archive out of view / Locked to view-only + comments, retained in place | A closed claim is never deleted; it is locked to view-only (Client, except the Comment field) and remains fully visible with audit history to internal roles, editable again only via Administrator reopen | Per §19.1, business rules #12–13, NFR-08/09: the audit trail is append-only/immutable and 5-year retention is a compliance requirement — "delete" is not a concept this domain's UX should ever surface; only "close"/"reopen"/"deactivate." |
| No-permission vs. not-found distinction | One generic 403/denial page for every case | Three distinct treatments: hide entirely (nav-level, no access at all), reject with a named reason (a business-rule block), "not found" (a scoped record mismatch) | Per §3.2/§3.5/§19: these are three different causes needing three different responses — treating a scoped-record mismatch as "forbidden" would confirm to an unauthorised user that the record exists at all, contradicting NFR-02's data-isolation intent. |

---

## 25. Open Questions / Assumptions

### 25.1 Known facts (directly sourced from the BRD/architecture)

- Two separate Next.js applications (Admin Portal, User/Client Portal) behind one shared NestJS API
  (architecture §1–4); no shared page-level UI components between them (architecture §20.2).
- MFA is mandatory for every role, every login (NFR-01).
- Client-Primary and Client-Secondary are permission-identical; the only distinction is administrative —
  Primary is the fixed report/notification addressee (`user-roles.md`; FR-29, FR-33).
- The 2-user-per-client limit is a defined rule (FR-17), with edge-case handling for larger clients still
  open (Q-009).
- Reports are downloadable Excel-style workbooks with all calculated fields (underwriting year, loss ratio,
  net claim, progress classification) computed server-side, never in either frontend (FR-22; architecture
  §5); every generated report is retained, immutable, and re-downloadable (FR-28).
- Notifications are outbound-email-only; the only confirmed in-app surface anywhere in the system is the
  Client dashboard reminder banner (FR-07; `process-flow.md`'s Module 6 note; architecture §4).
- The document checklist is advisory, never blocking (FR-03/FR-04, business rule #4).
- The 30-day late-reporting flag is advisory, never blocking (FR-19).
- Insurer-decision recording is a mutually exclusive three-way branch (Settled / Repudiated / Within
  Excess); Repudiation Reason is mandatory to save a Repudiated decision (FR-12).
- Cash settlement drives an AOL cycle; Repair/Replacement drives an excess-invoice cycle; no invoicing
  arises on a cash settlement (`process-flow.md` step 16).
- Claim closure is gated on outcome-specific prerequisites and refuses to proceed if unmet (FR-13, UC-07).
- Only the Administrator role can reopen a closed claim (business rule #12).
- The audit trail is append-only/immutable with a minimum 5-year retention (NFR-08/09, business rule #13).
- 29 products are confirmed in scope; 28 of 29 have supplied document checklists, Credit Insurance does not
  yet (Q-002).
- The 16-stage client-facing status list in `process-flow.md` is an explicit **proposal**, not final wording
  (Q-003).
- Broker-assisted lodgement requires no Client confirmation step before the claim proceeds (FR-33).
- Report scope is role-based: Client → own org; Broker → assigned clients; Manager/Administrator → any
  client (business rule #17).

### 25.2 Reasonable assumptions (this team's judgment calls, BRD silent)

- **WCAG 2.1 AA** as the accessibility baseline for both apps (§21) — the BRD sets no formal target.
- **Draft persistence** across a session-timeout or system-error interruption on the claim submission wizard
  (§15) — the BRD doesn't specify draft-save behaviour on a failed submission.
- **Minimal help/support affordance** = a persistent "Contact your Broker" link sourced from Company &
  Report Settings, not a ticketing system or knowledge base (§12.7).
- **Soft warning, not a hard block**, on likely-duplicate records (e.g. a duplicate policy number for the
  same client) (§19) — consistent with the domain's own "advisory, not blocking" cross-cutting theme, but
  not itself BRD-specified.
- **Standard forgot-password/password-reset flow** in both apps, as ordinary auth hygiene alongside the
  BRD-specified MFA requirement — not itself called out in the BRD.
- **View-only Profile contact details** for Client users, with edits remaining Administrator/Broker-only per
  FR-17 (§8.1) — prevents a Client-side side-channel around user-management access control.
- **First-time Client orientation** as 2–3 short, dismissible callouts rather than a forced multi-screen
  tour (§12.2).
- **Illustrative dashboard "needs attention" thresholds** (e.g. "documents outstanding >14 days," §13.1–
  13.3) are placeholders for a configuration decision to make later, not BRD-specified numbers.
- **"Not found," not "forbidden,"** as the response to a scoped-record mismatch (§3.5) — standard
  multi-tenant practice consistent with NFR-02's isolation intent, but not itself BRD-specified.

### 25.3 Things requiring validation (open BRD items + UX-specific validation)

- **Q-001** — product-specific workflow variations: could add screens/validation beyond the one generic
  workflow this document assumes.
- **Q-002** — Credit Insurance document list: closes the one remaining placeholder-state gap on the
  Documents screen.
- **Q-003** — exact client-facing status wording: the single most visible piece of client-facing copy in
  the product; should not be finalised in visual design before this is resolved.
- **Q-009** — 2-user-per-client edge cases: may require an exception-request pattern on Users & Access.
- **Q-010** — "responsible claims handler" field: may require an added field on Claim Detail — Overview.
- **Q-019** — when a claim counts as "Resolved": affects Performance Report content and any dashboard
  "needs attention" logic that references resolution state.
- **NFR-04/06/07 (Q-006)** — performance, uptime, and backup/recovery targets: affects whether upload/
  processing UI needs stronger progress/queueing treatment than currently specified.
- **NFR-10 (usability)** — the whole User Portal's simplicity assumptions should be validated in UAT with an
  actual non-technical SOE client contact, not just reviewed internally.
- **Status wording usability** — separate from the Q-003 content sign-off itself, the proposed 16-stage
  wording should be usability-tested with a real Client contact before being treated as final UI copy.
- **Bulk actions for Brokers** — this document deliberately did not design bulk-mutation affordances
  (§11.8); whether Brokers would actually want any once using the system day-to-day should be checked
  against real usage, not left as a permanent assumption from BRD silence.
- **The dashboard/no-notification-centre approach** (§13, §11.4) — validate with actual Administrator/
  Manager/Broker users whether a sorted queue genuinely substitutes for a notification centre in practice.
- **The mobile-first User Portal assumption** (§20.2) — validate with actual Client stakeholders that SOE
  client contacts do check claims from a phone in practice, not merely plausibly.

### 25.4 General industry convention (context only, not BRD-sourced)

Status-driven claim timelines, advisory (non-blocking) document-checklist patterns, and role-scoped
dashboards (an internal handler's work queue vs. an insured's simplified status view) are common,
well-understood patterns across claims-management and broker-portal software generally. This document's
choices align with that convention where the BRD is silent, but the convention itself is offered here only
as general background — it is not a requirement sourced from the Aris Brokers BRD.

---

## 26. Recommended Next Steps Before UI Implementation

1. **Resolve Q-003 (status wording) and Q-001 (product variations) with the Client** before finalising the
   claim-status UI copy and the product-specific checklist logic — both gate work visual design will need.
2. **Resolve Q-002 (Credit Insurance document list)** — closes the one remaining placeholder-state gap on
   the Documents screen.
3. **Validate the dashboard/no-notification-centre approach (§13, §11.4) and the mobile-first User Portal
   assumption (§20.2)** with actual Broker/Manager/Administrator and Client stakeholders, ideally as part of
   the same UAT round NFR-10 already calls for.
4. **Confirm WCAG 2.1 AA formally with the Client** (§21) as the accessibility target, rather than carrying
   it forward as a recommendation into visual design.
5. **Resolve Q-009 (2-user-per-client edge cases) and Q-010 (responsible claims handler)** — both could add
   fields/flows to Users & Access and Claim Detail — Overview respectively; better resolved before those
   screens are visually designed than after.
6. **Resolve Q-019 (Resolved vs. Closed)** — affects Performance Report content and any "needs attention"
   dashboard logic that references resolution state.
7. **Obtain NFR-04/06/07 targets (Q-006)** — confirms whether upload/processing UI needs stronger progress/
   queueing treatment than currently specified.
8. **Proceed to visual design system definition** — colour palette (including the status→colour mapping's
   actual values), typography, spacing tokens — building directly on §22's non-visual foundations.
9. **Begin screen-level visual design starting with the Critical-tier screens from §23.3** (auth/MFA, claims
   list, claim detail and its Documents/Decision & Settlement tabs, the new-claim wizard, the client
   dashboard, the closed-claim view) before Supporting/Administrative/Configuration-tier screens.
10. **Validate the bulk-action-free design (§11.8) and the FR-08/FR-27 Communication-vs-Comment distinction
    (§12.6)** specifically during UAT, since both are judgment calls this team made against indirect signals
    in the BRD rather than explicit instructions.

---

*End of the complete UX blueprint (Sections 1–26). Ready for hand-off to UI/visual implementation.*
