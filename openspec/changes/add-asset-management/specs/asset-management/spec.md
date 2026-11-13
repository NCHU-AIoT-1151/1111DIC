## ADDED Requirements

### Requirement: Asset Import from Documents
The system SHALL extract item name, specification, amount, date, and vendor from an uploaded invoice or quotation and SHALL propose a standard asset category and an asset-or-item classification.

#### Scenario: Low-confidence field
- **WHEN** a required field is extracted with confidence below the threshold
- **THEN** the system asks the Custodian to confirm that field before creating the record

### Requirement: Asset Lifecycle State Machine
The system SHALL manage every asset through the states IN_STOCK, LENT, MAINTENANCE, PENDING_DISPOSAL, and DISPOSED and SHALL reject any transition not defined by the state machine.

#### Scenario: Illegal transition
- **WHEN** an action attempts to move an asset from DISPOSED to LENT
- **THEN** the transition is rejected and no record is changed

### Requirement: Lending with Conflict Check
The system SHALL reject a lending request whose period overlaps an existing lending of the same asset and SHALL send a reminder before the due date.

#### Scenario: Overlapping request
- **WHEN** a member requests an oscilloscope for a period that overlaps an existing lending
- **THEN** the request is rejected
- **AND** available alternative assets or periods are suggested

### Requirement: Disposal Eligibility Check
The system SHALL check minimum useful life and working condition before drafting a disposal request and MUST NOT draft an ordinary disposal for a working asset.

#### Scenario: Expired but working
- **WHEN** an asset has exceeded its minimum useful life and still works normally
- **THEN** the system recommends continued use or transfer instead of disposal

#### Scenario: Broken before minimum life
- **WHEN** an asset has not reached its minimum useful life and is irreparably broken
- **THEN** the system drafts a special-case disposal request that requires human approval

### Requirement: Custodian Departure Check
The system SHALL list all assets held by a custodian and all unreturned lendings when the custodian is marked as leaving the lab.

#### Scenario: Graduating student
- **WHEN** a graduating student still holds 3 assets and 1 unreturned lending
- **THEN** the system creates transfer and return tasks for the Custodian and PI
