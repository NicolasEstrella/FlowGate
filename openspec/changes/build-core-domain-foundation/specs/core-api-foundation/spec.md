## ADDED Requirements

### Requirement: Backend foundation shall expose a consistent foundational API surface
The backend SHALL expose health, users, workflows, and workflow instance endpoints aligned with the foundational FlowGate domain.

#### Scenario: Health endpoint reports service availability
- **WHEN** an operator or dependent service calls the backend health endpoint
- **THEN** the API returns a successful response when the service is running and able to report its readiness state

#### Scenario: Foundational resources are addressable
- **WHEN** authorized clients interact with the foundation API
- **THEN** users, workflows, and workflow instances are exposed through stable endpoint groups prepared for later expansion

### Requirement: Backend foundation shall standardize request pipeline behavior
The application SHALL provide middleware-based error handling, request correlation, structured logging, and authorization enforcement for the foundational API surface.

#### Scenario: Unhandled exception returns standard error contract
- **WHEN** an unhandled server error occurs during request processing
- **THEN** the API responds with the configured error format instead of leaking raw framework exceptions

#### Scenario: Protected endpoint enforces role policy
- **WHEN** a caller without the required role accesses a protected foundational endpoint
- **THEN** the request is denied according to the configured authorization policy