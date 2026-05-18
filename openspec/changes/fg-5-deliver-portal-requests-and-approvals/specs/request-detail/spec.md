## ADDED Requirements

### Requirement: Request detail shows full context
The system SHALL render a detail page for a workflow instance loaded from `GET /api/workflow-instances/{id}`, displaying: title, current status badge, requester name, workflow name, form data summary, approval step timeline, and audit log.

#### Scenario: Detail page loads successfully
- **WHEN** any authenticated user navigates to `/workspace/requests/{id}`
- **THEN** the system fetches the instance and renders all sections: header, step timeline, and audit log

#### Scenario: Not found shows error state
- **WHEN** the API returns 404
- **THEN** the system displays a "Request not found" message and a back link

### Requirement: Step timeline shows status and roles
The system SHALL render each approval step in order, showing: step name, required role, status badge (`Pending`, `InProgress`, `Approved`, `Rejected`, `AdjustmentsRequested`), assigned user (if any), decision comment (if any), and decision timestamp.

#### Scenario: Active step highlighted
- **WHEN** a step has status `InProgress`
- **THEN** the step card is visually highlighted as the current active step

#### Scenario: Parallel steps shown at same level
- **WHEN** two or more steps share the same `groupId`
- **THEN** they are rendered side-by-side (or stacked with a parallel indicator) to signal simultaneous execution

### Requirement: SLA indicator visible
The system SHALL show an SLA indicator on the detail page that displays the instance creation date and elapsed time in a human-readable format (e.g., "Opened 2 days ago").

#### Scenario: Elapsed time displayed
- **WHEN** the detail page renders
- **THEN** the header shows the time elapsed since `createdAt` in relative format
