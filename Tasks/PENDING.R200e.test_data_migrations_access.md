# R200e – Test Data and Migrations Access

**Status**: Pending
**Task Type**: Refactor
**Run Mode**: Sequential

## Goal

Ensure Test Data and Migrations are reachable only from Configuration detail (no direct nav). Update any routes or redirects that might expose them as top-level. Verify no orphaned links. Enumerators remain accessible via EnumPicker "Open enumerators" link.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator/README.md`
- `mongodb_configurator_spa/README.md`
- `mongodb_configurator_spa/src/router/index.ts`

**Target files** (to be updated):

- `mongodb_configurator_spa/src/router/index.ts` (if redirects needed)
- `mongodb_configurator_spa/src/components/AppLayout.vue` (verify nav items)
- Any pages/components that link to Test Data or Migrations
- `cypress/e2e/test_data.cy.ts`
- `cypress/e2e/migrations.cy.ts`
- `cypress/e2e/enumerators.cy.ts`

## Requirements

### Test Data and Migrations access (Phase H)

- Test Data: reachable only from Configuration detail page (via chips/links)
- Migrations: reachable only from Configuration detail page (via chips/links)
- No standalone list in nav for Test Data or Migrations
- Enumerators: no top-level nav; accessed via "Open enumerators" link in EnumPicker when picking an enumerator in the Dictionary editor
- Update any routes that might expose them as top-level (e.g. redirects from old nav)
- Verify no orphaned links

## Testing expectations

- Run `cypress run --spec "cypress/e2e/test_data.cy.ts"`, `cypress run --spec "cypress/e2e/migrations.cy.ts"`
- Run `cypress run --spec "cypress/e2e/enumerators.cy.ts"` (enumerators accessed via picker)
- **Gate:** Test data, migrations, enumerators tests pass

## Packaging / build checks

Before marking this task as completed:

- Run `npm run build` and ensure the container builds cleanly
- Run Cypress specs per above

## Dependencies / Ordering

- Should run **after**:
  - R200d (Types and Configuration flow)
- Should run **before**:
  - R200f

## Implementation notes (to be updated by the agent)

**Summary of changes**
