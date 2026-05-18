## ADDED Requirements

### Requirement: Approver sees their pending tasks
The system SHALL display a paginated list of approval steps in `InProgress` status that match the authenticated user's role, loaded from `GET /api/workflow-instances/pending`.

#### Scenario: Inbox populated for matching role
- **WHEN** an `Approver` (or `Finance`/`Legal`) navigates to the Inbox page
- **THEN** the system fetches pending steps and renders each as a row with: request title, workflow name, requester name, step name, and creation date

#### Scenario: Empty inbox message
- **WHEN** there are no pending steps for the user's role
- **THEN** the system displays an empty-state message: "No pending approvals"

#### Scenario: Navigation to request detail
- **WHEN** the user clicks a row in the inbox
- **THEN** the system navigates to the Request Detail page for that workflow instance

### Requirement: Inbox refreshes on demand
The system SHALL provide a refresh button that re-fetches the pending list without a full page reload.

#### Scenario: Refresh updates the list
- **WHEN** the user clicks "Refresh"
- **THEN** the system calls `GET /api/workflow-instances/pending` again and re-renders the list
