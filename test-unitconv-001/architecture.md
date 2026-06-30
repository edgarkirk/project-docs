# unitconv - Solution Architecture

## Overview
Stateless measurement conversion web service exposing a Spring Boot backend and React frontend supporting metres<->feet, kilometres<->miles, and litres<->gallons with clear validation and measurable NFRs.

The service is a small, stateless HTTP API with simple validation and low computational needs. Java + Spring Boot enables robust development, well-structured layered architecture, and clear request/response validation (Bean Validation). React is chosen for a single-page web UI with composable form and result components.

## Technology Stack
| Layer | Choice |
|-------|--------|
| Backend | java / spring-boot 3.4.x |
| Frontend | typescript / react |
| Datastore | PostgreSQL 16 |

## Repositories
| id | Name | Role |
|----|------|------|
| be | unitconv-backend | backend |
| fe | unitconv-frontend | frontend |

## Container View (C4)
\`\`\`mermaid
flowchart TB
  user["User"]
  subgraph system["unitconv"]
    fe["Web App (react)<br/>[fe]"]
    be["Backend API (spring-boot)<br/>[be]"]
    db[("PostgreSQL 16")]
  end
  user -->|uses| fe
  fe -->|REST / OpenAPI 'api'| be
  be -->|reads/writes| db
\`\`\`

## Data Model
\`\`\`mermaid
erDiagram
  ConversionRequest {
    uuid id PK
    number value
    string sourceUnit
    string targetUnit
  }
  ConversionResult {
    uuid id PK
    number inputValue
    string sourceUnit
    string targetUnit
    number result
  }
  ValidationError {
    uuid id PK
    string message
    string field
  }
  Unit {
    uuid id PK
    string name
    string system
  }
\`\`\`

## API Contract
REST contract FE->BE defined in \`openapi.json\` (OpenAPI 3.0.3). The frontend depends on this contract.

## Components
| Component | Repo | Responsibility |
|-----------|------|----------------|
| REST Controller | be | Expose REST endpoints (/api/convert, /api/units), request/response validation via Bean Validation, error handling via @RestControllerAdvice. |
| Conversion Service | be | Core conversion logic (metres<->feet, kilometres<->miles, litres<->gallons), unit compatibility checks, numeric precision handling. |
| Validation Module | be | Input validation (numeric parsing, compatible unit pairs), constructs ValidationError responses for invalid requests. |
| Unit Definitions | be | Authoritative list of supported units and mappings used by the Conversion Service and exposed via /api/units. |
| React App Shell | fe | Single-page application host, routing, global state for current conversion session. |
| Converter Form | fe | User input form for numeric value, source and target unit selection, client-side basic validation and submit to /api/convert. |
| Result & Validation Display | fe | Render conversion result or validation errors returned by the API, ensure accessible and mobile-friendly presentation. |

## Non-Functional Requirements
- **NFR-1 (performance):** p95 latency for POST /api/convert is under 200ms when subjected to 100 concurrent clients issuing requests with valid inputs.
- **NFR-2 (security):** All invalid inputs (non-numeric value or incompatible unit pairs) return HTTP 400 with a JSON body containing a 'message' field describing the error.
- **NFR-3 (reliability):** Error rate for valid conversion requests remains below 0.1% over a 24-hour window under normal load (up to 10k requests/day).
- **NFR-4 (interaction):** Frontend achieves a Lighthouse accessibility score of at least 90 for the main conversion flow (form, controls, and result display).

## Architecturally Significant Requirements
- **ASR-1 - Where conversions are executed: client vs server.** Drivers: Need for authoritative, consistent validation and result formatting across all clients; potential future extension to add more unit types or business rules; requirement to return clear validation errors from a single place. Decision: Perform all conversions and compatibility validation on the server-side (stateless API). The frontend handles input convenience and basic pre-checks, but final validation and calculation occur in the backend Conversion Service.
- **ASR-2 - Technology choice for backend and frontend.** Drivers: Small, latency-sensitive stateless API with straightforward validation and simple JSON DTOs; goal for rapid implementation and clear request/response validation. Decision: Use Java Spring Boot for the backend to leverage Bean Validation, layered architecture, and a mature ecosystem. Use React for the frontend to provide a responsive SPA and reusable form/result components.
