## ADDED Requirements

### Requirement: Rule Engine shall produce correct approval steps based on request context
The Rule Engine SHALL evaluate `RuleContext` (processType, amount, currency, extraFields) and return an ordered list of `ApprovalStepDefinition` records.

#### Scenario: Simple reimbursement under 5000 produces single manager step
- **GIVEN** a `RuleContext` with `amount = 1000` and `processType = "reimbursement"`
- **WHEN** the Rule Engine evaluates the context
- **THEN** the result contains exactly one step: `ManagerApproval` with `ExecutionType = Sequential`

#### Scenario: Amount over 5000 adds Finance step after Manager
- **GIVEN** a `RuleContext` with `amount = 6000` and `processType = "reimbursement"`
- **WHEN** the Rule Engine evaluates the context
- **THEN** the result contains `ManagerApproval` (index 0) and `FinanceApproval` (index 1), both Sequential

#### Scenario: Amount over 10000 adds Director step after Finance
- **GIVEN** a `RuleContext` with `amount = 15000` and `processType = "reimbursement"`
- **WHEN** the Rule Engine evaluates the context
- **THEN** the result contains `ManagerApproval`, `FinanceApproval`, and `DirectorApproval` in order, all Sequential

#### Scenario: Contract type adds LegalReview parallel to Finance
- **GIVEN** a `RuleContext` with `amount = 8000` and `processType = "contract"`
- **WHEN** the Rule Engine evaluates the context
- **THEN** the result contains `ManagerApproval` (Sequential), `FinanceApproval` (Parallel, GroupId = "finance-legal"), and `LegalReview` (Parallel, GroupId = "finance-legal")
