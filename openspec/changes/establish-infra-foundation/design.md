## Context

`FlowGate.Infra` already provides a minimal Docker Compose setup with PostgreSQL, the backend, and the frontend, but it does not yet reflect the documented platform architecture. The current stack has no RabbitMQ, Redis, MinIO, or gateway layer, and the README only documents the reduced footprint. This change needs to establish a reliable local platform baseline that downstream backend and portal work can assume.

## Goals / Non-Goals

**Goals:**
- Expand the local stack to cover the platform services required by the documented architecture.
- Make startup sequencing, health checks, volumes, and environment variables explicit and repeatable.
- Publish a contributor-facing contract for bootstrap, reset, credentials, and troubleshooting.

**Non-Goals:**
- Production hardening, TLS, secret management, and multi-environment deployment concerns.
- Advanced observability and delivery automation.
- Application-level workflow behavior beyond what is necessary to boot the local environment.

## Decisions

1. Use Docker Compose as the single orchestration entry point for local development.
   Rationale: the repository already uses Compose and the acceptance criteria call for one-command environment startup.
   Alternative considered: split service startup into multiple compose files. Rejected because it increases bootstrap complexity before the platform baseline is stable.

2. Model every critical infrastructure service with explicit health checks and gate dependent application containers on healthy dependencies.
   Rationale: this reduces race conditions during local bootstrap and gives contributors a deterministic readiness signal.
   Alternative considered: rely only on container start order. Rejected because `depends_on` without health semantics is not enough for database and broker readiness.

3. Route browser traffic through NGINX while keeping service ports directly available for troubleshooting.
   Rationale: the gateway mirrors the documented architecture and lets the portal and API be exercised behind a single entry point without removing direct access for debugging.
   Alternative considered: expose portal and API independently only. Rejected because later phases will need routing behavior that is easier to validate early.

4. Keep development configuration in environment files with documented defaults rather than hardcoding values across multiple services.
   Rationale: this creates one source of truth for local credentials and addresses drift between backend, portal, and infrastructure configuration.

## Risks / Trade-offs

- [More containers increase local resource usage] -> Mitigation: keep images lightweight, scope defaults to development, and document reset procedures.
- [Compose complexity can make troubleshooting harder] -> Mitigation: standardize health checks, naming, and README troubleshooting sections.
- [Gateway routing can mask service-level issues] -> Mitigation: preserve direct service ports for backend and portal during development.

## Migration Plan

1. Extend `docker-compose.yml` with the missing platform services, named volumes, shared network definitions, and health checks.
2. Add a documented environment-variable contract through `.env.example` and align service configuration to those variables.
3. Introduce or update NGINX configuration to route portal and API traffic through a predictable local entry point.
4. Refresh the infrastructure README with bootstrap, reset, and troubleshooting instructions.
5. Validate the stack with a clean `docker compose up` cycle and a destructive reset path.

## Open Questions

- Which host ports should be reserved for RabbitMQ management and MinIO console access in local development?
- Should the gateway be the primary documented entry point, or should direct frontend/backend URLs remain equally prominent in the README?