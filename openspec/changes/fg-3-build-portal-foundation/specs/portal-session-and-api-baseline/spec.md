## ADDED Requirements

### Requirement: Portal shall enforce session-aware routing
The portal SHALL use guards and session services to prevent unauthorized access to protected routes.

#### Scenario: Anonymous user is blocked from protected area
- **WHEN** a user without an active session attempts to access a protected route
- **THEN** the portal redirects or blocks access according to the configured foundation behavior

#### Scenario: Authorized user reaches role-allowed route
- **WHEN** a user with the required role navigates to a protected route
- **THEN** the route activates successfully

### Requirement: Portal shall centralize backend communication
The portal SHALL centralize backend HTTP calls through environment-aware configuration, interceptors, and a standard error-handling strategy.

#### Scenario: API base URL switches by environment
- **WHEN** the portal runs in a configured environment
- **THEN** HTTP services resolve backend requests through the environment-specific API base settings

#### Scenario: API error is normalized for the UI
- **WHEN** a backend request fails
- **THEN** the portal processes the failure through the shared interception and error-handling flow instead of page-specific ad hoc logic