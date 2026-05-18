## Context

`FlowGate.Core` is still the default ASP.NET Core template, with `Program.cs` only wiring controllers and Swagger and the sample structure still oriented around the WeatherForecast example. The project needs a backend foundation that matches the FlowGate domain model, works against PostgreSQL provisioned by infrastructure, and establishes the cross-cutting boundaries required by later workflow work.

## Goals / Non-Goals

**Goals:**
- Replace the template structure with a domain-oriented backend organization.
- Introduce the baseline persistence, RBAC, middleware, and API surface needed by later changes.
- Ensure the service can bootstrap cleanly against the local infrastructure stack and evolve without structural rewrites.

**Non-Goals:**
- Implementing the workflow engine's full transition logic.
- Delivering asynchronous automation, notifications, or document generation.
- Finalizing enterprise identity-provider integration beyond a development-ready RBAC baseline.

## Decisions

1. Organize the backend into clear domain, application, infrastructure, and API boundaries inside the existing repository.
   Rationale: the change is foundational and must separate business concepts from transport and persistence concerns before more behavior is added.
   Alternative considered: keep a flatter folder structure and refactor later. Rejected because later workflow work would be forced to move core abstractions under load.

2. Use EF Core with PostgreSQL as the authoritative persistence baseline from the first migration.
   Rationale: the infrastructure proposal already centers on PostgreSQL, and the domain model needs durable relational storage for workflows, instances, approvals, audit logs, and users.
   Alternative considered: in-memory or SQLite bootstrap. Rejected because it would defer relational modeling decisions and break alignment with the actual runtime architecture.

3. Introduce RBAC as application roles with seed-ready data and authorization policies.
   Rationale: Admin, Approver, Finance, Legal, and Standard User access boundaries are already part of the product definition and influence the API shape.
   Alternative considered: postpone authorization until portal implementation. Rejected because unsecured foundational endpoints would need incompatible retrofits later.

4. Standardize middleware for error handling, correlation, and structured logging before feature endpoints expand.
   Rationale: cross-cutting behavior is easier to establish now than to retrofit after multiple modules depend on inconsistent patterns.

## Risks / Trade-offs

- [Introducing architecture layers increases initial implementation effort] -> Mitigation: keep the boundaries lightweight and focused on domain seams that later changes will reuse.
- [Early domain modeling may miss later workflow nuances] -> Mitigation: model extension points explicitly and avoid overcommitting to final engine rules in this phase.
- [Authentication scope may expand later] -> Mitigation: treat the RBAC setup as a foundation with replaceable identity plumbing and stable authorization policies.

## Migration Plan

1. Remove template-specific artifacts and establish the internal application structure.
2. Add domain entities, DbContext, persistence mappings, and the initial migration targeting PostgreSQL.
3. Configure middleware, logging, validation/error handling, and role-based authorization.
4. Add foundational endpoints for health, users, workflows, and workflow instances.
5. Seed baseline role data and validate startup against the local Postgres environment.

## Open Questions

- Will user identity remain local in the first implementation, or should the model reserve explicit external identity fields now?
- Should the first foundation endpoints expose CRUD-style contracts or a narrower administrative bootstrap surface?