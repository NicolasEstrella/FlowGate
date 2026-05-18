## Context

`FlowGate.Core` has a clean domain foundation and persistence layer but no execution logic. The existing `WorkflowInstance`, `ApprovalStep`, and `AuditLog` entities provide anchor points. The engine must be introduced as application-layer services that operate on those entities through EF Core without breaking the existing API surface.

## Goals / Non-Goals

**Goals:**
- Implement the full synchronous approval lifecycle: Draft → Submitted → InApproval → Approved | Rejected | AdjustmentsRequested → (loop) → Cancelled.
- Support sequential, parallel, and conditional step execution patterns.
- Produce an auditable, inspectable state transition trail per instance.
- Expose API endpoints that allow the portal to operate the complete approval cycle.
- Cover the engine with TDD unit tests before implementing production code.

**Non-Goals:**
- Asynchronous workers, RabbitMQ integration, or background jobs.
- Real-time notifications (email, push, webhooks).
- Automated SLA escalation workers.
- Dynamic workflow definition builder or admin UI for rule configuration.
- Document generation.

## Decisions

1. **Rule Engine as Strategy pattern with hardcoded rules.**
   Rationale: the set of rules is known and small. A strategy pattern keeps each rule isolated and testable without introducing configuration complexity before the rules stabilize.
   Alternative: load rules from database configuration. Rejected because it requires a schema that does not yet exist and delays the engine without adding demonstrable value.

2. **Parallel steps grouped by `GroupId` string on `ApprovalStep`.**
   Rationale: allows arbitrary grouping of concurrent steps without a separate join table. The engine waits for all steps in a group to reach a terminal state before advancing.
   Alternative: separate `StepGroup` entity. Rejected as over-engineering for the current rule set.

3. **`FormDataJson` stored as JSONB on `WorkflowInstance`.**
   Rationale: request payloads differ by process type and are not fixed-schema yet. JSONB preserves flexibility while keeping the data attached to the instance.
   Alternative: separate typed `RequestData` entity per process type. Rejected because it requires finalizing type schemas before the engine is validated.

4. **State machine guards enforced at the service layer, not the entity.**
   Rationale: keeps domain entities as plain data models and concentrates transition logic in a testable service that can be mocked by upper layers.

5. **Audit entries written inline on each transition** using the existing `AuditLog` entity.
   `DetailsJson` stores `{ "previousState": "...", "nextState": "...", "comment": "..." }` as JSON.

## Architecture

```
API Controller (WorkflowInstancesController)
     │
     ▼
IWorkflowEngine  (application interface)
     │
     ├── IRuleEngine         → produces ApprovalStepDefinition[]
     ├── IWorkflowStateMachine → guards and applies transitions
     └── FlowGateDbContext   → persists entities
```

### Rule Engine Rules
| Condition | Steps added |
|-----------|-------------|
| amount ≤ 5 000 | ManagerApproval (Sequential) |
| amount > 5 000 | + FinanceApproval (Sequential) |
| amount > 10 000 | + DirectorApproval (Sequential) |
| type = "contract" | + LegalReview (Parallel with Finance, same GroupId) |

### State Machine Transitions
| From | To | Guard |
|------|----|-------|
| Draft | Submitted | requester is owner |
| Submitted | InApproval | always (engine) |
| InApproval | Approved | all steps approved |
| InApproval | Rejected | any step rejected |
| InApproval | AdjustmentsRequested | approver requests adjustment |
| AdjustmentsRequested | Submitted | requester resubmits |
| Any non-terminal | Cancelled | owner or Admin |

## Risks / Trade-offs

- [Hardcoded rule engine will need refactoring when rules become configurable] → Mitigation: keep each rule as a named strategy behind `IRuleEngine` so swap-in is additive.
- [JSONB deserialization adds coupling to JSON shape] → Mitigation: use `Dictionary<string, string>` as the FormData type — no typed deserialization in engine code.
- [Parallel step logic adds coordination complexity] → Mitigation: cover thoroughly with unit tests before shipping.

## Migration Plan

1. Add `StepExecutionType` enum and enrich `ApprovalStep` / `WorkflowInstance` entities.
2. Update EF configurations and generate migration.
3. Create interfaces: `IRuleEngine`, `IWorkflowStateMachine`, `IWorkflowEngine`.
4. Implement services behind those interfaces (TDD: tests first).
5. Add DTOs and extend `WorkflowInstancesController` with new endpoints.
6. Register services in DI and validate end-to-end.

## Open Questions

- Should `FormDataJson` be indexed in PostgreSQL as JSONB, or is plain `text` sufficient for the current query patterns?
- Will the `DueAtUtc` per step be set by a fixed offset from submission or by a rule-driven deadline?
