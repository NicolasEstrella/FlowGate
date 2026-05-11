## Context

`FlowGate.Portal` is still the default Angular shell, with an empty route table and only the root component wired around `RouterOutlet`. The product, however, requires a role-aware corporate portal that will become the main operating surface for requests, approvals, dashboards, and administration. This foundation has to establish the shell, session model, API integration, and shared UI primitives before operational screens are added.

## Goals / Non-Goals

**Goals:**
- Replace the starter shell with a responsive authenticated application layout.
- Introduce role-aware routing, session behavior, and HTTP integration conventions.
- Create shared design tokens and reusable UI primitives that later features can reuse without visual drift.

**Non-Goals:**
- Full request submission flows, inbox screens, or advanced administrative pages.
- Final enterprise authentication integration beyond a controlled local or mocked session baseline.
- Complex workflow visualization or document-management interfaces.

## Decisions

1. Build the portal around a top-level authenticated shell with role-sensitive navigation.
   Rationale: later product phases add multiple operational surfaces, and the portal needs a stable navigational skeleton before those pages exist.
   Alternative considered: add standalone pages first and unify them later. Rejected because it would fragment layout, guards, and role affordances.

2. Centralize API access through Angular services, interceptors, and environment configuration.
   Rationale: this keeps backend integration concerns out of page components and gives the foundation one place to evolve authentication headers, base URLs, and error handling.
   Alternative considered: page-level HTTP calls. Rejected because it would create duplication before the main feature pages arrive.

3. Introduce shared UI tokens and base components before feature-heavy screens.
   Rationale: loaders, cards, tables, status chips, and empty states are cross-cutting elements and should not be reinvented by each future module.

4. Use a simulated or environment-driven session model as the first-stage auth baseline.
   Rationale: the backend and infrastructure foundations may evolve before final auth integration, but the portal still needs guards and role-aware behavior now.

## Risks / Trade-offs

- [A mocked or local session model can diverge from final authentication] -> Mitigation: keep session contracts isolated behind services and guards so the provider can be replaced later.
- [Early component abstractions can be too generic or too narrow] -> Mitigation: focus on the specific primitives already implied by dashboards and operational lists.
- [Visual direction chosen now may constrain later UX] -> Mitigation: define tokens and component APIs that support iteration without changing route and layout contracts.

## Migration Plan

1. Replace the starter route configuration with authenticated and public route structure.
2. Implement the application shell, navigation primitives, and role-aware dashboard entry points.
3. Add session services, guards, interceptors, environment-based API configuration, and shared error handling.
4. Introduce design tokens and shared components for loading, empty, table, card, and status patterns.
5. Validate the portal against the local backend endpoint topology and mocked/local session behavior.

## Open Questions

- Which roles must have distinct dashboard landing pages in the first foundation slice beyond Admin and Standard User?
- Should the initial shell include a dedicated unauthenticated login route, or is an environment-driven auto-session enough for this phase?