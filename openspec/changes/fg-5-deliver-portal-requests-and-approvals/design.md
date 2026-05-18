## Context

The FlowGate backend now exposes a complete workflow engine via REST API (`/api/workflow-instances`). The portal (`FlowGate.Portal`) already has an authentication layer, routing shell, role guards, and a `PortalApiService` base. There are no request or approval pages yet — the product is unusable end-to-end until they exist.

The portal is Angular 19+ (standalone components, signals, `inject()`), using SCSS, with a `core/` folder for services/guards/models and a `pages/` folder for route-level components. Shared UI pieces go in `shared/`.

## Goals / Non-Goals

**Goals:**
- Deliver the full requester journey: create request → submit → track status.
- Deliver the full approver journey: inbox → open detail → decide.
- Implement the adjustment loop: approver requests changes → request returns to requester.
- Connect all portal pages to the backend engine endpoints.
- Provide clear status and SLA visibility for both personas.

**Non-Goals:**
- Admin workflow builder (separate change).
- File/attachment upload.
- Real-time push notifications (polling is acceptable).
- Full i18n/localization.
- Executive reporting dashboards.

## Decisions

### 1. Feature module `requests/` inside `pages/`

All new pages are grouped under `pages/requests/` following the existing convention. Lazy-loaded via Angular router using `loadChildren` to keep the initial bundle small.

**Alternatives considered:** A top-level `features/` folder — rejected to avoid deviating from the established `pages/` convention already in use.

### 2. Angular services in `core/services/` — one dedicated service for workflow instances

A `WorkflowInstancesService` is added to `core/services/` following the existing `PortalApiService` + `SessionService` pattern. It encapsulates all HTTP calls to `api/workflow-instances/*` and returns typed observables.

**Alternatives considered:** Extending `PortalApiService` directly — rejected because it would grow that file unboundedly and mix concerns.

### 3. Typed models in `core/models/portal-api.models.ts`

New interfaces (`WorkflowInstanceDetail`, `ApprovalStepDetail`, `AuditLogEntry`, `PendingApproval`) are appended to the existing models file to keep model discovery centralized.

### 4. Role-based UI visibility via `SessionService`

Decision action buttons (approve, reject, adjust, cancel) are shown/hidden based on `SessionService.session()` roles — no separate permissions service needed. The `Approver` role unlocks decision actions; `StandardUser` unlocks creation.

**Alternatives considered:** Backend-driven permissions per instance — deferred to a later change when ownership rules are more complex.

### 5. Status badge as shared component

A `StatusBadgeComponent` in `shared/` maps `WorkflowInstanceStatus` and `ApprovalStepStatus` strings to color classes. Reused across list and detail views.

## Risks / Trade-offs

- [Risk] Backend returns `InvalidOperationException` messages directly in 400/500 responses — the portal must surface them as user-friendly toasts rather than raw JSON. → Mitigation: HTTP error interceptor maps backend error body to a toast notification.
- [Risk] The `GET /pending` endpoint filters by role server-side; if the dev auth headers are missing the role claim, the inbox returns empty. → Mitigation: Document required headers in the dev proxy config and README.
- [Risk] No optimistic UI — every action requires a round-trip before the page updates. → Acceptable for v1 given the approval domain requires confirmed state.
