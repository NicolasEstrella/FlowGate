## 1. Backend structure and domain baseline

- [x] 1.1 Remove template-specific backend artifacts and establish the internal API, application, domain, and infrastructure organization.
- [x] 1.2 Define the foundational domain entities and supporting types for workflows, workflow instances, approval steps, audit logs, and users.
- [x] 1.3 Register the new application and infrastructure services in the backend startup pipeline.

## 2. Persistence and authorization

- [x] 2.1 Add the EF Core PostgreSQL persistence baseline with `DbContext`, entity mappings, and database configuration.
- [x] 2.2 Create the initial migration and bootstrap path for a clean PostgreSQL environment.
- [x] 2.3 Implement baseline RBAC roles, seed logic, and authorization policies for Admin, Approver, Finance, Legal, and Standard User.

## 3. API foundation and validation

- [x] 3.1 Add middleware for structured error handling, request correlation, and logging.
- [x] 3.2 Implement the foundational health, users, workflows, and workflow instances endpoints.
- [x] 3.3 Validate backend startup, migration application, and protected endpoint behavior against the local infrastructure stack.