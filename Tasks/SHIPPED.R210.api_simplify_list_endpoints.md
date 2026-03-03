# R210 – API: Simplify List Dictionaries and List Types Endpoints

**Status**: Shipped
**Task Type**: Refactor
**Run Mode**: Sequential

## Goal

Simplify the API list endpoints for dictionaries and types to align with SPA usage. The SPA now uses:
- **Dictionaries**: `GET /api/collections/` (collection-centric view with description, latest version, etc.) — dictionaries are not listed directly
- **Types**: `GET /api/types/` (returns types with `root.description` for card display)

Consolidate and simplify these endpoints so the API surface matches SPA needs and reduces duplication.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator_api/README.md`
- `mongodb_configurator_api/configurator/routes/collection_routes.py`
- `mongodb_configurator_api/configurator/routes/type_routes.py`
- `mongodb_configurator_api/configurator/routes/configuration_routes.py`
- `mongodb_configurator_api/configurator/routes/dictionary_routes.py`
- `mongodb_configurator_spa/src/utils/api.ts`
- `mongodb_configurator_spa/src/composables/useCollections.ts`
- `mongodb_configurator_spa/src/composables/useFiles.ts`

## Requirements

- **List dictionaries**: The SPA uses `GET /api/collections/` for the Dictionaries page (collection cards). If `GET /api/dictionaries/` exists and is unused by the SPA, consider deprecating or removing it from the SPA client.
- **List types**: The SPA uses `GET /api/types/` for the Types page. The endpoint was extended to include `root.description` per type. Simplify the implementation (e.g., ensure types list loads efficiently, consider a summary endpoint if full Type load is expensive).
- Reduce code duplication and ensure response shapes are consistent with SPA expectations.
- Update OpenAPI docs if endpoint contracts change.

## Testing expectations

- Run API unit tests: `pipenv run test` or `pytest tests/`
- Run SPA build: `npm run build`
- Run Cypress: `cypress run --spec "cypress/e2e/dictionaries.cy.ts"` and `cypress run --spec "cypress/e2e/type.basic.cy.ts"`
- **Gate:** All tests pass

## Packaging / build checks

Before marking this task as completed:

- Run `pipenv run container` (API) and `npm run container` (SPA)
- Ensure no regressions in e2e tests

## Dependencies / Ordering

- Should run **after**: R200d (SPA refactor complete)
- Should run **before**: R211 (deprecate unused endpoints)

## Implementation notes (to be updated by the agent)

**Summary of changes**
- **API**: Added `Type.get_types_summary()` in `type_services.py` — lightweight list that reads YAML via `FileIO.get_document` for `_locked` and `root.description` without full Property parsing. Replaced per-file `Type()` instantiation in `get_types` route.
- **API**: Updated `type_routes.py` to use `Type.get_types_summary()` in `GET /api/types/`.
- **API**: Added `type_summaries` and `type_summary` schemas to OpenAPI; updated `GET /api/types/` response schema.
- **SPA**: Fixed `collectionsApi.getCollections` in `api.ts` to use `apiService.getCollections` (was incorrectly using `getConfigurations`).
- **SPA**: Updated `useCollections.test.ts` to mock `apiService.getCollections` and use `loadCollections` (was `fetchCollections`).
