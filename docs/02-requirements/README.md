# Requirements

What the system must do, and the constraints it must do it within.

## Contents

- [`functional-requirements.md`](functional-requirements.md) — features, acceptance criteria
- [`non-functional-requirements.md`](non-functional-requirements.md) — performance, security, compliance (e.g. POPIA/FICA if handling SA financial/insurance data), availability targets
- [`user-roles.md`](user-roles.md) — brokers, clients, admins, and what each can do
- [`open-questions.md`](open-questions.md) — requirements still being clarified with stakeholders
- [`use-cases.md`](use-cases.md) — the "how" behind each functional requirement (actors, business rules, step-by-step scenarios)
- [`business-rules.md`](business-rules.md) — quick-reference index of cross-cutting business rules
- [`traceability-matrix.md`](traceability-matrix.md) — consolidated FR ↔ Use Case ↔ Test Case cross-reference
- [`data-requirements.md`](data-requirements.md) — core data entities, field-level dictionary, and the logical ERD
- [`product-document-requirements.md`](product-document-requirements.md) — the 29 confirmed products and required documents per product
- [`process-flow.md`](process-flow.md) — end-to-end claims process flow and client-facing status stages
- [`data-migration-requirements.md`](data-migration-requirements.md) — what existing data must be brought into the system ahead of go-live
- [`templates/`](templates/) — copy-paste blocks for adding new entries to any of the above, plus a Gherkin-style user story template

## Adding a requirement

1. Pick the right doc (functional, non-functional, role, or open question).
2. Copy the matching template from `templates/`.
3. Assign the next sequential ID (FR-XXX, NFR-XXX, US-XXX, Q-XXX) — never reuse a retired ID, mark it Deprecated instead.
4. Fill in every field; leave nothing as `<Placeholder>`.
