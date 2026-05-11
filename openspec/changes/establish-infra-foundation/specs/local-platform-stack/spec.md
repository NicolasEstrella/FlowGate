## ADDED Requirements

### Requirement: Local development stack shall provide the core platform services
The local development environment SHALL be startable with a single Compose command and SHALL include PostgreSQL, RabbitMQ, Redis, MinIO, the FlowGate backend, the FlowGate portal, and an NGINX gateway.

#### Scenario: Full platform bootstrap
- **WHEN** a contributor starts the local stack from `FlowGate.Infra`
- **THEN** all required platform and application containers are created within the same compose project

#### Scenario: Core services exposed predictably
- **WHEN** the stack is running
- **THEN** the gateway, portal, backend, database, and auxiliary service endpoints are reachable through documented local ports or URLs

### Requirement: Local routing shall include a gateway entry point
The infrastructure SHALL provide an NGINX gateway that routes browser requests to the portal and API according to the documented local topology.

#### Scenario: Portal traffic routed through gateway
- **WHEN** a user opens the documented gateway URL
- **THEN** the request is served by the portal without requiring the user to know container-internal addresses

#### Scenario: API traffic routed through gateway
- **WHEN** the portal or an operator sends a request to the documented API route on the gateway
- **THEN** the request is forwarded to the backend service successfully