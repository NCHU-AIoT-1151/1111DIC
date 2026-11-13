## ADDED Requirements

### Requirement: Dual-Track Residual Value
The system SHALL output both a book value computed by the straight-line method over the minimum useful life and a market value interval, together with an explanation of their difference.

#### Scenario: Three-year-old oscilloscope
- **WHEN** a user asks the value of an oscilloscope bought three years ago
- **THEN** the response contains the book value, a market value interval, the data source, and the reason the two differ

### Requirement: Confidence-Aware Market Estimation
The system SHALL use a gradient boosting model with an 80% bootstrap interval when at least N_min comparable listings exist and SHALL otherwise fall back to a log-linear category rate and mark the result LOW_CONFIDENCE.

#### Scenario: Sparse market data
- **WHEN** fewer than N_min comparable listings exist for the asset category
- **THEN** the market value is computed from the category depreciation rate
- **AND** the result is marked LOW_CONFIDENCE

### Requirement: Option-Based Advice
The system SHALL return one of KEEP_USING, REPAIR_OR_KEEP, TRANSFER_OR_SHARE, or DISPOSE with its supporting reason, and SHALL recommend DISPOSE only when the asset is not working and the repair cost exceeds the upper bound of the market value interval.

#### Scenario: Low utilization
- **WHEN** a working asset has utilization below u_min
- **THEN** the advice is TRANSFER_OR_SHARE
