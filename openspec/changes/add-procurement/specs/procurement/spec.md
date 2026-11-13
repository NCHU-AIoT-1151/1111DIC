## ADDED Requirements

### Requirement: Inventory-First Procurement
The system SHALL query in-stock, idle, and transferable assets before any web search and SHALL suggest borrowing or transfer when inventory satisfies the requested specification and quantity.

#### Scenario: Idle workstation available
- **WHEN** a member requests a GPU workstation and an idle workstation with matching specification exists
- **THEN** the system suggests borrowing or transferring it
- **AND** no web search is performed

### Requirement: Minimum Valid Quotations
The system SHALL collect at least 3 valid quotations after entity matching and de-duplication, relaxing search conditions up to K_max times, and SHALL mark the request NEED_MANUAL_QUOTES if fewer than 3 remain.

#### Scenario: Insufficient quotations
- **WHEN** only 2 valid quotations remain after K_max searches
- **THEN** the request is marked NEED_MANUAL_QUOTES and routed to a human

### Requirement: Two-Stage Entity Matching
The system SHALL treat two offers as the same product only if their structured specification fields match and the LLM matcher agrees.

#### Scenario: Same name, different capacity
- **WHEN** two offers share a product name but differ in memory capacity
- **THEN** they are treated as different products

### Requirement: Abnormal Price Detection
The system SHALL flag any quotation priced substantially below the median of valid quotations and SHALL state possible causes such as specification mismatch, refurbished goods, or unreliable sellers.

#### Scenario: Offer 40 percent below median
- **WHEN** a quotation is 40% below the median price
- **THEN** it is flagged and excluded from the top recommendation until a human confirms it

### Requirement: Threshold Routing and Split-Order Detection
The system SHALL determine the procurement route from the total amount using rules.yaml and SHALL flag suspected split orders whose combined amount crosses a threshold.

#### Scenario: Split order
- **WHEN** two requests of 80,000 TWD each with similar specifications are submitted by the same project within the configured period
- **THEN** both are flagged as a suspected split order and routed to human review

### Requirement: Traceable Purchase List
The system SHALL output a purchase list containing ranked offers, total cost of ownership, quotation sources, procurement route, and budget check result.

#### Scenario: Over budget
- **WHEN** the best offer's total exceeds the stated budget
- **THEN** the purchase list shows the budget gap and alternative offers within budget
