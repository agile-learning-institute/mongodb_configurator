# R200 – SPA Refactor to Dictionary First

**Status**: Pending
**Task Type**: Refactor / Feature
**Run Mode**: Sequential

## Goal

Refactor the UI to focus on Dictionaries and Types as the primary entry points. Configurations, Migrations, and Test Data are accessed through Dictionaries/Configurations rather than as top-level navigation. The result is a simplified, schema-centric experience with a modern card-based layout and neutral blue theme.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator/README.md`
- `mongodb_configurator_api/README.md`
- `mongodb_configurator_api/docs/openapi.yaml`
- `mongodb_configurator_api/configurator/services/template_service.py` (R160 output)

**Target files** (to be updated):

- `mongodb_configurator_spa/src/router/index.ts`
- `mongodb_configurator_spa/src/components/AppLayout.vue`
- `mongodb_configurator_spa/src/pages/DictionariesPage.vue`
- `mongodb_configurator_spa/src/pages/TypesPage.vue`
- `mongodb_configurator_spa/src/pages/DictionaryDetailPage.vue`
- `mongodb_configurator_spa/src/pages/ConfigurationDetailPage.vue`
- `mongodb_configurator_spa/src/components/NewCollectionDialog.vue`
- `mongodb_configurator_spa/src/components/RefPicker.vue`
- `mongodb_configurator_spa/src/components/EnumPicker.vue`
- `mongodb_configurator_spa/src/plugins/vuetify.ts`
- `mongodb_configurator_spa/src/utils/api.ts`
- `mongodb_configurator_spa/src/composables/useFiles.ts`
- New: `mongodb_configurator_spa/src/composables/useCollections.ts` (or extend)
- New: `mongodb_configurator_spa/src/components/CollectionCard.vue` (dictionary list cards)
- New: `mongodb_configurator_spa/src/components/TypeCard.vue` (types list cards)
- `cypress/e2e/*.cy.ts` (all Cypress specs)

## Requirements

### 1. Navigation and information architecture

- **Top-level nav**: Only **Dictionaries** and **Types** as list pages.
- **Configurations**: Accessed via a Dictionary card ("Open Configuration") or from the Dictionary detail page.
- **Migrations** and **Test Data**: Accessed only from a Configuration detail page (no standalone list in nav).
- **Enumerators**: No top-level nav; accessed via "Open Enumerators" link in EnumPicker when picking an enumerator in the Dictionary editor.
- **Admin**, **Event Viewer**, **Help**: Unchanged.

### 2. Dictionaries list page (collection-centric cards)

- One card per **collection** (not per dictionary file).
- Use `GET /api/collections/` (R150) when available; otherwise derive from configurations + dictionaries client-side.
- Each card shows:
  - Collection name
  - Latest version (minimal badge)
  - Two buttons: **Open Dictionary** (latest version), **Open Configuration**
- Responsive card grid: multiple columns based on viewport width (e.g. CSS Grid or Vuetify `v-row`/`v-col`).

### 2a. New Collection dialog (simplified)

- **Use R160 API**: Call `POST /api/configurations/collection/{name}` which creates config, dictionary, and test_data (see R160).
- **Remove version UI**: Drop the "Version Components" section (Major/Minor/Patch controls, increment buttons, version preview). Version numbers are an afterthought.
- **Always use v1.0.0.0**: New collections start at `1.0.0.0` by default (handled by API). No user input for version.
- **Focus on dictionary**: Dialog fields are Collection Name and (optionally) Description. User thinks "I'm creating a dictionary for a new collection," not "I'm setting up version numbers."

### 3. Types list page

- One card per type file.
- Same responsive card grid layout.
- Card: type name, **Open** button to type detail.
- "New Type" unchanged.

### 4. Dictionary detail page

- Link to **Configuration** for the collection (e.g. in header or toolbar).
- Version migration UI in header (minimal; user works with latest; older versions de-emphasized).
- Opening from list always uses latest dictionary for that collection.

### 5. Configuration access flow

- Configuration detail page remains; routing preserved.
- Entry points: from Dictionary card, from Dictionary detail page.
- Configuration detail continues to expose Migrations and Test Data for each version.
- **Configure Database** and **Drop Database** buttons: Move from the app bar (`AppLayout`) to the Configuration detail page. Place them prominently at the top of the page. Remove them from the main app bar to reduce focus on database operations; they remain available when the user is in the configuration context.

### 6. Picker enhancements

- **RefPicker** (when selecting a type): Add "Open type" link to navigate to the type detail page (in addition to picking).
- **EnumPicker** (when selecting an enumerator): Add "Open enumerators" link to navigate to enumerators page (or a focused enumerator view).

### 7. Theme

- Switch from green to a **neutral blue** color scheme in `vuetify.ts` (primary, secondary, accent, surface, etc.).

## Phased implementation (recommended order)

| Phase | Scope | Notes |
|------|-------|-------|
| **A** | Theme (blue) | Low risk; can be done first. |
| **B** | Navigation shell | Update `AppLayout` nav items; remove Configurations, Enumerators, Test Data, Migrations from sidebar; remove Configure Database and Drop Database buttons from app bar. Keep routes for now. |
| **C** | Collections API integration | Add `useCollections` (or extend) to call `GET /api/collections/`; fallback to client-side derivation if R150 not done. |
| **D** | Dictionaries list page | New card grid, `CollectionCard`, Open Dictionary + Open Configuration. |
| **D'** | New Collection dialog | Remove version UI; call R160 API (`POST /api/configurations/collection/{name}`). |
| **E** | Types list page | New card grid, `TypeCard`. |
| **F** | Dictionary detail | Config link, version UI. |
| **G** | Configuration flow | Ensure config is reachable only from dictionary; remove from nav. Add Configure Database and Drop Database buttons prominently at top of Configuration detail page. |
| **H** | Test Data & Migrations | Ensure they are only reachable from configuration detail. |
| **I** | RefPicker + EnumPicker | Add "Open" links. |
| **J** | Cypress | Update specs for new navigation and flows. |

## Testing expectations

- Unit tests updated for new composables and components.
- Cypress tests reworked for new navigation, card interactions, and picker "Open" links.

## Packaging / build checks

Before marking this task as completed:

- Run `npm run build` (or equivalent) and ensure the container builds cleanly.
- Run unit tests and Cypress tests per README.

## Dependencies / Ordering

- Should run **after**:
  - **R160** (create_collection with config + dictionary + test_data) – *required*; New Collection dialog uses this API.
  - **R150** (GET /api/collections/) – *recommended*; simplifies Dictionaries list. Can proceed without it using client-side logic.
  - **R100** (json_schema/latest) – *optional*; useful if dictionary detail needs schema without version.
- Should run **before**:
  - None

## Implementation notes (to be updated by the agent)

**Summary of changes**
