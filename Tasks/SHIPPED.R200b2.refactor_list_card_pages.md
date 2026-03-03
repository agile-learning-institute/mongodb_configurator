# R200b2 – Review and Refactor List-Card Pages

**Status**: Shipped
**Task Type**: Refactor
**Run Mode**: Sequential

## Goal

Review and refactor the two list-card pages (Dictionaries and Types) after R210 simplifies the API. Align patterns, reduce duplication, and ensure both pages use the simplified API response shapes consistently.

## Prerequisites

R210 **required**. The API list endpoints will have changed; this task refactors the SPA to match.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator_spa/src/pages/DictionariesPage.vue`
- `mongodb_configurator_spa/src/pages/TypesPage.vue`
- `mongodb_configurator_spa/src/components/CollectionCard.vue`
- `mongodb_configurator_spa/src/components/TypeCard.vue`
- `mongodb_configurator_spa/src/composables/useCollections.ts`
- `mongodb_configurator_spa/src/composables/useFiles.ts` (or equivalent for types)
- `mongodb_configurator_spa/src/utils/api.ts`
- R210 implementation notes (after R210 is shipped)

## Requirements

- **Review** both list-card pages for:
  - Duplicated layout, loading, error, and empty-state logic
  - Inconsistent patterns between Dictionaries and Types
  - Alignment with R210 API response shapes
- **Refactor** to:
  - Extract shared list-card page patterns (e.g., grid layout, loading/error/empty states) into a reusable component or composable
  - Ensure `CollectionCard` and `TypeCard` follow consistent conventions
  - Update composables to use simplified API endpoints from R210
- Preserve existing behavior and Cypress coverage.

## Testing expectations

- Run `cypress run --spec "cypress/e2e/dictionaries.cy.ts"`
- Run `cypress run --spec "cypress/e2e/type.basic.cy.ts"`
- **Gate:** All list-card e2e tests pass

## Packaging / build checks

Before marking this task as completed:

- Run `npm run build` and ensure the container builds cleanly
- Run Cypress specs per above

## Dependencies / Ordering

- Should run **after**:
  - R210 (API simplify list endpoints)
- Should run **before**:
  - R200e

## Implementation notes (to be updated by the agent)

**Summary of changes**
- Created `ListCardPageLayout.vue` — shared layout component with loading, error, empty, and card-grid states. Accepts `title`, `loading`, `error`, `items`, `pageKey`, `emptyIcon`, `emptyTitle`, `emptyMessage`; slots: `header-actions`, `empty-action`, default (card grid).
- Refactored `DictionariesPage.vue` and `TypesPage.vue` to use `ListCardPageLayout`. Preserved all `data-test` attributes for Cypress.
