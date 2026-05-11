## Why

`FlowGate.Core` is still a template API and does not provide the domain, persistence, or cross-cutting foundations required by the product architecture. Building that backend foundation now is necessary so later workflow features can land on stable models, storage, and security boundaries instead of forcing a structural rewrite.

## What Changes

- Replace the WeatherForecast template with a domain-oriented backend structure aligned to FlowGate concepts.
- Introduce the initial domain model, persistence layer, RBAC foundation, and cross-cutting middleware needed by the API.
- Expose foundational endpoints and database bootstrap assets so the backend can integrate with the local infrastructure and upcoming workflow engine work.

## Capabilities

### New Capabilities
- `core-domain-model`: Define the foundational entities and aggregates for workflows, workflow instances, approvals, audit logs, and users.
- `core-persistence-baseline`: Establish EF Core with PostgreSQL, mappings, migrations, and seed-ready access control data.
- `core-api-foundation`: Provide the API structure, error handling, logging, health checks, and initial resource endpoints required for product growth.

### Modified Capabilities
None.

## Impact

Affected areas include the full `FlowGate.Core` application structure, EF Core configuration and migrations, authentication and authorization setup, middleware pipeline, foundational API endpoints, and integration with the local Postgres environment.