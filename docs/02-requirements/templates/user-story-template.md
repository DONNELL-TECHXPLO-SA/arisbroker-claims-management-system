<!--
Use this format when a requirement is best expressed from a specific user's
point of view. Link the story's ID back to the FR-XXX it satisfies, if any.
-->

### US-XXX: <Short title>

**As a** <type of user, e.g. Broker, Claims Handler, Client, Admin>
**I want** <goal / capability>
**So that** <benefit / reason>

**Linked requirement:** FR-XXX (if applicable)

**Acceptance criteria** (Gherkin style)

```gherkin
Scenario: <describe the scenario>
  Given <initial context>
  And <additional context>
  When <action taken>
  Then <expected outcome>
  And <additional outcome>
```

**Notes / edge cases**

- <anything unusual to consider, e.g. what happens if a claim is submitted with missing documents>
