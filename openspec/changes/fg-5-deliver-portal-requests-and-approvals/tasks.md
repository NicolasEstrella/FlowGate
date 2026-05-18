## 1. Models and API service

- [x] 1.1 Add `WorkflowInstanceDetail`, `ApprovalStepDetail`, `AuditLogEntry`, `PendingApproval`, `CreateWorkflowInstanceRequest`, and `SubmitDecisionRequest` interfaces to `core/models/portal-api.models.ts`.
- [x] 1.2 Create `core/services/workflow-instances.service.ts` with methods: `createRequest`, `submit`, `getById`, `decide`, `cancel`, `getPending`, `getAll`.

## 2. Shared UI components

- [x] 2.1 Create `shared/status-badge/status-badge.component.ts` — maps status strings to CSS color classes and renders a badge.
- [x] 2.2 Create `shared/confirm-modal/confirm-modal.component.ts` — reusable confirmation dialog with optional comment textarea (required flag configurable).
- [x] 2.3 Create `shared/elapsed-time/elapsed-time.pipe.ts` — Angular pipe that converts a date to a human-readable relative string (e.g., "2 days ago").

## 3. Request Creation page

- [x] 3.1 Create `pages/requests/new/request-new-page.component.ts` — reactive form with `workflowKey` (select or text), `title`, and a dynamic key-value field list.
- [x] 3.2 Add route `/workspace/requests/new` in `app.routes.ts` guarded by `authGuard` and `roleGuard([AppRole.StandardUser, AppRole.Admin])`.
- [x] 3.3 Wire form submission to `WorkflowInstancesService.createRequest()`, then navigate to the detail page on success.
- [x] 3.4 Display inline validation errors for missing required fields and API error toast on failure.

## 4. My Requests page

- [x] 4.1 Create `pages/requests/list/my-requests-page.component.ts` — loads `getAll()`, filters client-side by current session user ID, renders table with columns: title, workflow, status badge, created date.
- [x] 4.2 Add status filter dropdown/tab strip with client-side filtering; include "All" option.
- [x] 4.3 Add route `/workspace/requests` in `app.routes.ts` guarded by `authGuard`.
- [x] 4.4 Add navigation link in `AppShellComponent` sidebar/nav to "My Requests".

## 5. Request Detail page

- [x] 5.1 Create `pages/requests/detail/request-detail-page.component.ts` — loads `getById(id)` from route param, renders header (title, status badge, SLA elapsed time), step timeline, and audit log.
- [x] 5.2 Render step timeline: ordered by `stepIndex`, highlight `InProgress` steps, render parallel steps (same `groupId`) side-by-side or with a parallel indicator.
- [x] 5.3 Add route `/workspace/requests/:id` in `app.routes.ts` guarded by `authGuard`.

## 6. Decision actions

- [x] 6.1 Add "Submit for Approval" button visible when instance status is `Draft` or `AdjustmentsRequested` and current user is the requester. Calls `WorkflowInstancesService.submit(id)` then refreshes.
- [x] 6.2 Add "Approve" button visible when user role matches active step's `requiredRole`. Opens `ConfirmModalComponent` with optional comment. Calls `decide({ decision: 'Approve' })`.
- [x] 6.3 Add "Reject" button with mandatory comment in modal. Calls `decide({ decision: 'Reject' })`. Comment field required — block submission if empty.
- [x] 6.4 Add "Request Adjustment" button with mandatory comment. Calls `decide({ decision: 'RequestAdjustment' })`.
- [x] 6.5 Add "Cancel" button visible only to the requester when instance status is `Draft`, `Submitted`, or `AdjustmentsRequested`. Opens confirmation modal. Calls `WorkflowInstancesService.cancel(id)`.

## 7. Approver Inbox page

- [x] 7.1 Create `pages/requests/inbox/approver-inbox-page.component.ts` — loads `getPending()`, renders table with: title, workflow name, requester, step name, created date, and a "Review" link to detail page.
- [x] 7.2 Add empty-state message when inbox is empty.
- [x] 7.3 Add a "Refresh" button that re-fetches without page reload.
- [x] 7.4 Add route `/workspace/approvals` in `app.routes.ts` guarded by `authGuard` and `roleGuard([AppRole.Approver, AppRole.Finance, AppRole.Legal])`.
- [x] 7.5 Add navigation link in `AppShellComponent` sidebar/nav to "Approvals" (visible only for approver roles).

## 8. Error handling and HTTP integration

- [x] 8.1 Ensure HTTP error interceptor surfaces backend error messages as toast notifications (extend existing interceptor or create a new one).
- [x] 8.2 Verify that `proxy.conf.json` routes `/api/**` to the backend correctly for all new endpoints.
