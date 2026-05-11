## Why

`FlowGate.Portal` is currently only a starter Angular application and does not represent the product's core user experience. Establishing the portal foundation now creates the navigation, session, and UI building blocks required for the operational screens planned in later phases.

## What Changes

- Replace the current scaffold with an authenticated application shell, route structure, and role-aware navigation baseline.
- Introduce HTTP integration plumbing, shared UI primitives, and consistent loading, error, and empty states.
- Deliver initial dashboard experiences for core roles so the portal can evolve without front-end reorganization in later phases.

## Capabilities

### New Capabilities
- `portal-application-shell`: Provide the authenticated shell, responsive layout, and role-based navigation model for the FlowGate portal.
- `portal-session-and-api-baseline`: Establish route guards, session handling, environment-aware API access, interceptors, and shared error handling.
- `portal-shared-ui-foundation`: Define reusable design tokens, shared components, and starter dashboards for key user roles.

### Modified Capabilities
None.

## Impact

Affected areas include the Angular routing and app shell, authentication and HTTP service layers, shared UI components and styling tokens, environment configuration, and dashboard entry points that will anchor later portal features.