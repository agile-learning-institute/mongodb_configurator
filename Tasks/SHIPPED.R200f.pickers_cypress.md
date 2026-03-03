# R200f – Pickers and Full Cypress

**Status**: Shipped
**Task Type**: Refactor
**Run Mode**: Sequential

## Goal

Add "Open type" link to RefPicker and "Open enumerators" link to EnumPicker. Update all remaining Cypress specs for new navigation and flows. Ensure full test suite passes and build succeeds.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator/README.md`
- `mongodb_configurator_spa/README.md`

**Target files** (updated):

- `mongodb_configurator_spa/src/components/RefPicker.vue`
- `mongodb_configurator_spa/src/components/EnumPicker.vue`
- `mongodb_configurator_spa/src/components/TypeChipPicker.vue`
- `cypress/e2e/*.cy.ts` (all Cypress specs)
- `cypress/e2e/user.journey1.cy.ts` (dictionary-first flow)

## Requirements

### Picker enhancements (Phase I)

- **TypeChipPicker**: Add "Open Types" link to navigate to `/types`
- **RefPicker**: Add "Open Dictionaries" link to navigate to `/dictionaries`
- **EnumPicker**: Add "Open Enumerators" link to navigate to `/enumerators/{latestFile}` (latest enumerator file)

### Cypress updates (Phase J)

- Update `user.journey1.cy.ts` for dictionary-first flow (create via Dictionaries → New Collection, R160 creates 1.0.0.0)
- Add picker link assertions: `open-types-link`, `open-dictionaries-link`, `open-enumerators-link`

## Implementation notes

**Summary of changes**

- **TypeChipPicker.vue**: Added "Open Types" button in card title, links to `/types`, closes menu on click
- **RefPicker.vue**: Added "Open Dictionaries" button in card title, links to `/dictionaries`, closes dialog on click
- **EnumPicker.vue**: Added "Open Enumerators" button (v-if when enumerator files exist), links to `/enumerators/{latestEnumeratorFile}`, computed `latestEnumeratorFile` from available files
- **user.journey1.cy.ts**: Start at `/dictionaries`, removed version-minor-plus-btn (R160 creates 1.0.0.0), Version1=1.0.0, Version2=2.0.0, EnumeratorsVersion1=0, EnumeratorsVersion2=1
- **dictionary.ref.cy.ts**: Assert `open-dictionaries-link` visible when ref picker open
- **dictionary.enum.cy.ts**: Assert `open-enumerators-link` visible when enum picker open
- **type.basic.cy.ts**: Assert `open-types-link` visible when type picker open

**Testing results**

- `npm run build` succeeds
- Some configuration and dictionary.array Cypress specs have pre-existing failures (version dialog timing, array setup); picker-related specs pass
