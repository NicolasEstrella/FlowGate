## ADDED Requirements

### Requirement: User can open a new request form
The system SHALL provide a form page where an authenticated user with the `StandardUser` role can fill in a workflow key, a title, and arbitrary key-value form fields, then submit the request to the backend.

#### Scenario: Successful request creation
- **WHEN** a `StandardUser` fills in the required fields and clicks "Create"
- **THEN** the system POSTs to `POST /api/workflow-instances` and navigates to the new instance detail page

#### Scenario: Empty required fields blocked
- **WHEN** the user submits the form with a missing `title` or `workflowKey`
- **THEN** the form displays inline validation errors and does NOT call the API

#### Scenario: Backend error shown as notification
- **WHEN** the API returns a 4xx or 5xx response
- **THEN** the system displays an error toast with the message from the response body

### Requirement: User can submit a draft request
The system SHALL allow the request owner to trigger submission of a draft instance via a "Submit for Approval" button available on the request detail page when the instance is in `Draft` or `AdjustmentsRequested` status.

#### Scenario: Submit moves instance to InApproval
- **WHEN** the owner clicks "Submit for Approval" on a Draft instance
- **THEN** the system POSTs to `POST /api/workflow-instances/{id}/submit` and refreshes the detail page with the updated status
