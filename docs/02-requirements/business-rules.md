# Business Rules

> Source: consolidated from the "Business Rules" fields of [`use-cases.md`](use-cases.md) and related
> Functional Requirements in [`functional-requirements.md`](functional-requirements.md). This file is a
> quick-reference index — each rule links back to its full context rather than repeating it, per this
> repo's convention of linking rather than duplicating content.

| # | Rule | Source |
|---|---|---|
| 1 | A claim is flagged "Late Reported" (not blocked) where the gap between date of loss and date of registration exceeds 30 days; the Broker may capture a written motivation. | FR-19, UC-01, UC-11 |
| 2 | A claim cannot be submitted without selecting a policy and section; where the product requires it, a specific registered asset must also be selected. An item not on the asset register is not covered. | FR-01, FR-21, UC-01, UC-09 |
| 3 | The in-house claim reference number is system-generated and cannot be edited by any user. | FR-02, UC-01 |
| 4 | The required-document checklist is advisory only: no document is mandatory, and an incomplete checklist never blocks claim registration or progression. | FR-03, UC-01, UC-02, UC-04 |
| 5 | Only the assigned Broker (or Manager/Administrator) may action a claim; Broker users are scoped to their own assigned clients. Manager/Administrator can view/action any client's claims. | FR-10, UC-04, UC-07 |
| 6 | The Insurer claim number is mandatory before a claim's status can progress to "Submitted to Insurer". | FR-06, UC-04 |
| 7 | Assessor detail is limited to three accountability fields (Name/Company, Contact Details, Date Appointed) — no assessor workflow, scheduling, or assessor portal access exists in Phase 1. The assessment report itself is retained by the Insurer and never reaches the Broker. | FR-11, UC-04, UC-05 |
| 8 | An Insurer decision must be exactly one of three outcomes: **Settled** (Cash/Repair/Replacement), **Repudiated** (mandatory Repudiation Reason), or **Within Excess** (no Insurer liability, Client bears the cost). A "Repudiated" outcome cannot be saved without a reason. | FR-12, UC-05 |
| 9 | On a cash settlement: Broker uploads an unsigned AOL → Client uploads the signed AOL → Broker records the Insurer's proof of payment. No invoicing arises on a cash settlement. Both the unsigned and signed AOL versions are retained. | FR-12, UC-06 |
| 10 | On a repair/replacement settlement: Broker issues the excess invoice to the Client → Client uploads proof of payment of the excess → Broker records that proof of payment has been forwarded to the service provider. The system notifies the Client only; it never corresponds with the service provider. | FR-12, UC-06 |
| 11 | A claim can only close once outcome-specific closure documents are present: Proof of Payment for a Settled claim; a Repudiation Reason for a Repudiated claim. Only Broker (own clients) or Manager/Administrator (any client) may close a claim. | FR-13, UC-07 |
| 12 | A closed claim becomes view-only to Client users (comments only) and remains editable only by an Administrator (re-open/amend). Closed claims are retained for a minimum of 5 years regardless of outcome, and remain open to notes/comments for post-closure events (e.g. subrogation, excess refund). | FR-13, NFR-09, UC-07 |
| 13 | Audit entries are immutable — no role can edit or delete them — and are retained for a minimum of 5 years (FSCA). Brokers see only their own actions/claims in the audit trail; Administrator/Manager see all. | FR-18, NFR-08, UC-14 |
| 14 | Maximum of 2 active users (Primary/Secondary) per Client organisation; both have identical system permissions. | FR-17, UC-09, UC-10 |
| 15 | A client cannot be linked to a claim until at least one active policy record exists; where the product requires asset-level claiming, at least one asset must be registered under the relevant policy/section first. | FR-09, FR-21, UC-09 |
| 16 | Reminders/notifications are delivered by **outbound email only** — there is no in-app or SMS notification channel in Phase 1. Reminder frequency/threshold is Administrator-configurable and reminders continue until the outstanding item is resolved. | FR-07, NFR-11, UC-13 |
| 17 | Report generation access is scoped by role: Client → own organisation only; Broker → assigned clients only; Manager/Administrator → any client. | FR-14, FR-15, UC-08, [`user-roles.md`](user-roles.md) |
| 18 | Report layout must reproduce the Client-supplied template exactly (tab names/order, merged headers, cell addresses, totals, colour-key blocks, column widths); values are written as data, never as spreadsheet formulas. | FR-22, FR-32, UC-08 |
| 19 | The Underwriting Year rolls annually from the policy inception date (not the calendar year) and is used to group claims for loss-ratio reporting. | FR-23, UC-08 |
| 20 | A portfolio loss ratio cannot be produced for an underwriting year with no captured risk premium — it must render as unavailable, never as zero. | FR-16 |
| 21 | Broker and Client comments are two independently editable, separately reportable fields on each claim — distinct from the conversational Client-Broker communication log (FR-08). The Client comment remains editable via "leave a comment" even after claim closure. | FR-27, FR-13 |
| 22 | Where a Broker/Administrator lodges a claim on behalf of a Client, the lodging user and lodgement channel ("Broker-Assisted" vs "Client Self-Service") are recorded on the claim, and the Client-Primary contact is notified automatically with no confirmation step required. | FR-33, UC-15 |
| 23 | VAT amounts are always entered manually by the Broker from the insurer/repairer invoice — the system never calculates VAT. Net Claim = Gross Claim − Excess + VAT (system-calculated). | FR-25 |

## Cross-Cutting Themes

- **Advisory, not blocking**: the document checklist (Rule 4) and the late-reporting flag (Rule 1) both
  warn users without preventing progress — a deliberate design principle carried consistently through the
  BRD (see Section 6, Current State, and Section 14, Process Flow).
- **Least privilege + client data isolation**: Rules 5, 14, and 17 together ensure no client or broker can
  see another organisation's data (NFR-02).
- **Everything insurer-facing stays on email in Phase 1**: Rules 6, 7, 9, 10 all describe a system boundary
  where the Insurer interacts with the Broker by email, and the portal only ever records the *outcome* of
  that interaction — see [`process-flow.md`](process-flow.md) for the full end-to-end picture.
