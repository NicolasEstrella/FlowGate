## ADDED Requirements

### Requirement: State Machine shall guard valid workflow instance transitions
The State Machine SHALL only allow transitions defined in the transition table and throw on invalid attempts.

#### Scenario: Draft transitions to Submitted
- **GIVEN** a `WorkflowInstance` with `CurrentStatus = Draft`
- **WHEN** `TransitionTo(Submitted)` is called
- **THEN** the instance status becomes `Submitted` and no exception is thrown

#### Scenario: Submitted transitions to InApproval
- **GIVEN** a `WorkflowInstance` with `CurrentStatus = Submitted`
- **WHEN** `TransitionTo(InApproval)` is called
- **THEN** the instance status becomes `InApproval`

#### Scenario: InApproval transitions to Approved
- **GIVEN** a `WorkflowInstance` with `CurrentStatus = InApproval`
- **WHEN** `TransitionTo(Approved)` is called
- **THEN** the instance status becomes `Approved`

#### Scenario: InApproval transitions to AdjustmentsRequested
- **GIVEN** a `WorkflowInstance` with `CurrentStatus = InApproval`
- **WHEN** `TransitionTo(AdjustmentsRequested)` is called
- **THEN** the instance status becomes `AdjustmentsRequested`

#### Scenario: AdjustmentsRequested transitions back to Submitted on resubmit
- **GIVEN** a `WorkflowInstance` with `CurrentStatus = AdjustmentsRequested`
- **WHEN** `TransitionTo(Submitted)` is called
- **THEN** the instance status becomes `Submitted`

#### Scenario: Illegal transition throws InvalidOperationException
- **GIVEN** a `WorkflowInstance` with `CurrentStatus = Draft`
- **WHEN** `TransitionTo(Approved)` is called
- **THEN** an `InvalidOperationException` is thrown
