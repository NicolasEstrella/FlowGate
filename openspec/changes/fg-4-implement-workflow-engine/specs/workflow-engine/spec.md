## ADDED Requirements

### Requirement: Workflow Engine shall orchestrate the full approval lifecycle
The Workflow Engine SHALL coordinate rule evaluation, step creation, decision processing, parallel step resolution, and audit logging.

#### Scenario: StartAsync creates a Draft instance
- **GIVEN** a valid `CreateRequestCommand` with a workflow key, title, and form data
- **WHEN** `StartAsync` is called
- **THEN** a `WorkflowInstance` in `Draft` status is persisted and its ID is returned

#### Scenario: SubmitAsync applies rule engine and transitions to InApproval
- **GIVEN** a `WorkflowInstance` in `Draft` status
- **WHEN** `SubmitAsync` is called
- **THEN** approval steps are created per rule evaluation, the instance transitions to `InApproval`, and an audit log entry is recorded

#### Scenario: ProcessDecisionAsync with Approve on last sequential step transitions to Approved
- **GIVEN** a `WorkflowInstance` in `InApproval` with one pending sequential step
- **WHEN** `ProcessDecisionAsync` is called with decision `Approve` on that step
- **THEN** the step is marked `Approved`, the instance transitions to `Approved`, and an audit log entry is recorded

#### Scenario: ProcessDecisionAsync with Reject transitions instance to Rejected
- **GIVEN** a `WorkflowInstance` in `InApproval` with one pending step
- **WHEN** `ProcessDecisionAsync` is called with decision `Reject`
- **THEN** the step is marked `Rejected`, the instance transitions to `Rejected`, and an audit log entry is recorded

#### Scenario: Parallel steps wait until all group members decide before advancing
- **GIVEN** a `WorkflowInstance` in `InApproval` with two parallel steps sharing the same `GroupId`
- **WHEN** the first step is approved but the second is still pending
- **THEN** the instance remains in `InApproval`

#### Scenario: Parallel group advances when all members are approved
- **GIVEN** a `WorkflowInstance` in `InApproval` with two parallel steps sharing the same `GroupId`, first already approved
- **WHEN** the second step is approved
- **THEN** the instance either advances to the next sequential step or transitions to `Approved` if no further steps exist

#### Scenario: CancelAsync transitions Draft or Submitted instance to Cancelled
- **GIVEN** a `WorkflowInstance` in `Draft` or `Submitted` status
- **WHEN** `CancelAsync` is called
- **THEN** the instance transitions to `Cancelled` and an audit log entry is recorded
