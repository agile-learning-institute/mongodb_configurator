# R200a – Theme and Navigation Shell

**Status**: Shipped
**Task Type**: Refactor
**Run Mode**: Sequential

## Goal

Switch the SPA theme to neutral blue and update the navigation shell so only Dictionaries and Types appear as top-level nav items. Remove Configure Database and Drop Database buttons from the app bar. All routes remain reachable via direct URL.

## Prerequisites

No API dependencies. R200 pipeline assumes R100/R150/R160 are complete before starting R200a.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator/README.md`
- `mongodb_configurator_spa/README.md`

**Target files** (to be updated):

- `mongodb_configurator_spa/src/plugins/vuetify.ts`
- `mongodb_configurator_spa/src/components/AppLayout.vue`
- `cypress/e2e/*.cy.ts` (update specs that assert nav items)

## Requirements

### Theme (Phase A)

- Switch from green to a **neutral blue** color scheme in `vuetify.ts` (primary, secondary, accent, surface, etc.)

### Navigation shell (Phase B)

- Update `AppLayout.vue` nav items to only **Dictionaries** and **Types** as list pages
- Remove Configurations, Enumerators, Test Data, Migrations from sidebar
- Remove Configure Database and Drop Database buttons from app bar
- Keep all routes; routes remain reachable via direct URL
- Admin, Event Viewer, Help: unchanged

## Testing expectations

- Run `cypress run --spec "cypress/e2e/app.help.cy.ts"` (smoke)
- Run `cypress run --spec "cypress/e2e/dictionaries.cy.ts"` (nav to Dictionaries)
- Run `cypress run --spec "cypress/e2e/type.basic.cy.ts"` (nav to Types)
- Update specs that assert nav items (e.g. remove `nav-item-configurations` expectations)
- **Gate:** All tests pass before commit

## Packaging / build checks

Before marking this task as completed:

- Run `npm run build` and ensure the container builds cleanly
- Run Cypress specs per above

## Dependencies / Ordering

- Should run **after**:
  - R100, R150, R160 (pipeline prerequisites)
- Should run **before**:
  - R200b, R200c

## Implementation notes (to be updated by the agent)

**Summary of changes**
- vuetify.ts: Switched theme from green to neutral blue (primary #1565C0, surface #E3F2FD, etc.)
- AppLayout.vue: Nav items reduced to Dictionaries and Types only; removed Configure Database and Drop Database buttons from app bar; removed process/drop handlers and dialog; updated app-header gradient to blue; fallback help route to /dictionaries
- event-viewer.cy.ts, app.help.cy.ts: Updated to use configure-collection-btn from Configuration detail page instead of process-all-btn
