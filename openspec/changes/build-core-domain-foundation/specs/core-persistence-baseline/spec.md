## ADDED Requirements

### Requirement: Backend foundation shall persist to PostgreSQL through EF Core
The backend SHALL use EF Core with PostgreSQL for its primary persistence layer and SHALL provide a database context, entity mappings, and an initial migration for a clean environment.

#### Scenario: Clean environment applies initial schema
- **WHEN** the application is pointed to an empty PostgreSQL database
- **THEN** the initial migration creates the schema required by the foundational domain model

#### Scenario: Domain entities map consistently
- **WHEN** foundational entities are persisted or queried
- **THEN** EF Core mappings preserve the relationships and constraints defined by the backend foundation

### Requirement: Authorization baseline data shall be seedable
The backend SHALL provide a repeatable mechanism to create the baseline application roles required for local development and early integration.

#### Scenario: Baseline roles are available after bootstrap
- **WHEN** a new local environment is initialized
- **THEN** Admin, Approver, Finance, Legal, and Standard User role data can be created without manual database edits