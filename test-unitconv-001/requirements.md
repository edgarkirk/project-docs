# Requirements: Unit Conversion Service

## Functional Requirements

### REQ-01: Convert Measurement

- The system must convert a numeric value from a source unit to a target unit.
- Supported conversions (bidirectional):
  - metres <-> feet
  - kilometres <-> miles
  - litres <-> gallons
- The result must be returned as a numeric value.

### REQ-02: List Supported Units

- The system must expose the list of supported units with their measurement system (metric/imperial).

### REQ-03: Validate Input

- Non-numeric values must be rejected.
- Incompatible unit pairs (e.g., metres to gallons) must be rejected.
- Missing required fields must be rejected.
- All validation errors must return HTTP 400 with a JSON body containing a `message` field.

### REQ-04: Frontend Conversion Form

- The user must be able to enter a numeric value, select source and target units, and submit.
- On success, the converted result is displayed.
- On validation error, the error message is displayed.

## Acceptance Criteria

### AC-01: Successful Conversion

Given a valid value, source unit, and target unit,
When the user submits a conversion,
Then the system returns the converted result.

### AC-02: Reverse Conversion

Given a conversion from metres to feet returns X,
When the user converts X feet to metres,
Then the result matches the original value (within rounding precision).

### AC-03: Incompatible Units Rejected

Given source unit "metres" and target unit "gallons",
When the user submits a conversion,
Then the system returns HTTP 400 with a message indicating incompatible units.

### AC-04: Non-Numeric Value Rejected

Given a non-numeric value (e.g., "abc"),
When the user submits a conversion,
Then the system returns HTTP 400 with a message indicating invalid input.

### AC-05: Missing Fields Rejected

Given a request missing the `value` field,
When the user submits a conversion,
Then the system returns HTTP 400.

### AC-06: Frontend Displays Result

Given a successful conversion,
When the API responds,
Then the frontend displays the converted value with source and target units.

### AC-07: Frontend Displays Error

Given a validation error from the API,
When the response is received,
Then the frontend displays the error message to the user.

## Business Rules

- Conversions are stateless. Nothing is persisted.
- Unit compatibility groups: length (metres, feet, kilometres, miles), volume (litres, gallons).
- Conversion within a group is valid. Cross-group is invalid.
- No user accounts or authentication.

## Out of Scope

- Persistence or conversion history.
- User accounts or authentication.
- Additional unit types beyond the six listed.
- Batch conversions.