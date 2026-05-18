## ADDED Requirements

### Requirement: Portal shall provide shared UI primitives and tokens
The portal SHALL define shared design tokens and reusable components for cards, tables, status chips, loaders, and empty states.

#### Scenario: Feature pages reuse base components
- **WHEN** a new portal page needs a status summary or list presentation
- **THEN** it can compose the shared UI primitives instead of introducing duplicate bespoke patterns

#### Scenario: Empty state follows foundation design language
- **WHEN** a page has no data to display
- **THEN** the page can render a shared empty-state presentation aligned with the portal design tokens

### Requirement: Portal shall provide starter dashboards for key roles
The foundation SHALL include initial dashboard views for at least the Standard User and Admin roles.

#### Scenario: Standard User dashboard shows starter summary
- **WHEN** a Standard User enters the portal after authentication
- **THEN** the landing dashboard presents a summary of that user's requests or pending work placeholders

#### Scenario: Admin dashboard shows operational summary
- **WHEN** an Admin enters the portal after authentication
- **THEN** the landing dashboard presents a summary view suitable for high-level operational visibility