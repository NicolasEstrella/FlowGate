## Why

`FlowGate.Core` has foundational domain entities and persistence, but no execution logic. Without a workflow engine, the platform cannot route approval requests, enforce business rules, track state transitions, or produce audit trails — making it impossible to demonstrate the core product promise. Implementing the engine now unblocks the frontend approval UI and validates the domain model under real behavior.

## What Changes

- Enrich `WorkflowInstance` and `ApprovalStep` entities with engine-required fields (FormData, ExecutionType, GroupId, StepIndex).
- Introduce a Rule Engine that evaluates process type, amount, and context to produce the correct approval step sequence.
- Introduce a State Machine that enforces valid transitions and records audit entries on every change.
- Introduce a Workflow Engine orchestrator that wires rule evaluation, step creation, decisions, and parallel-step resolution.
- Expose API endpoints for creating, submitting, deciding, and cancelling workflow instances.
- Add a test project (`FlowGate.Core.Tests`) covering engine, rule, and state-machine behavior with TDD.

## Capabilities

### New Capabilities
- `workflow-engine`: Orchestrate the full lifecycle of a workflow instance from Draft through final decision.
- `rule-engine`: Evaluate dynamic business rules to produce the correct approval step definitions per request.
- `state-machine`: Guard and execute valid status transitions with audit trail persistence.
- `parallel-step-support`: Allow approval steps to execute in parallel groups, advancing only when all group members have decided.
- `approvals-inbox`: Expose pending approval tasks scoped to the authenticated approver.

### Modified Capabilities
- `workflow-instances-api`: Extend the existing listing endpoint with full CRUD + decision actions.
- `domain-model`: Enrich `WorkflowInstance` with `FormDataJson` and `ApprovalStep` with `ExecutionType`, `GroupId`, `StepIndex`.

## Impact

Affected areas: domain entities, EF Core configurations, EF migration, application service interfaces, infrastructure service implementations, API controller, dependency injection wiring, and a new test project.
