# Requirements: Unit Conversion Service

**Jira Ticket:** DF-001


## Functional Requirements

### REQ-01: Convert Measurement

- The system must convert a numeric value from a source unit to a target unit.
- Supported conversions (bidirectional):
  - metres <-> feet
  - kilometres <-> miles
  - litres <-> gallons
- The request contains: value, sourceUnit, targetUnit.
- Each conversion result must be persisted in the database with a server-generated UUID.
- The response contains: id (server-generated), inputValue, sourceUnit, targetUnit, result.

### REQ-02: List Supported Units

- The system must expose the list of supported units with their measurement system (metric/imperial).
- Units are stored in the database and returned via GET /api/units.
- Each unit has: id, name, system.

### REQ-03: Validate Input

- Non-numeric values must be rejected.
- Incompatible unit pairs (e.g., metres to gallons) must be rejected.
- Missing required fields must be rejected.
- All validation errors must return HTTP 400 with a ValidationError body containing: id (UUID), message (string), and optionally field (string or null — present when the error relates to a specific input field).

## Acceptance Criteria

### AC-01: Successful Conversion

Given a valid value, source unit, and target unit,
When the user submits a conversion via POST /api/convert,
Then the system persists the result and returns a ConversionResult with a server-generated id.

### AC-02: Reverse Conversion

Given a conversion from metres to feet returns X,
When the user converts X feet to metres,
Then the result matches the original value (within rounding precision).

### AC-03: Incompatible Units Rejected

Given source unit "metres" and target unit "gallons",
When the user submits a conversion,
Then the system returns HTTP 400 with a ValidationError indicating incompatible units.

### AC-04: Non-Numeric Value Rejected

Given a non-numeric value (e.g., "abc"),
When the user submits a conversion,
Then the system returns HTTP 400 with a ValidationError indicating invalid input.

### AC-05: Missing Fields Rejected

Given a request missing a required field (value, sourceUnit, or targetUnit),
When the user submits a conversion,
Then the system returns HTTP 400 with a ValidationError.

### AC-06: List Units

Given the system has pre-loaded unit definitions,
When the user calls GET /api/units,
Then the system returns all supported units with id, name, and system fields.

## Business Rules

- Unit compatibility groups: length (metres, feet, kilometres, miles), volume (litres, gallons).
- Conversion within a group is valid. Cross-group is invalid.
- Supported units are pre-loaded at application startup (seed data or migration).
- No user accounts or authentication.

## Out of Scope

- User accounts or authentication.
- Additional unit types beyond the six listed.
- Batch conversions.
