# Data Requirements

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 12 ("Data Table – Field
> Mapping"), Appendix D ("Report Specifications & Field Provenance"). This defines the core data entities
> the system must hold and, per Appendix D, maps every field of the two contracted reports back to its
> source. **This is a requirements-level data dictionary, not a physical database schema** — table design,
> normalisation, and indexing are technical design decisions the development team will make during the
> architecture/design phase (see [`../04-design/database/README.md`](../04-design/database/README.md)),
> informed by this document.

## 12.1 Policy Information

| Field | Data Type | Description / Notes | Mandatory |
|---|---|---|---|
| Policy Number | Text | Unique insurer-issued policy identifier. | Yes |
| Policy Period | Date range (start/end) | Period of cover. | Yes |
| Insurer(s) | Text / lookup | One or more insurers on the policy. | Yes |
| Class of Insurance | Lookup (Motor / Non-Motor) | High-level class as used by the Client. | Yes |
| Section(s) of Policy | Lookup (multi-select) | One of the 29 confirmed products — see [`product-document-requirements.md`](product-document-requirements.md). | Yes |
| Sum Insured / Limit of Indemnity | Currency | Cover limit applicable to the section. | Yes |
| Excess(es) | Currency / percentage | Client's contribution per claim before Insurer liability arises. | Yes |
| Policy Terms & Special Conditions | Text / attachment | E.g. "theft must be accompanied by forcible entry". | No |
| Policy Schedule Document | File attachment | Uploaded policy schedule/summary; not necessarily visible to all roles. | Yes |
| Risk Premium Paid (per underwriting year) | Currency | Captured by the Broker for each underwriting year of the policy. Required for the loss ratio (FR-16, FR-24). | Yes, per underwriting year |
| Premium Payment Frequency | Lookup | E.g. Yearly, Monthly. Reported on the Claim-Loss Ratio tab (FR-16, FR-24). | Yes |
| Policy Term (months) | Number (calculated) | Derived from the policy period; shown as "TERM" in the report header (FR-29). | Auto |

## 12.2 Asset Information

Clients claim against a specific policy, section, and — where the product requires it — a specific insured
asset (e.g. an individual vehicle under a Motor Fleet policy, or an individual building under a Property
policy). The Broker/Administrator maintains this asset register per policy (see UC-09).

| Field | Data Type | Description / Notes | Mandatory |
|---|---|---|---|
| Asset ID | Auto-generated | Unique identifier. | Yes (auto) |
| Linked Policy / Section | Lookup | The policy and section the asset falls under. | Yes |
| Asset Description | Text | E.g. vehicle make/model/registration, building address, item description. | Yes |
| Asset Type | Lookup | E.g. Vehicle, Building, Equipment, Other — aligned to the product/section. | Yes |
| Asset Value / Sum Insured (if asset-specific) | Currency | Only where the policy insures assets individually (e.g. scheduled items). | No |
| Asset Label / Barcode | Text | The client's own asset label or barcode, reported alongside Asset ID on the Claims History Report and as Barcode on the Non-Motor Claims History tab (FR-14, FR-15). | No |
| Vehicle Identification Number (VIN) | Text | Motor assets only. Reported on the Motor Claims History tab (FR-26). | Conditional |
| Licence Plate | Text | Motor assets only. Reported on the Motor Claims History tab (FR-26). | Conditional |
| Serial Number | Text | Non-motor assets. Reported on the Non-Motor Claims History tab (FR-15). See [`open-questions.md`](open-questions.md) Q-016 re: Serial No. vs Barcode. | Conditional |

## 12.3 Insurer Information

| Field | Data Type | Description / Notes | Mandatory |
|---|---|---|---|
| Insurer Name | Text / lookup | Name of the underwriting insurer. | Yes |
| Insurer Contact / Branch | Text | Claims department contact, if applicable. | No — open item |
| Products Underwritten | Lookup (multi-select) | Cross-reference to the Client's product list. | No — open item |

## 12.4 Client Information (Insured / SOE)

| Field | Data Type | Description / Notes | Mandatory |
|---|---|---|---|
| Client Name | Text | Legal/registered name of the SOE client. | Yes |
| Client Type | Lookup | State-Owned Enterprise (commercial lines only). | Yes |
| Assigned Broker / Claims Handler | Lookup (user) | Determines Broker-level access scoping (FR-10). | Yes |
| Primary Contact User | Name, email, phone | First of up to 2 permitted client-side users. | Yes |
| Secondary Contact User | Name, email, phone | Second (optional) client-side user, e.g. the manager of the primary contact. | No |
| Linked Polic(ies) | Lookup (1-to-many) | One client may hold multiple policies/insurers. | Yes |
| Onboarding Date | Date | Date the client was set up on the portal. | No |

## 12.5 Broker Information (Internal User)

| Field | Data Type | Description / Notes | Mandatory |
|---|---|---|---|
| Broker Name | Text | Full name of the internal user. | Yes |
| Role | Lookup | Administrator / Manager / Broker-User. | Yes |
| Assigned Clients | Lookup (multi) | Applicable to Broker-User role only; Manager/Administrator implicitly see all. | Yes (Broker-User) |
| Contact Details | Text | Email, phone. | Yes |
| MFA Enrolment Status | Boolean | Must be "True" before first productive login. | Yes |

## 12.6 Claim Information

| Field | Data Type | Description / Notes | Mandatory |
|---|---|---|---|
| In-House Claim Number | Auto-generated text | System-generated on submission (FR-02). | Yes (auto) |
| Insurer Claim Number | Text | Captured once allocated by the Insurer (FR-06). | No, then Yes to progress |
| Client (linked) | Lookup | The SOE client the claim belongs to. | Yes |
| Policy Number (linked) | Lookup | The specific policy/section being claimed against. | Yes |
| Asset (linked) | Lookup | The specific insured asset being claimed against, where the product requires asset-level claiming (FR-21). | Conditional |
| Date of Loss | Date | Date the incident occurred. | Yes |
| Date Reported / Registered | Date (system) | Auto-captured on submission; used for the 30-day rule. | Yes (auto) |
| Location of Loss | Text | Where the incident occurred. | Yes |
| Claim Type | Lookup | Motor / Non-Motor and specific product. | Yes |
| Late-Reported Flag | Boolean (system-calculated) | Set automatically where reporting exceeds 30 days (FR-19). | Auto |
| Assigned Broker | Lookup | Drives access scoping and notifications. | Yes |
| Lodged By | Lookup (user) | The user who performed the lodgement (Client, Broker-User, or Administrator); distinct from the Client the claim belongs to (FR-33). | Yes (auto) |
| Lodgement Channel | Lookup | "Client Self-Service" or "Broker-Assisted", set automatically based on who performed the lodgement (FR-33). | Yes (auto) |
| Assessor Name / Company | Text | Field 1 of 3 assessor fields, captured once appointed by the Insurer (FR-11). | No |
| Assessor Contact Details | Text | Field 2 of 3 assessor fields (phone/email). | No |
| Assessor Date Appointed | Date | Field 3 of 3 assessor fields. | No |
| Claim Status | Lookup (staged list) | See [`process-flow.md#claim-status-stages-client-facing`](process-flow.md#claim-status-stages-client-facing) for the full status/stage list. | Yes |
| Decision Outcome | Lookup | Settled (Cash / Repair / Replacement), Repudiated (declined by the Insurer), or Within Excess. | No, populated at decision stage |
| Repudiation Reason | Text / lookup | Mandatory where Decision Outcome = Repudiated; captures the Insurer's stated reason for declining the claim. | Conditional (mandatory if Repudiated) |
| Settlement Amount | Currency | Amount settled by the Insurer at the decision stage. Where a figure is subsequently confirmed as paid, it is held in Amount Paid. | No |
| Excess Paid | Currency | Excess actually paid by the Client on a repair or replacement settlement; distinct from the Excess Applied used in the claim calculation. | No |
| Recoveries | Currency | Amounts recovered, where applicable. | No |
| Salvage Value | Currency | Value of salvage, where applicable. | No |
| Closure Date | Date (system) | Auto-captured when the claim is closed. | No (auto) |
| Loss Description | Text | Free-text account of the incident, captured by the Client at lodgement. Reported as "Description" / "Claim Description" (FR-26). | Yes |
| Person Involved | Text | Name of the person who was in the incident, captured by the Client. May be the policyholder, an employee of the insured or a third party, so held as free text (FR-26). | Conditional (motor) |
| Case Number | Text | Police case number where one exists, captured by the Broker (FR-26). | No |
| Underwriting Year | Number (system-calculated) | Derived from date of loss against policy inception, rolling annually (FR-23). | Auto |
| Gross Claim Amount | Currency | Total claim value, captured by the Broker (FR-25). | Yes at decision stage |
| Excess Applied | Currency | Defaults from the policy section; may be overridden by the Broker (FR-25). | Yes at decision stage |
| VAT Amount | Currency | Entered manually by the Broker from the insurer or repairer invoice. Not calculated by the system (FR-25). | No |
| Net Claim Amount | Currency (calculated) | Gross claim less excess plus VAT (FR-25). | Auto |
| Progress | Lookup (system-calculated) | Outstanding or Resolved, derived from claim status (FR-30). | Auto |
| Broker Comment | Text | Broker-authored commentary, reported as its own column (FR-27). | No |
| Client Comment | Text | Client-authored commentary, entered through a "leave a comment" action at the foot of the claim, available after closure (FR-27). | No |
| Amount Paid | Currency | The figure actually settled by the Insurer, captured by the Broker. Reported as its own column on the Non-Motor Claims History tab, distinct from the calculated Net Claim Amount — see [`open-questions.md`](open-questions.md) Q-017. | No |

## 12.7 Document Information

| Field | Data Type | Description / Notes | Mandatory |
|---|---|---|---|
| Document ID | Auto-generated | Unique identifier. | Yes (auto) |
| Linked Claim / Client / Policy | Lookup | The record the document is attached to. | Yes |
| Document Type | Lookup | E.g. Police Report, Photos, Quotation, Third-Party Details, Agreement of Loss (unsigned/signed), Repair Authorisation, Proof of Payment, Policy Schedule, Ad-hoc/Other. | Yes |
| Uploaded By | Lookup (user, auto) | Captured automatically for audit purposes. | Yes (auto) |
| Upload Date/Time | Date-time (auto) | Captured automatically. | Yes (auto) |
| File | Attachment | The document itself. | Yes |

**Architecture note (from the BRD, non-binding for this phase)**: the BRD frames documents as held
centrally in a single "Document Management" grouping rather than embedded within Client/Policy/Asset
records, so that storage, retention, and volume-handling logic (NFR-04, NFR-09) live in one place, while
the UI can still display a Policy's or Claim's documents as if they "lived with" that record. This is
BRD context for the design phase, not a decision made now.

## 12.8 Document Requirements & Responsibility Matrix

Maps each generic document type to the claim stage at which it is required and the party responsible for
providing or uploading it. This underpins the required-document checklist (FR-03) and is supplemented by
the full per-product document lists in
[`product-document-requirements.md`](product-document-requirements.md).

| Document Type | Required at Claim Stage | Responsible Party | Mandatory / Conditional |
|---|---|---|---|
| Claim Form / Loss Narrative | Claim Submission | Client | Mandatory |
| Police Report | Claim Submission (where applicable, e.g. theft, injury, hijacking) | Client | Conditional |
| Photos of Damage / Incident | Claim Submission | Client | Mandatory (where applicable) |
| Third-Party Details | Claim Submission | Client | Conditional (motor claims) |
| Quotation(s) for Repair/Replacement | Claim Submission / Document Upload | Client | Mandatory (where applicable) |
| Policy Schedule / Summary | Client & Policy Set-up (pre-claim) | Broker / Administrator | Mandatory |
| Ad-hoc Insurer-Requested Documents (e.g. tracker report, licence endorsement confirmation, ride-hailing registration confirmation) | Under Assessment (as requested by Insurer) | Client submits; Broker requests | Conditional |
| Assessment Report | Under Assessment | Insurer/Assessor (retained by the Insurer; not provided to the Broker) | Not held in the portal (Phase 1) |
| Agreement of Loss – Unsigned | Decision Made – Settled (Cash) | Broker (uploads, received from Insurer via email) | Mandatory if settled in cash |
| Agreement of Loss – Signed | Awaiting Signed AOL | Client (uploads signed copy) | Mandatory if settled in cash |
| Repair / Replacement Authorisation | Decision Made – Settled (Repair/Replacement) | Broker (uploads, received from Insurer) | Mandatory if settled by repair/replacement |
| Insurer Repudiation Letter (with Repudiation Reason) | Decision Made – Repudiated | Broker (uploads, received from Insurer) | Mandatory if repudiated |
| Proof of Payment | Claim Closure | Broker (uploads, received from Insurer via email) | Mandatory if settled |

> **Open item**: this generic, stage-based list is supplemented by the full, product-specific document
> requirements supplied by the Client for 28 of the 29 confirmed products — see
> [`product-document-requirements.md`](product-document-requirements.md). A document list for Credit
> Insurance has not yet been supplied — see [`open-questions.md`](open-questions.md) Q-002.

## 12.9 User / Access Information

| Field | Data Type | Description / Notes | Mandatory |
|---|---|---|---|
| User ID | Auto-generated | Unique identifier. | Yes (auto) |
| Name | Text | Full name. | Yes |
| Role | Lookup | Administrator / Manager / Broker-User / Client-Primary / Client-Secondary. | Yes |
| Linked Organisation | Lookup | The Broker firm, or the specific Client SOE. | Yes |
| Email | Text | Used for login and notifications. | Yes |
| MFA Status | Boolean | Must be enabled before productive use (NFR-01). | Yes |
| Active / Inactive | Boolean | Controls login ability. | Yes |
| Last Login | Date-time (auto) | For security monitoring. | No (auto) |

## 12.10 Entity Relationship Diagram (Logical)

The BRD includes a **logical/conceptual** Entity Relationship Diagram showing how the core entities above
relate to one another. It is explicitly *not* a physical database schema — table design, indexes, and
normalisation are technical design decisions deferred to the architecture/design phase. The relationships
it confirms are:

- **Client** (1) — (many) **Policy** — each Policy belongs to one Client and references one or more
  Insurers.
- **Policy** (1) — (many) **Asset** — Assets exist only where a product requires asset-level claiming, and
  are conditional/zero-or-more.
- **Policy** (1) — (many) **Claim** — a Claim always references exactly one Policy (and, conditionally,
  one Asset) and one Client.
- **Broker/User** (1) — (many) **Client** — each Client is assigned to one Broker/User (or organisation);
  each User is linked to one Client or Broker organisation (see Section 15 access model).
- **Claim** (1) — (many) **Document** — Documents mostly link directly to a Claim; the Policy Schedule
  Document is the one document type that links directly to a Policy instead.

## 12.11 Company & Report Settings

Broker organisation details and standard report content, captured once by the Administrator (FR-31) and
pulled into every generated report rather than repeated in each report definition.

| Field | Data Type | Description / Notes | Mandatory |
|---|---|---|---|
| Registered Company Name | Text | The broker's legal registered name, shown on the BROKER line of the Performance Report header. | Yes |
| Trading Name | Text | Trading name where it differs from the registered name (e.g. t/a Aris Brokers). | No |
| FSP Licence Number | Text | Financial Sector Conduct Authority licence number, shown on generated reports. | Yes |
| Company Registration Number | Text | Registered company number. | No |
| Company Logo | File attachment | Used on the portal and on generated reports and documents. | No |
| Company Contact Details | Text | Telephone, email and physical address as they appear on reports. | No |
| Report Disclaimer Text | Text (per report type) | Standard disclaimer reproduced on each report; held separately for each report type so wording can differ. **Wording still to be supplied by the Client** — see [`open-questions.md`](open-questions.md) Q-015. | Yes |
| VAT Rate Label | Text / percentage | Used only as the report column heading (e.g. "VAT @ 15%"). VAT amounts themselves are entered manually per claim (FR-25) and are not calculated from this value. | Yes |
| Default Reminder Interval | Number (days) | The default inactivity period after which a reminder is generated (FR-07); may be varied by the Administrator. | Yes |
| Status Label & Colour Map | Lookup table | Maps each internal claim status to the label and colour used on generated reports, so that the report legend and row shading stay consistent with the Client-supplied template. | Yes |

## Appendix D-Style Report Field Provenance

The BRD's Appendix D maps **every field** of the two contracted report workbooks back to its source (Client
entry, Broker entry, System record, or System calculation). This is the authoritative reference for what
data the Claims History Report and Performance Report require to be held in the system — see
[`../04-design/README.md`](../04-design/README.md) for how this feeds future report/database design.
Summary by report:

### Claims History Report (workbook of 2 tabs: "Index & Key", "Summary") — realises FR-14, FR-22, FR-28, FR-29

The Summary tab (the all-claims register) reports, per claim: Insured Year, Date of Loss, Date Registered,
User (Person Involved), Section of Cover, Location, Asset ID, Asset Label, Description, Claim Number
(Insurer's), Gross, Excess, VAT 15%, Net, Status, with column totals for Gross/Excess/VAT/Net and
row-colouring by claim status. The Index & Key tab carries the report name/date, addressee, disclaimer, and
the status colour-key — all sourced from Company & Report Settings (§12.11) and the Client record.

### Performance Report (workbook of 4 tabs: "Claim-Loss Ratio", "Summary Report", "Non-Motor Claims History", "Motor Claims History") — realises FR-15, FR-16, FR-22, FR-28, FR-29, FR-30

- **Claim-Loss Ratio tab**: header (Insured name, Broker, Product, Term, Inception date, Policy no.), then
  per underwriting year — number of claims, total claim value, risk premium paid, payment frequency,
  portfolio loss ratio — with a grand total row, followed by claim-level detail rows (Claim No., class of
  insurance, claim description, date of loss, underwriting year, payment frequency, total claim).
- **Summary Report tab**: Outstanding / Resolved / Total counts, split separately for Non-Motor and Motor
  claims (system-calculated from Progress, FR-30).
- **Non-Motor Claims History tab** (two-row merged header, 20 fields incl. group headings "Asset details"
  and "Quantum of Loss"): Asset type, Claim No., Claim type, Person's Name, Date of Loss, Case No.,
  Description, Serial No., Barcode, Claim Submission date, Total Claim, Excess, VAT @ 15%, Amount Paid, Net
  Claim, Claim Status, Progress, Aris Brokers Comments, Client Comments (rendered under the client's own
  name), Colour Code Key.
- **Motor Claims History tab**: same structure as Non-Motor, with Asset details sub-headings of VIN Number
  and Licence Plate (instead of Serial No./Barcode), a Quantum of Claim group with Amount Paid (instead of
  a separate Net Claim column), and no separate Amount Paid comment column.

The Matters Arising tab in the Client's own template is a meeting action register, not claims data, and is
**excluded from the system** — the Broker continues to maintain it outside the portal (FR-15). The
standalone "Settlement Report" concept from earlier BRD drafts is superseded by these two workbooks; its
values are reported within the tabs above.

**Exact cell-level layout** (merged ranges, cell addresses, column widths, totals-row positioning) is fixed
in the BRD's Appendix H and referenced by FR-32 (Report Layout Fidelity) — it is retained in the source BRD
as an implementation-fidelity specification rather than duplicated here, since it is directly consumed
during the report-generation build rather than being a business/data requirement in its own right.
