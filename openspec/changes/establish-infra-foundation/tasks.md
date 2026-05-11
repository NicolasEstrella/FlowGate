## 1. Compose foundation

- [x] 1.1 Expand `FlowGate.Infra/docker-compose.yml` to include RabbitMQ, Redis, MinIO, backend, portal, PostgreSQL, and NGINX in the same local stack.
- [x] 1.2 Add named volumes, shared networking, and health checks for the critical infrastructure services.
- [x] 1.3 Configure service dependency conditions so application containers wait for required infrastructure readiness.

## 2. Configuration and routing

- [x] 2.1 Add a documented `.env.example` contract and align compose services to environment-driven local settings.
- [x] 2.2 Introduce or update NGINX routing so the gateway fronts the portal and API with predictable local URLs.
- [x] 2.3 Document the exposed ports, default credentials, and service endpoints used during development.

## 3. Validation and operations

- [x] 3.1 Update the infrastructure README with bootstrap, reset, logs, and troubleshooting instructions for the full stack.
- [x] 3.2 Validate a clean `docker compose up` flow and confirm backend dependency connectivity against the provisioned services.
- [x] 3.3 Validate the destructive reset path and verify the documented recovery steps match observed behavior.