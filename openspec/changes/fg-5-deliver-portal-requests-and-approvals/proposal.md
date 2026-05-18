## Why

The FlowGate backend workflow engine is fully operational, but there is no user-facing interface to interact with it. Requesters cannot open or submit requests, and approvers cannot locate their pending tasks or act on them — making the product unusable end-to-end.

## What Changes

- New **Request Creation** flow: form to create and submit a workflow instance from the portal.
- New **Approver Inbox** page: filtered list of pending approval steps assigned to the current user's role.
- New **Request Detail** page: full workflow timeline, current status, approval steps, SLA indicator, comments, and decision actions.
- New **Decision Actions**: approve, reject, request adjustment, and cancel — each with comment input.
- New **My Requests** page: list of workflow instances created by the current user, with status and date.
- Angular service layer to consume all `api/workflow-instances` endpoints added in the backend.
- Route guards and role-based UI visibility (`StandardUser` for creation, `Approver` for inbox and decision actions).

## Capabilities

### New Capabilities

- `request-creation`: Multi-step form that collects form data, posts to the backend, and initiates the approval workflow.
- `approver-inbox`: Filtered, paginated list of steps in `InProgress` status that match the current user's role, with quick-action links.
- `request-detail`: Full-page view of a workflow instance: status badge, SLA indicator, step timeline, audit log, and contextual action buttons.
- `decision-actions`: Approve / Reject / Request Adjustment / Cancel actions with confirmation modal and optional comment.
- `my-requests`: List of all workflow instances created by the authenticated user, with status filter and navigation to detail.

### Modified Capabilities

<!-- No existing spec-level requirements are changing -->

## Impact

- **FlowGate.Portal**: New feature module `requests/`, new Angular services in `core/services/`, new shared components (status badge, timeline, action modal).
- **API contracts**: Consumes `POST /api/workflow-instances`, `POST /{id}/submit`, `GET /{id}`, `POST /{id}/decide`, `POST /{id}/cancel`, `GET /pending`.
- **Auth**: Uses `X-FlowGate-UserId`, `X-FlowGate-UserName`, `X-FlowGate-Roles` headers passed through the Angular dev proxy; role-aware UI rendering.
- **No backend changes required**: All endpoints are already implemented in `implement-workflow-engine`.
