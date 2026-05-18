## ADDED Requirements

### Requirement: Portal shall provide an authenticated application shell
The portal SHALL provide a responsive authenticated shell with a header, side navigation, and role-aware module entry points.

#### Scenario: Authenticated user sees shell layout
- **WHEN** an authenticated user opens the portal
- **THEN** the application renders the shared shell with the primary navigation and content outlet

#### Scenario: Navigation reflects user role
- **WHEN** a user with a specific application role accesses the portal
- **THEN** the navigation presents the modules permitted for that role

### Requirement: Portal shall define stable route structure for future modules
The portal SHALL define guarded route groups that allow later request, approval, audit, and administrative pages to be added without replacing the shell architecture.

#### Scenario: Protected route resolves inside shell
- **WHEN** a user navigates to a protected application route
- **THEN** the route renders within the authenticated shell rather than a standalone page frame