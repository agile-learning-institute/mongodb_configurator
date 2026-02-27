# R200b – Collections API and Dictionaries List

**Status**: Pending
**Task Type**: Refactor / Feature
**Run Mode**: Sequential

## Goal

Add `useCollections` composable to fetch collection-centric data, create `CollectionCard` component, and refactor the Dictionaries page to a card grid layout (one card per collection). Each card shows collection name, latest version badge, Open Dictionary and Open Configuration buttons.

## Prerequisites

R150 **required**. Uses `GET /api/collections/`; cannot proceed without R150.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator/README.md`
- `mongodb_configurator_api/README.md`
- `mongodb_configurator_api/docs/openapi.yaml`
- `mongodb_configurator_spa/src/composables/useFiles.ts` (or similar)

**Target files** (to be updated):

- `mongodb_configurator_spa/src/pages/DictionariesPage.vue`
- `mongodb_configurator_spa/src/utils/api.ts` (if adding collections endpoint)
- New: `mongodb_configurator_spa/src/composables/useCollections.ts` (or extend existing)
- New: `mongodb_configurator_spa/src/components/CollectionCard.vue`
- `cypress/e2e/dictionaries.cy.ts`

## Requirements

### Collections API integration (Phase C)

- Add `useCollections` composable (or extend existing) calling `GET /api/collections/` (R150)

### Dictionaries list page (Phase D)

- One card per **collection** (not per dictionary file)
- Create `CollectionCard.vue` with:
  - Collection name
  - Latest version (minimal badge)
  - Two buttons: **Open Dictionary** (latest version), **Open Configuration**
- Responsive card grid: multiple columns based on viewport width (e.g. CSS Grid or Vuetify `v-row`/`v-col`)
- Refactor `DictionariesPage.vue` to use card grid layout

## Testing expectations

- Run `cypress run --spec "cypress/e2e/dictionaries.cy.ts"`
- Update dictionaries spec for new card layout (e.g. `data-test="collection-card-*"` instead of file list)
- **Gate:** Dictionaries tests pass

## Packaging / build checks

Before marking this task as completed:

- Run `npm run build` and ensure the container builds cleanly
- Run Cypress dictionaries spec

## Dependencies / Ordering

- Should run **after**:
  - R200a (Theme and Navigation Shell)
  - R150 (`GET /api/collections/`) – **required**
- Should run **before**:
  - R200d

## Implementation notes (to be updated by the agent)

**Summary of changes**
