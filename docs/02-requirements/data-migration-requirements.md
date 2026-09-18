# Data Migration Requirements

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 16. Sets out what existing
> information is to be brought into the Claims Management Portal ahead of go-live, and how, per the
> Client's confirmation during discovery.

## Migration Scope

The following existing information is to be migrated into the portal ahead of go-live:

- **Policy information** (per [`data-requirements.md#121-policy-information`](data-requirements.md#121-policy-information))
  — policy number, period, insurer(s), class, section(s), sum insured/limit of indemnity, excess(es), and
  terms/conditions.
- **Client information** (per [`data-requirements.md#124-client-information-insured--soe`](data-requirements.md#124-client-information-insured--soe))
  — client name, assigned broker, primary/secondary contact users, and linked polic(ies).
- **Insurer information** (per [`data-requirements.md#123-insurer-information`](data-requirements.md#123-insurer-information))
  — insurer name and contact details.

**Historical claim records are explicitly out of scope** for migration into the portal in Phase 1. Existing
claims remain in the Client's Lucid repository, which will continue to be retained to satisfy the FSCA
five-year record-keeping requirement for claims registered prior to go-live. Only claims registered on or
after go-live will exist within the new system (see Open Items below regarding any in-flight claims at
cutover).

## Migration Approach

The migration of Policy, Client, and Insurer information will be a **manual process** rather than an
automated system-to-system integration. Client staff (Administrator and/or Broker users) will be
responsible for performing the data capture:

- Administrator and/or Broker users will capture each Client, Policy, and Insurer record directly into the
  portal (per [`use-cases.md`](use-cases.md) UC-09: Maintain Client, Policy & Asset Records), using source
  information drawn from existing policy summaries and the Lucid repository.
- No automated ETL, API integration, or bulk data feed from Lucid (or any other existing system) is in
  scope for Phase 1.
- TechXplo will assess, during the design phase, whether a structured bulk-upload template (e.g. a
  spreadsheet import) would materially reduce the manual effort involved for the Client's ~14–15 SOE
  clients and their associated policies — this would still constitute a manual, Client/Broker-driven data
  load rather than a system integration.
- Manual migration will use the same data-entry functionality intended for day-to-day operation (UC-09), so
  no separate migration tooling needs to be built.

## Data Quality & Verification

- Each manually captured Client, Policy, and Insurer record must be reviewed for accuracy and completeness
  by an Administrator (or the assigned Broker) before being made available for claim submission — the
  "Incomplete/draft" record state defined in UC-09 supports this.
- TechXplo recommends a formal verification/sign-off step — e.g. each Broker confirms their own assigned
  portfolio of clients and policies has been captured correctly — prior to go-live.

## Migration Timing & Prioritisation

Given the volume involved (approximately 14–15 SOE clients, each potentially holding multiple policies
across several insurers), migration should be prioritised and phased ahead of go-live, in an order to be
agreed with the Client during the design phase (e.g. by client criticality, policy renewal date, or claims
activity level).

## Open Items

| Open Item | Detail |
|---|---|
| In-flight claims at cutover | Whether claims open/in-progress at the point of go-live need to be manually re-captured into the portal for continued tracking, or whether they remain managed via the legacy manual process until closure. See [`open-questions.md`](open-questions.md) Q-012. |
| Source format of policy summaries | Confirmation of the source format of existing policy summaries (e.g. PDF, Word, Excel), to assess whether a bulk-upload template is feasible. See [`open-questions.md`](open-questions.md) Q-013. |

## Related Risk

The manual nature of this migration is tracked as a delivery risk — see
[`../09-project-management/risk-register.md`](../09-project-management/risk-register.md) RSK-03 (data
quality/transcription risk) and RSK-04 (historical claims data not migrated).
