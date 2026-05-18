## 1. Domain enrichment and migration

- [x] 1.1 Add `StepExecutionType` enum (`Sequential`, `Parallel`, `Conditional`) and enrich `ApprovalStep` with `ExecutionType`, `GroupId`, and `StepIndex` fields.
- [x] 1.2 Add `FormDataJson` (nullable string / JSONB) to `WorkflowInstance`.
- [x] 1.3 Update `ApprovalStepConfiguration` and `WorkflowInstanceConfiguration` to map the new fields.
- [x] 1.4 Generate the EF Core migration `AddWorkflowEngineFields`.

## 2. Rule Engine

- [x] 2.1 Define `IRuleEngine`, `RuleContext` record, and `ApprovalStepDefinition` record in the application layer.
- [x] 2.2 Write `RuleEngineTests` (unit) covering: simple ≤5k, >5k, >10k, contract-parallel scenarios — tests must fail first.
- [x] 2.3 Implement `RuleEngineService` with Strategy pattern so tests pass.

## 3. State Machine

- [x] 3.1 Define `IWorkflowStateMachine` interface in the application layer.
- [x] 3.2 Write `WorkflowStateMachineTests` (unit) covering: valid transitions and illegal transition guard — tests must fail first.
- [x] 3.3 Implement `WorkflowStateMachineService` so tests pass.

## 4. Workflow Engine orchestrator

- [x] 4.1 Define `IWorkflowEngine` interface with `StartAsync`, `SubmitAsync`, `ProcessDecisionAsync`, and `CancelAsync`.
- [x] 4.2 Write `WorkflowEngineTests` (unit) covering: create draft, submit, approve-single, reject-single, parallel wait, parallel advance — tests must fail first.
- [x] 4.3 Implement `WorkflowEngineService` so tests pass.

## 5. DTOs and API endpoints

- [x] 5.1 Add request/response DTOs: `CreateWorkflowInstanceRequest`, `SubmitDecisionRequest`, `WorkflowInstanceDetailDto`, `ApprovalStepDetailDto`, `AuditLogEntryDto`, `PendingApprovalDto`.
- [x] 5.2 Extend `WorkflowInstancesController` with new endpoints: `POST /`, `POST /{id}/submit`, `GET /{id}`, `POST /{id}/decide`, `POST /{id}/cancel`, `GET /pending`.

## 6. Dependency injection and validation

- [x] 6.1 Register `IRuleEngine`, `IWorkflowStateMachine`, and `IWorkflowEngine` in `Infrastructure.DependencyInjection`.
- [x] 6.2 Build the project (`dotnet build`) and confirm zero errors.
- [x] 6.3 Run all tests (`dotnet test`) and confirm all pass.
