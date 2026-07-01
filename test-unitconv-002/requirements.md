# Requirements: Unit Conversion Service (Full Stack)

**Jira Ticket:** DF-002


## Functional Requirements

### REQ-01: Convert Measurement

- The system must convert a numeric value from a source unit to a target unit.
- Supported conversions (bidirectional):
  - metres <-> feet
  - kilometres <-> miles
  - litres <-> gallons
- Each conversion result must be persisted in the database.
- The result must be returned as a numeric value.

### REQ-02: List Supported Units

- The system must expose the list of supported units with their measurement system (metric/imperial).
- Units are stored in the database and returned via GET /api/units.
- Each unit has: id, name, system.

### REQ-03: Validate Input

- Non-numeric values must be rejected.
- Incompatible unit pairs (e.g., metres to gallons) must be rejected.
- Missing required fields must be rejected.
- All validation errors must return HTTP 400 with a ValidationError body containing: id (UUID), message (string), and optionally field (string or null — present when the error relates to a specific input field).

### REQ-04: Conversion Form (Frontend)

- The frontend must display a form with: a numeric input for the value, a dropdown for source unit, a dropdown for target unit, and a Convert button.
- Unit dropdowns must be populated from the backend GET /api/units endpoint.
- On submit, the frontend calls POST /api/convert and displays the result below the form.
- Validation errors from the backend must be displayed as user-friendly messages near the relevant field.

### REQ-05: Conversion History (Frontend)

- The frontend must display a list of recent conversions performed in the current session.
- Each entry shows: source value + unit, target value + unit.
- History is stored in frontend state only (not persisted across page reloads).

## Acceptance Criteria

### AC-01: Successful Conversion

Given a valid value, source unit, and target unit,
When the user submits a conversion via the form,
Then the result is displayed below the form and added to the history list.

### AC-02: Reverse Conversion

Given a conversion from metres to feet returns X,
When the user converts X feet to metres,
Then the result matches the original value (within rounding precision).

### AC-03: Incompatible Units Rejected

Given source unit "metres" and target unit "gallons",
When the user submits a conversion,
Then the form displays an error message indicating incompatible units.

### AC-04: Non-Numeric Value Rejected

Given a non-numeric value (e.g., "abc"),
When the user submits a conversion,
Then the form displays an error message indicating invalid input.

### AC-05: Unit Dropdowns Populated

Given the backend has pre-loaded unit definitions,
When the frontend loads,
Then both source and target unit dropdowns are populated with all supported units.

### AC-06: List Units (API)

Given the system has pre-loaded unit definitions,
When GET /api/units is called,
Then the system returns all supported units with id, name, and system fields.

## Business Rules

- Unit compatibility groups: length (metres, feet, kilometres, miles), volume (litres, gallons).
- Conversion within a group is valid. Cross-group is invalid.
- Supported units are pre-loaded at application startup (seed data or migration).
- No user accounts or authentication.
- Frontend conversion history is session-only (cleared on page reload).

## Out of Scope

- User accounts or authentication.
- Additional unit types beyond the six listed.
- Batch conversions.
- Server-side conversion history.
