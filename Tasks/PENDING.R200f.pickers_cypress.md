# R200f – Pickers and Full Cypress

**Status**: Pending
**Task Type**: Refactor
**Run Mode**: Sequential

## Goal

Add "Open type" link to RefPicker and "Open enumerators" link to EnumPicker. Update all remaining Cypress specs for new navigation and flows. Ensure full test suite passes and build succeeds.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator/README.md`
- `mongodb_configurator_spa/README.md`

**Target files** (to be updated):

- `mongodb_configurator_spa/src/components/RefPicker.vue`
- `mongodb_configurator_spa/src/components/EnumPicker.vue`
- `cypress/e2e/*.cy.ts` (all Cypress specs)
- `cypress/e2e/user.journey1.cy.ts` (dictionary-first flow)

## Requirements

### Picker enhancements (Phase I)

- **RefPicker** (when selecting a type): Add "Open type" link to navigate to the type detail page (in addition to picking)
- **EnumPicker** (when selecting an enumerator): Add "Open enumerators" link to navigate to enumerators page (or a focused enumerator view)

### Cypress updates (Phase J)

- Update all remaining Cypress specs for new navigation and flows
- Update `user.journey1.cy.ts` for dictionary-first flow (create config via Dictionaries → New Collection instead of Configurations)
- Ensure all 23 specs pass

## Testing expectations

- Run full `cypress run`
- Update `user.journey1.cy.ts` for dictionary-first flow (create config via Dictionaries → New Collection)
- **Gate:** All 23 specs pass; `npm run build` succeeds

## Packaging / build checks

Before marking this task as completed:

- Run `npm run build` and ensure the container builds cleanly
- Run `cypress run` – all specs must pass
- Run unit tests per README (if applicable)

## Dependencies / Ordering

- Should run **after**:
  - R200e (Test Data and Migrations access)
- Should run **before**:
  - None (final R200 sub-task)

## Implementation notes (to be updated by the agent)

**Summary of changes**
