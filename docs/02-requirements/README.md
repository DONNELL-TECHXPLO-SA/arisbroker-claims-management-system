# Requirements

What the system must do, and the constraints it must do it within.

## Contents

- [`functional-requirements.md`](functional-requirements.md) — features, acceptance criteria
- [`non-functional-requirements.md`](non-functional-requirements.md) — performance, security, compliance (e.g. POPIA/FICA if handling SA financial/insurance data), availability targets
- [`user-roles.md`](user-roles.md) — brokers, clients, admins, and what each can do
- [`open-questions.md`](open-questions.md) — requirements still being clarified with stakeholders
- [`templates/`](templates/) — copy-paste blocks for adding new entries to any of the above, plus a Gherkin-style user story template

## Adding a requirement

1. Pick the right doc (functional, non-functional, role, or open question).
2. Copy the matching template from `templates/`.
3. Assign the next sequential ID (FR-XXX, NFR-XXX, US-XXX, Q-XXX) — never reuse a retired ID, mark it Deprecated instead.
4. Fill in every field; leave nothing as `<Placeholder>`.
