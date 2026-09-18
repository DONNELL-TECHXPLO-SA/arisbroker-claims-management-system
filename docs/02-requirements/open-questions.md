# Open Questions

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 18 ("Open Items & Information
> Required from the Client"), Section 16.5 ("Migration Open Items"), and OPEN ITEM notes in Sections 11
> (NFR-04/06/07/10), 12.8 and 13. These are the items the BRD explicitly flags as **not yet assumed** —
> per the BRD's own framing, "these are tracked here rather than assumed."
>
> **ID convention note**: assigned sequentially as Q-001 onward per this repo's template, in the order the
> BRD presents them. Each retains a pointer back to its BRD location for traceability.

---

### Q-001: Which of the 29 products follow the standard claims workflow vs. requiring product-specific variations?

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo (BA) / Aris Brokers, requirements sessions (Aug–Sep 2026) |
| **Date raised** | Not specified per-item in BRD; tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | Aris Brokers (workshop or documented workflow) |
| **Blocks** | Final build scope for [`../02-requirements/product-document-requirements.md`](product-document-requirements.md); [`use-cases.md`](use-cases.md) product-specific steps |

**Question** — Confirmation of which products follow the standard claims workflow (Section 14 process
flow / UC-01–UC-15) versus which require product-specific exceptions or steps.

**Why it matters** — Could introduce additional use cases, screens, or validation rules beyond the generic
workflow currently scoped.

**Answer (once resolved)** — _Pending._

---

### Q-002: Credit Insurance document list

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo (BA) |
| **Date raised** | Tracked since BRD v0.6 (16 Sep 2026) |
| **Needs answer from** | Aris Brokers |
| **Blocks** | [`product-document-requirements.md`](product-document-requirements.md) (28 of 29 products currently documented) |

**Question** — The Client has supplied document lists for 28 of the 29 confirmed products. A document list
for Credit Insurance is still required.

**Why it matters** — FR-03's required-document checklist cannot be generated for Credit Insurance claims
until this is supplied.

**Answer (once resolved)** — _Pending._

---

### Q-003: Client-facing claim status wording

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo (BA) |
| **Date raised** | Tracked since BRD v0.2 (15 Sep 2026) |
| **Needs answer from** | Joint workshop (TechXplo + Aris Brokers) |
| **Blocks** | [`process-flow.md#claim-status-stages-client-facing`](process-flow.md#claim-status-stages-client-facing); FR-05 |

**Question** — Confirmation of the exact status stages/wording desired on the client dashboard. Section
14.1 of the BRD is an initial proposal only.

**Why it matters** — Directly affects the client-facing UI copy and the status/colour legend used across
generated reports (FR-31).

**Answer (once resolved)** — _Pending._

---

### Q-004: Two-way email synchronisation feasibility

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | TechXplo (technical feasibility assessment) |
| **Blocks** | Phase 1 vs Phase 2 scope for FR-08/FR-20 (Client-Broker communication log / Outlook integration) |

**Question** — Feasibility and scope of inbound client email replies automatically appearing in the portal
(Broker–Client channel).

**Why it matters** — Currently assumed out of scope for Phase 1 (Section 3.2); confirming feasibility early
avoids rework if the Client wants it pulled forward.

**Answer (once resolved)** — _Pending — RSK-02 in the risk register tracks the related schedule risk._

---

### Q-005: Microsoft 365 integration scope (Phase 2)

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | Aris Brokers |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | Aris Brokers (to define) |
| **Blocks** | Phase 2 planning only — no Phase 1 impact |

**Question** — Scope/purpose of the proposed Microsoft 365 integration beyond outbound Outlook email
(FR-20).

**Why it matters** — Affects Phase 2 scoping and NFR-12 (extensibility) architecture considerations.

**Answer (once resolved)** — _Pending — explicitly deferred to Phase 2 (Section 3.4)._

---

### Q-006: Performance, availability, backup and recovery targets

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo / Aris Brokers |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | Joint (TechXplo proposes, Aris Brokers approves) |
| **Blocks** | [`non-functional-requirements.md`](non-functional-requirements.md) NFR-04 (document-volume benchmark), NFR-06 (uptime SLA), NFR-07 (backup RPO/RTO) |

**Question** — Specific uptime SLA, backup frequency, and Recovery Point/Time Objectives (RPO/RTO); target
concurrent-user and document-volume benchmarks.

**Why it matters** — Cannot finalise NFR-04/06/07 acceptance criteria, or size infrastructure during the
architecture phase, without these targets.

**Answer (once resolved)** — _Pending._

---

### Q-007: Legal/registered company details & branding

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | Aris Brokers |
| **Blocks** | [`data-requirements.md#1211-company--report-settings`](data-requirements.md#1211-company--report-settings) (FR-31); report header generation (FR-29) |

**Question** — Confirmation of the Client's registered name, FSP number, and logo/branding for the portal
and generated documents.

**Why it matters** — Required to populate the Company & Report Settings area before any report can be
correctly branded/addressed.

**Answer (once resolved)** — _Pending._

---

### Q-008: Hosting / data residency

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo / Aris Brokers |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | Joint |
| **Blocks** | Architecture/infrastructure decisions (deliberately deferred — see [`../03-architecture/README.md`](../03-architecture/README.md)); RSK-05 |

**Question** — Confirmation of hosting preference and data-residency requirements (South Africa-based
hosting, given POPIA/FSCA considerations).

**Why it matters** — Affects the Client's compliance position and will be a required input to Phase 2
architecture decisions.

**Answer (once resolved)** — _Pending — TechXplo to propose an option satisfying POPIA/FSCA expectations
for the Client's approval during the design phase (RSK-05)._

---

### Q-009: Client user-limit edge cases

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | Aris Brokers |
| **Blocks** | [`user-roles.md`](user-roles.md) (2-user-per-client rule, FR-17) |

**Question** — Final sign-off on the 2-users-per-client rule, including how larger SOE clients with more
than 2 interested stakeholders should be handled.

**Why it matters** — Could require a rule change or an exception process for larger clients.

**Answer (once resolved)** — _Pending._

---

### Q-010: "Responsible claims handler" field for continuity planning

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | Aris Brokers |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | Aris Brokers |
| **Blocks** | [`user-roles.md`](user-roles.md) / FR-10 (Broker-Client Allocation) |

**Question** — Whether a "responsible claims handler" field is still needed on the claim record for
internal continuity/succession planning, even though each client currently has a fixed assigned broker.

**Why it matters** — May require an additional data field and business rule beyond the current
one-broker-per-client model.

**Answer (once resolved)** — _Pending._

---

### Q-011: Policy data source & refresh approach

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | TechXplo |
| **Blocks** | [`data-migration-requirements.md`](data-migration-requirements.md); FR-09 |

**Question** — Confirmation of policy data source and refresh approach (manual capture per policy vs. a
future integration with an insurer/underwriting-management system).

**Why it matters** — Phase 1 assumes manual capture only (Section 3.3); confirms this remains the case
through go-live.

**Answer (once resolved)** — _Pending._

---

### Q-012: In-flight claims at cutover

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | Aris Brokers |
| **Blocks** | [`data-migration-requirements.md#65-open-items`](data-migration-requirements.md); go-live cutover plan; RSK-04 |

**Question** — Whether claims open/in-progress at the point of go-live need to be manually re-captured into
the portal for continued tracking, or whether they remain managed via the legacy manual process until
closure.

**Why it matters** — Affects migration effort and whether the Lucid repository must remain in parallel use
post-go-live for those claims.

**Answer (once resolved)** — _Pending._

---

### Q-013: Migration source format & ownership

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.1 (19 Aug 2026) |
| **Needs answer from** | Aris Brokers to confirm |
| **Blocks** | [`data-migration-requirements.md`](data-migration-requirements.md) |

**Question** — Confirmation of the source format of existing policy summaries (e.g. PDF, Word, Excel), to
assess whether a bulk-upload template is feasible, and who performs the manual data capture ahead of
go-live.

**Why it matters** — Determines whether TechXplo can offer a structured import template versus fully manual
UC-09 data entry for ~14–15 clients' policies.

**Answer (once resolved)** — _Pending._

---

### Q-014: VAT validation rule

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.6 (16 Sep 2026) |
| **Needs answer from** | Aris Brokers |
| **Blocks** | FR-25 (Claim Financial Values) |

**Question** — The two claims-history tabs in the supplied report template calculate VAT on different bases
(15% of gross on motor, 15% of gross less excess on non-motor). Confirmed that VAT is entered manually from
the invoice and the system will not calculate it — but is any validation rule wanted to sanity-check the
manually entered VAT amount?

**Why it matters** — Without a validation rule, transcription errors in VAT entry would not be caught by
the system.

**Answer (once resolved)** — _Pending._

---

### Q-015: Report disclaimer wording

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.5 (16 Sep 2026) |
| **Needs answer from** | Aris Brokers |
| **Blocks** | [`data-requirements.md#1211-company--report-settings`](data-requirements.md#1211-company--report-settings) (FR-31) content load at go-live |

**Question** — The disclaimer text is confirmed to be held as configurable text per report type in the
Company & Report Settings area (FR-31) — but the Client still needs to supply the actual wording.

**Why it matters** — Cannot populate this settings field or finalise report templates without the wording.

**Answer (once resolved)** — _Pending._

---

### Q-016: Non-motor asset identifiers — Serial No. vs Barcode

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.6 (16 Sep 2026) |
| **Needs answer from** | Aris Brokers |
| **Blocks** | [`data-requirements.md#122-asset-information`](data-requirements.md#122-asset-information) |

**Question** — The Non-Motor Claims History report tab reports both a Serial No. and a Barcode per asset.
Confirm whether both are available on existing non-motor assets, or whether one of the two is sufficient.

**Why it matters** — Affects the asset data-capture requirement (FR-21) and whether both fields are
mandatory to collect.

**Answer (once resolved)** — _Pending._

---

### Q-017: Amount Paid vs. Net Claim Amount on non-motor claims

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo |
| **Date raised** | Tracked since BRD v0.6 (16 Sep 2026) |
| **Needs answer from** | Aris Brokers |
| **Blocks** | [`data-requirements.md#126-claim-information`](data-requirements.md#126-claim-information) (Amount Paid field); FR-25 |

**Question** — The non-motor claims-history tab carries both a calculated Net Claim and a separate Amount
Paid figure. Confirm when the two are expected to differ and who supplies Amount Paid.

**Why it matters** — Currently Amount Paid is captured by the Broker as "the figure actually settled by the
Insurer" — needs confirmation this is distinct from Net Claim in all cases, not just some.

**Answer (once resolved)** — _Pending._

---

### Q-018: Is the Insurer (and excess/sum insured) set per Policy, or per Section within a Policy?

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo (during database design) |
| **Date raised** | 2026-09-17 |
| **Needs answer from** | Aris Brokers |
| **Blocks** | [`../04-design/database/README.md#74-insurer-and-financial-terms-live-on-policysection-not-policy-risk-premium-lives-on-policy`](../04-design/database/README.md#74-insurer-and-financial-terms-live-on-policysection-not-policy-risk-premium-lives-on-policy) |

**Question** — Section 12.1 of the BRD lists "Insurer(s)" as a field of the Policy itself ("one or more
insurers on the policy"), while FR-25 says the claim excess "defaults from the policy section". Where a
single policy covers more than one product/section (e.g. a policy with both a Property section and a Motor
Fleet section), is it possible for different sections under the same policy to be underwritten by
different insurers, and does the excess/sum insured vary by section? Or does every policy always have
exactly one insurer, with the excess/sum insured set once at the policy level, regardless of how many
sections/products it covers?

**Why it matters** — The database schema currently captures insurer, sum insured, and excess at the
Section level (one row per product within a policy), on the assumption that these can vary by section.
If, in practice, one policy is always tied to a single insurer with one excess/sum-insured figure, that
data should sit on the Policy record instead, which is a small change but affects how policies are
captured and how the Claim-Loss Ratio and Claims History reports pull the Insurer/Excess columns.

**Answer (once resolved)** — _Pending._

---

### Q-019: Does a claim count as "Resolved" once the Insurer's decision is recorded, or only once the claim is formally closed?

| Field | Value |
|---|---|
| **Status** | Open |
| **Raised by** | TechXplo (during database design) |
| **Date raised** | 2026-09-17 |
| **Needs answer from** | Aris Brokers |
| **Blocks** | FR-30 (Progress Classification); [`../04-design/database/README.md#77-progress-interpretation-resolved--status--closed-only`](../04-design/database/README.md#77-progress-interpretation-resolved--status--closed-only) |

**Question** — FR-30 defines Progress as "Outstanding (the claim is not yet approved) or Resolved (the
claim is resolved **and** closed)". Take a claim the Insurer has Settled, Repudiated, or ruled Within
Excess (UC-05), but which the Broker has not yet formally closed on the portal (UC-07) — for example,
while awaiting the Insurer's Proof of Payment. On the Performance Report's Summary Report tab, should that
claim count as **Resolved**, or does it still count as **Outstanding** until the Broker actually closes it?

**Why it matters** — This changes the Outstanding/Resolved counts on the Summary Report tab, split by
class of insurance, that the Client uses to gauge portfolio status at a glance. The current design reads
the FR-30 wording literally (Resolved requires closure), but that may understate "Resolved" if the Client
actually means "the outcome has been decided" regardless of whether the file has been formally closed yet.

**Answer (once resolved)** — _Pending._

---

## Previously Raised & Resolved (for reference)

The BRD's Section 18 table also lists a number of items that were raised during the two requirements
workshops (sessions 01–02) and the report-template review (v0.3–v0.7) and have since been **resolved and
incorporated directly into the current requirements** rather than left open. These are not repeated here as
open questions; each is captured at its resolution point:

| Resolved item | Where it now lives |
|---|---|
| Claims History Report template format | [`functional-requirements.md`](functional-requirements.md) FR-14; [`data-requirements.md`](data-requirements.md) Appendix D.1 |
| Performance Report template format | FR-15; Appendix D.2 |
| Claim-Loss Ratio tab format | FR-16; Appendix D.2/H.2.1 |
| Reporting period selection | FR-22 |
| Multi-policy ("consolidated") reporting | FR-22, FR-16, FR-29 |
| Broker registered name & FSP number on reports | FR-31; [`data-requirements.md#1211-company--report-settings`](data-requirements.md#1211-company--report-settings) |
| Report addressee (always the Client's Primary contact) | FR-29 |
| Non-motor reporting in/out of scope | FR-15 (confirmed **in** scope) |
| Matters Arising register | Explicitly excluded from the system — see [`functional-requirements.md`](functional-requirements.md) FR-15 note |
| Process flow diagram (lodgement paths, notification channel, assessor-report handling, decision outcomes) | [`process-flow.md`](process-flow.md) |
| Session 02 process-step clarifications (E01–E16 exception handling, notification timing, assessment report ownership, etc.) | Reflected directly in [`use-cases.md`](use-cases.md) and [`functional-requirements.md`](functional-requirements.md) |

Full history of every change and resolution is preserved in the source BRD's Appendices B, E, F, G, I and J
(Session/Version Change Logs) if a detailed audit of *how* a requirement evolved is ever needed.
