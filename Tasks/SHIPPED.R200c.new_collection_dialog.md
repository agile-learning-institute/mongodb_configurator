# R200c – New Collection Dialog

**Status**: Shipped
**Task Type**: Refactor
**Run Mode**: Sequential

## Goal

Simplify the New Collection dialog by removing version UI (Major/Minor/Patch controls) and calling the R160 API (`POST /api/configurations/collection/{name}`) which creates config, dictionary, and test_data. New collections start at 1.0.0.0 by default (handled by API).

## Prerequisites

R160 **required**. Uses `POST /api/configurations/collection/{name}`; cannot proceed without R160.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator/README.md`
- `mongodb_configurator_api/README.md`
- `mongodb_configurator_api/docs/openapi.yaml`
- `mongodb_configurator_api/configurator/services/template_service.py` (R160 output)

**Target files** (to be updated):

- `mongodb_configurator_spa/src/components/NewCollectionDialog.vue`
- `mongodb_configurator_spa/src/utils/api.ts` (if adding/updating create collection call)
- `cypress/e2e/configurations.cy.ts`

## Requirements

### New Collection dialog (Phase D')

- **Use R160 API**: Call `POST /api/configurations/collection/{name}` which creates config, dictionary, and test_data
- **Remove version UI**: Drop the "Version Components" section (Major/Minor/Patch controls, increment buttons, version preview)
- **Always use v1.0.0.0**: New collections start at `1.0.0.0` by default (handled by API). No user input for version
- **Focus on dictionary**: Dialog fields are Collection Name and (optionally) Description. User thinks "I'm creating a dictionary for a new collection," not "I'm setting up version numbers"

## Testing expectations

- Run `cypress run --spec "cypress/e2e/configurations.cy.ts"`
- Remove tests that assert version controls (e.g. `version-minor-plus-btn`, `version-display`)
- Add tests for simplified dialog (name + description only)
- **Gate:** Configurations tests pass

## Packaging / build checks

Before marking this task as completed:

- Run `npm run build` and ensure the container builds cleanly
- Run Cypress configurations spec

## Dependencies / Ordering

- Should run **after**:
  - R200a (Theme and Navigation Shell)
  - R160 (create_collection with config + dictionary + test_data) – **required**
- Should run **before**:
  - R200d

## Implementation notes (to be updated by the agent)

**Summary of changes**

- Simplified `NewCollectionDialog.vue`: removed version UI (Major/Minor/Patch controls, increment buttons, version preview). Dialog now has Collection Name and Description only.
- Uses R160 API: `apiService.createNewCollection(name, description)` calls `POST /api/configurations/collection/{name}` with `{ description }` body. API creates config, dictionary, and test_data at 1.0.0.0.
- Renamed dialog title to "Create New Dictionary". Validation: name must start with letter, alphanumeric + underscores.
- `cypress/e2e/configurations.cy.ts` updated: removed version control assertions; tests simplified dialog (name + description); uses `.find('input').type()` for Vuetify text fields.
