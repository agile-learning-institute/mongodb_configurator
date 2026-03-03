# R200d – Types List and Configuration Flow

**Status**: Shipped
**Task Type**: Refactor / Feature
**Run Mode**: Sequential

## Goal

Create `TypeCard` component and refactor the Types page to a card grid. Add Configuration link to Dictionary detail page. Move Configure Database and Drop Database buttons from the app bar to the Configuration detail page (top of page). Ensure Configurations, Test Data, and Migrations remain reachable from Configuration detail only.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator/README.md`
- `mongodb_configurator_spa/README.md`

**Target files** (to be updated):

- `mongodb_configurator_spa/src/pages/TypesPage.vue`
- `mongodb_configurator_spa/src/pages/DictionaryDetailPage.vue`
- `mongodb_configurator_spa/src/pages/ConfigurationDetailPage.vue`
- New: `mongodb_configurator_spa/src/components/TypeCard.vue`
- `cypress/e2e/type.basic.cy.ts`
- `cypress/e2e/type.simple.cy.ts`
- `cypress/e2e/configurations.cy.ts`
- `cypress/e2e/dictionaries.cy.ts`

## Requirements

### Types list page (Phase E)

- One card per type file
- Same responsive card grid layout as Dictionaries
- Create `TypeCard.vue` with: type name, **Open** button to type detail
- "New Type" unchanged

### Dictionary detail (Phase F)

- Add link to **Configuration** for the collection (e.g. in header or toolbar)
- Version migration UI in header (minimal; user works with latest; older versions de-emphasized)
- Opening from list always uses latest dictionary for that collection

### Configuration flow (Phase G)

- Configuration detail page remains; routing preserved
- Entry points: from Dictionary card, from Dictionary detail page
- Move **Configure Database** and **Drop Database** buttons from app bar to Configuration detail page; place prominently at top of page
- Ensure routes for Configurations, Test Data, Migrations remain reachable from Configuration detail only (no nav changes)

## Testing expectations

- Run `cypress run --spec "cypress/e2e/type.basic.cy.ts"`, `cypress run --spec "cypress/e2e/type.simple.cy.ts"`
- Run `cypress run --spec "cypress/e2e/configurations.cy.ts"` (Configure/Drop buttons on config page)
- Run `cypress run --spec "cypress/e2e/dictionaries.cy.ts"` (dictionary → config link)
- **Gate:** Types, config, and dictionary tests pass

## Packaging / build checks

Before marking this task as completed:

- Run `npm run build` and ensure the container builds cleanly
- Run Cypress specs per above

## Dependencies / Ordering

- Should run **after**:
  - R200b (Collections and Dictionaries list)
  - R200c (New Collection dialog)
- Should run **before**:
  - R200e

## Implementation notes (to be updated by the agent)

**Summary of changes**
- Created TypeCard.vue with type name, description, version pill; card clickable for Open; gear icon for config (if applicable)
- Refactored TypesPage.vue to card grid layout; uses GET /api/types/ with root.description
- Removed ConfigurationsPage, MigrationsPage, TestDataPage, EnumeratorsPage list pages
- Redirects: /configurations, /migrations, /test_data → /dictionaries; /enumerators → /enumerators/enumerations.0.yaml (or latest)
- Added Enumerators link to nav drawer (useLatestEnumerator)
- Configure Database and Drop Database moved to Configuration detail page
- Configuration, Test Data, Migration, Enumerator detail pages: back/cancel → /dictionaries
