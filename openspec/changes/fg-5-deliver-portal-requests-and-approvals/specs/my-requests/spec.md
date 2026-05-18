## ADDED Requirements

### Requirement: User sees their own requests
The system SHALL display a list page showing all workflow instances created by the authenticated user, loaded from `GET /api/workflow-instances` filtered client-side by `requesterId` matching the current session user ID.

#### Scenario: My requests list populates
- **WHEN** an authenticated user navigates to `/workspace/requests`
- **THEN** the system renders a table with columns: title, workflow name, status badge, creation date, and a link to the detail page

#### Scenario: Empty state for no requests
- **WHEN** the user has no workflow instances
- **THEN** the system displays "No requests yet" with a "Create Request" call-to-action button

### Requirement: User can filter by status
The system SHALL provide a status filter (dropdown or tab strip) on the My Requests page that filters the displayed list to instances matching the selected status.

#### Scenario: Filter applies without API call
- **WHEN** the user selects a status filter
- **THEN** the list re-renders with only matching instances (client-side filter, no new HTTP request)

#### Scenario: All filter shows full list
- **WHEN** the user selects "All" in the filter
- **THEN** all instances are shown regardless of status

### Requirement: Navigation to request detail from list
The system SHALL allow the user to click any row in the My Requests list to navigate to that instance's detail page.

#### Scenario: Row click navigates
- **WHEN** the user clicks a row
- **THEN** the router navigates to `/workspace/requests/{id}`
