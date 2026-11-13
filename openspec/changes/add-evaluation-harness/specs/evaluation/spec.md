## ADDED Requirements

### Requirement: Annotated Task Set
The evaluation SHALL use about 50 de-identified scenario tasks, split between management and procurement, each annotated with the correct outcome and the regulations that must be cited.

#### Scenario: Traceability to specs
- **WHEN** the task set is built
- **THEN** every spec scenario is covered by at least one task

### Requirement: Baseline Comparison
The evaluation SHALL compare LabSteward with B1 manual Excel workflow, B2 single LLM without tools, and B3 RAG-only question answering on the same tasks.

#### Scenario: Same tasks for all systems
- **WHEN** a comparison report is generated
- **THEN** all systems are scored on the identical task set and metrics

### Requirement: Reliability and Compliance Metrics
The evaluation SHALL report task success rate, pass^k with k = 4, regulation violation rate, field-extraction F1, residual value MAPE, price gap to the human-found lowest price, entity-matching precision, task time, and SUS score.

#### Scenario: Repeated runs
- **WHEN** a task is evaluated
- **THEN** it is run k times and counts toward pass^k only if all k runs succeed

### Requirement: Honest Reporting
The evaluation report MUST distinguish expected outcomes from measured results and MUST NOT report any number as a result before it is measured.

#### Scenario: Proposal-stage document
- **WHEN** a document is written before experiments are run
- **THEN** all numeric targets are labeled Expected Outcomes
