## ADDED Requirements

### Requirement: Approver can approve a step
The system SHALL display an "Approve" button on the request detail page when the current user's role matches the active step's `requiredRole` and the step is `InProgress`. Clicking it POSTs to `POST /api/workflow-instances/{id}/decide` with `{ stepId, decision: "Approve" }`.

#### Scenario: Approve action completes step
- **WHEN** an approver clicks "Approve" and confirms in the modal
- **THEN** the system posts the decision and refreshes the page with updated step and instance status

#### Scenario: Optional comment included
- **WHEN** the approver fills in a comment before approving
- **THEN** the comment is sent in the request body and displayed in the audit log after refresh

### Requirement: Approver can reject a request
The system SHALL display a "Reject" button alongside the approve action. Clicking it opens a confirmation modal requiring a comment (mandatory on rejection).

#### Scenario: Rejection requires comment
- **WHEN** the approver clicks "Reject" without entering a comment
- **THEN** the modal form shows a validation error and does NOT submit

#### Scenario: Rejection closes the workflow
- **WHEN** a valid rejection is submitted
- **THEN** the instance transitions to `Rejected` status and action buttons are no longer shown

### Requirement: Approver can request adjustment
The system SHALL display a "Request Adjustment" button. Clicking it opens a modal with a mandatory comment field. Submitting posts `decision: "RequestAdjustment"` to the decide endpoint.

#### Scenario: Adjustment returns request to requester
- **WHEN** the approver submits a request for adjustment
- **THEN** the instance transitions to `AdjustmentsRequested` and the "Submit for Approval" button becomes available to the requester again

### Requirement: Owner can cancel a request
The system SHALL display a "Cancel" button on the detail page when the current user is the requester AND the instance is in `Draft`, `Submitted`, or `AdjustmentsRequested` status. Clicking it POSTs to `POST /api/workflow-instances/{id}/cancel`.

#### Scenario: Cancel terminates the workflow
- **WHEN** the requester confirms cancellation
- **THEN** the instance transitions to `Cancelled` and all action buttons are removed
