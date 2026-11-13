## ADDED Requirements

### Requirement: Configurable Regulation Rules
The system SHALL load all monetary thresholds, asset definitions, and minimum useful lives from a versioned rules.yaml file and MUST NOT hard-code these values in code or prompts.

#### Scenario: Asset versus item classification
- **WHEN** an item has unit price 8,000 TWD
- **THEN** it is classified as an item (物品), not a property asset (財產)

#### Scenario: School-specific threshold
- **WHEN** a school's rules.yaml sets a different small-value procurement threshold
- **THEN** procurement routing uses the configured value without code changes

### Requirement: Evidence Citation
The system SHALL attach at least one regulation clause or data source to every recommendation.

#### Scenario: No supporting evidence
- **WHEN** retrieval returns no relevant regulation or data source for a recommendation
- **THEN** the recommendation is marked LOW_EVIDENCE and routed to human review

### Requirement: Human-in-the-Loop Approval
The system SHALL require human approval before committing any state-changing action and SHALL also require approval when a rule violation or uncertainty above threshold tau is detected.

#### Scenario: Disposal draft
- **WHEN** the agent produces a disposal request draft
- **THEN** nothing is committed until the Custodian and PI approve it

#### Scenario: Rejected result
- **WHEN** the reviewer rejects a result with feedback
- **THEN** the agent restarts from intent recognition with the feedback appended

### Requirement: Append-Only Audit Log
The system SHALL write an append-only audit record for every committed action containing the query, result, evidence, rule-file version, decision, approver, and timestamp.

#### Scenario: Committed lending
- **WHEN** a lending record is committed
- **THEN** an audit record exists with the approver and the rules.yaml version used
