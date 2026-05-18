## ADDED Requirements

### Requirement: Service readiness shall gate dependent startup
Containers that depend on infrastructure services SHALL not be considered ready until their required dependencies report healthy status.

#### Scenario: Backend waits for PostgreSQL
- **WHEN** the local stack starts and PostgreSQL is not yet healthy
- **THEN** the backend container waits on database readiness before normal startup completion

#### Scenario: Gateway waits for upstream services
- **WHEN** the stack starts and the backend or portal is unavailable
- **THEN** the gateway is not presented as fully ready until its upstream routes are available according to the compose design

### Requirement: Critical services shall define health checks and persistence
PostgreSQL, RabbitMQ, and MinIO SHALL define health checks and named persistent storage so the environment can be recreated without silent data loss or ambiguous readiness.

#### Scenario: Recreated stack preserves state
- **WHEN** the stack is stopped and restarted without volume removal
- **THEN** persisted service data remains available through the named volumes

#### Scenario: Health check exposes unhealthy service
- **WHEN** a critical container cannot satisfy its readiness probe
- **THEN** the compose environment exposes the unhealthy state for troubleshooting