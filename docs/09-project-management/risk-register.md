# Risk Register (RAID)

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 17. Key risks, assumptions,
> issues, and dependencies (RAID) identified for this project as at the BRD date (17 Sep 2026). Assumptions
> and constraints affecting scope are also documented in
> [`../01-overview/scope.md`](../01-overview/scope.md); this register focuses on items requiring active
> monitoring and mitigation. It should be maintained and reviewed jointly by TechXplo and the Client
> throughout delivery.

| Risk ID | Description | Category | Likelihood | Impact | Mitigation / Response | Owner | Status |
|---|---|---|---|---|---|---|---|
| RSK-01 | The final list of commercial products, product-specific workflows, and required documents has not yet been fully confirmed by the Client (see [`../02-requirements/open-questions.md`](../02-requirements/open-questions.md) Q-001), which could delay finalisation of build scope. | Scope | Medium | Medium | Client to confirm outstanding product/document information early in the design phase; any resulting build impact to be managed via the Change Request process (see accompanying Scope of Work). | Client / TechXplo | Open |
| RSK-02 | Feasibility of two-way email synchronisation (inbound Client replies auto-populating the portal) is not yet confirmed. | Technical | Medium | Low | TechXplo to complete a technical feasibility assessment early in the design phase; if not feasible for Phase 1, defer to Phase 2. | TechXplo | Open |
| RSK-03 | Manual migration of Policy, Client, and Insurer information (see [`../02-requirements/data-migration-requirements.md`](../02-requirements/data-migration-requirements.md)) introduces a risk of transcription errors or incomplete records, given no automated validation against a source system. | Data Quality | Medium | Medium | Mandatory verification/sign-off step per migrated Client record before go-live; use of the "Incomplete/draft" record state to prevent premature use of unverified data. | Client (Broker/Admin) | Open |
| RSK-04 | Historical claims data is not migrated into the portal; if any in-flight claims at go-live need continued tracking within the new system, this may require additional manual re-capture effort not currently scoped. | Data | Low–Medium | Medium | Confirm treatment of in-flight claims at cutover as part of the Data Migration Open Items (see [`../02-requirements/open-questions.md`](../02-requirements/open-questions.md) Q-012) before go-live planning is finalised. | Client / TechXplo | Open |
| RSK-05 | Hosting/data-residency approach is not yet confirmed, which may affect the Client's POPIA/FSCA compliance position. | Compliance | Low | High | TechXplo to propose a hosting option that satisfies POPIA/FSCA data-residency expectations, for the Client's approval during the design phase. | TechXplo / Client | Open |
| RSK-06 | SOE client contacts may be unfamiliar with a self-service claims portal, risking low adoption or continued reliance on email/phone. | Adoption | Medium | Medium | Provide onboarding/training materials and a walkthrough session for Client contacts as part of go-live activities (see accompanying Scope of Work, Milestone M6). | Client | Open |
| RSK-07 | Additional scope raised after BRD/SOW sign-off (e.g. Phase 2 items pulled forward) could extend the delivery timeline if not formally controlled. | Schedule | Medium | Medium | All post-sign-off changes to be managed through the Change Request process defined in the accompanying Scope of Work. | TechXplo / Client | Open |
| RSK-08 | The audit trail and retention design must reliably meet the FSCA's five-year record-keeping requirement; any gap could expose the Client to regulatory risk. | Regulatory | Low | High | Audit trail (FR-18) and retention requirements (NFR-08, NFR-09) are built into Phase 1 scope and will be explicitly tested during UAT (see [`../07-testing/test-cases.md`](../07-testing/test-cases.md) TC-14). | TechXplo | Open |

## Notes

- All 8 risks are recorded as **Open** as at BRD v0.7 (17 Sep 2026) — none have yet been closed or accepted.
- Several risks map directly to items in [`../02-requirements/open-questions.md`](../02-requirements/open-questions.md):
  RSK-01 ↔ Q-001; RSK-02 ↔ Q-004; RSK-04 ↔ Q-012/Q-013; RSK-05 ↔ Q-008.
- This register should be revisited at each project milestone and updated as risks are mitigated, escalate,
  or new risks emerge.
