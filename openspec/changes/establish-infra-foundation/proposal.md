## Why

The FlowGate workspace still lacks a local infrastructure baseline that reflects the documented architecture, which blocks realistic backend and portal development. Establishing that baseline now removes environmental drift and gives the next phases stable service dependencies.

## What Changes

- Expand the local infrastructure stack in `FlowGate.Infra` to include PostgreSQL, RabbitMQ, Redis, MinIO, backend, portal, and an NGINX gateway.
- Standardize local development configuration through shared environment variables, named volumes, health checks, and predictable port exposure.
- Document bootstrap, reset, and troubleshooting flows so the full environment can be recreated without hidden steps.

## Capabilities

### New Capabilities
- `local-platform-stack`: Provide a single local compose stack for the core FlowGate services and application containers.
- `service-bootstrap-readiness`: Ensure container startup order, health checks, and dependency readiness are explicit and repeatable.
- `developer-infra-documentation`: Define the operational documentation and environment-variable contract for local development.

### Modified Capabilities
None.

## Impact

Affected areas include `FlowGate.Infra/docker-compose.yml`, local environment configuration files, container networking and volumes, NGINX gateway routing, and the infrastructure README used by all contributors.