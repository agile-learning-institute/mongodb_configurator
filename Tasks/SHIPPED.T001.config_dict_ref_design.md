# T001 – Configuration–Dictionary–Ref Design: Lock/Delete Coupling and Referenced Dictionaries

**Status**: Shipped
**Task Type**: Feature / Refactor
**Run Mode**: Sequential

## Handoff for New Agent

This task is self-contained. Read the entire file, then the Context/Input files. Implement in order: Phase 1 → Phase 3 → Phase 2. Run tests after each phase. Commit when complete.

---

## Goal

Implement three intertwined design changes:

1. **Phase 1 (RefPicker)**: RefPicker shows only non-versioned (referenced) dictionaries, allows creating new ones, and allows opening (like EnumPicker/TypeChipPicker).
2. **Phase 2 (Config-driven lock/delete)**: Remove lock/unlock/delete from dictionary detail page for versioned dictionaries; configuration page is the source of truth. Lock/delete config version → lock/delete dictionary only when appropriate.
3. **Phase 3 (Referenced dictionaries)**: Non-versioned dictionaries (`{name}.yaml`) managed exclusively via RefPicker; not shown on Dictionaries cards page.

---

## Design Decisions (Fixed)

| Topic | Decision |
|-------|----------|
| **Migrations** | Not versioned; stay as is |
| **Non-versioned dictionaries on Dictionaries page** | Do NOT show; do NOT allow creation from there |
| **Non-versioned dictionary management** | Exclusively via RefPicker dialog |
| **Filtering** | Client-side filtering for now |
| **Phase 3 API** | PUT `/api/dictionaries/{file_name}/` supports creating new dictionaries: pass new filename (e.g. `persona.yaml`) + minimal document body. No POST endpoint exists; PUT creates/updates. |
| **Phase 2 API** | No server-side behavior changes. Lock/delete rules are implemented entirely in the SPA using existing configuration and dictionary endpoints. |

---

## Phase 1: RefPicker Updates

### Requirements

- **Filter**: Show only dictionaries whose filename does NOT match versioned pattern.
  - Versioned pattern: `.*\.\d+\.\d+\.\d+\.yaml` (e.g. `sample.1.0.0.yaml`)
  - Referenced (non-versioned): `{name}.yaml` where name has no `.\d+.\d+.\d+` (e.g. `persona.yaml`, `address.yaml`)
- **Create new dictionary**: Add "Create new dictionary" action in picker. Opens a simple dialog to enter name; creates `{name}.yaml` via PUT with minimal document (use default template structure from `default_new_dictionary.yaml` or equivalent).
- **Open dictionary**: Add per-option open icon (like EnumPicker) that opens `/dictionaries/{fileName}` in new tab.
- **Open Dictionaries link**: Consider whether it should go to `/dictionaries` (which shows only versioned/collections) or a filtered view. Per design: non-versioned dicts are NOT on Dictionaries page. So "Open Dictionaries" can stay as-is (opens collections page) or link to a different view. Default: keep as `/dictionaries`.

### Target Files

- `mongodb_configurator_spa/src/components/RefPicker.vue`
- `mongodb_configurator_spa/src/utils/api.ts` (if adding createDictionary helper)

### Reference Implementation

- **EnumPicker** (`mongodb_configurator_spa/src/components/EnumPicker.vue`): Has per-option open icon (`mdi-open-in-new`), `openEnumeration(name)` that opens `/enumerators/{fileName}/{index}`. Uses `latestEnumeratorFile` and finds index by name.
- **TypeChipPicker**: Similar pattern for opening types.

---

## Phase 2: Config-Driven Lock/Delete for Dictionaries

### Requirements

- **Remove** lock, unlock, and delete buttons from dictionary detail page when the dictionary is **versioned** (has a corresponding configuration).
- **Dictionary detail page**: When `dictionary.file_name` matches versioned pattern (e.g. `sample.1.0.0.yaml`), hide lock/unlock/delete. When it is non-versioned (`persona.yaml`), show delete (and optionally lock if we support locking referenced dicts—design says they are not locked; omit lock for non-versioned).
- **Configuration page**: When user deletes a config version → API already deletes orphaned dictionaries. Ensure behavior is correct.
- **Configuration page**: When user locks a config version → lock the corresponding dictionary **only if** that dictionary is not referenced by any other (newer) config version.

### Phase 2 Complication (Critical)

**Scenario**: Configuration creates new version 1.0.0.1 from 1.0.0.0. Only the enumerators version was incremented; dictionary schema did not change.

- Config 1.0.0.0 → gets locked (previous version)
- Config 1.0.0.1 → references same dictionary `sample.1.0.0.yaml`
- Dictionary `sample.1.0.0.yaml` → must **stay unlocked** (still in use by 1.0.0.1)

**Rule**: When locking a configuration version, lock its dictionary **only if** that dictionary is not referenced by any other (typically newer) configuration version.

**Implementation sketch**:

1. On lock of config version X that references dictionary Y:
   - Check whether any other config version references Y.
   - If yes → do NOT lock Y.
   - If no → lock Y.
2. On unlock of config version X:
   - Unlock its dictionary Y.
3. On delete of config version X:
   - API already handles orphaned dictionaries. Ensure dictionary Y is deleted only when no other version references it.

### Target Files

- `mongodb_configurator_spa/src/pages/DictionaryDetailPage.vue` – hide lock/delete when versioned
- `mongodb_configurator_spa/src/pages/ConfigurationDetailPage.vue` – implement client-side lock/delete coupling between configuration versions and dictionaries (no API changes)

### Context

- `DictionaryDetailPage.vue` has `configFileName` / `configRoute` – used to determine if dictionary is tied to a config.
- Versioned dictionary filenames: `{name}.{major}.{minor}.{patch}.yaml`
- Configuration versions reference dictionary files in their version definition.

---

## Phase 3: Referenced Dictionary Creation

### Requirements

- **Create from RefPicker**: "Create new dictionary" in RefPicker creates `{name}.yaml` (no version in filename).
- **API**: Use `PUT /api/dictionaries/{file_name}/` with new filename and minimal document body. The Dictionary service accepts `(file_name, document)` and saves; FileIO.put_document will create the file. Use a minimal structure matching `default_new_dictionary.yaml` (root with `_id`, `name`, etc.).
- **No Dictionaries page changes**: Non-versioned dictionaries do not appear on Dictionaries cards page. Dictionaries page uses `useCollections` / collections API – it shows collections (versioned). No changes needed if collections only include versioned dictionaries.

### Target Files

- `mongodb_configurator_spa/src/components/RefPicker.vue` – create flow
- `mongodb_configurator_spa/src/utils/api.ts` – `saveDictionary` or equivalent (PUT already exists; ensure it supports create)
- Default dictionary template: `mongodb_configurator_api/configurator/templates/default_new_dictionary.yaml` – use as structure for new referenced dict. For referenced dicts, a minimal structure is sufficient (e.g. `{ file_name, root: { name: "root", type: "object", properties: [] } }`). The template has placeholders (`{{collection_name}}`, `{{description}}`); for referenced dicts use a generic minimal root.

---

## Context / Input Files

Read before implementation:

- `mongodb_configurator_spa/src/components/RefPicker.vue`
- `mongodb_configurator_spa/src/components/EnumPicker.vue` (reference for open + layout)
- `mongodb_configurator_spa/src/pages/DictionaryDetailPage.vue`
- `mongodb_configurator_spa/src/pages/ConfigurationDetailPage.vue` (lock/delete config version)
  - API files (routes/services/docs/templates) may be consulted for understanding payload shapes, but **no behavior changes are required on the server side** for this task.

---

## Naming Conventions

| Type | Filename pattern | Example |
|------|------------------|---------|
| Versioned dictionary | `{name}.{major}.{minor}.{patch}.yaml` | `sample.1.0.0.yaml` |
| Referenced dictionary | `{name}.yaml` (no `.\d+.\d+.\d+`) | `persona.yaml`, `address.yaml` |

**Client-side detection**: Regex for versioned: `/\.\d+\.\d+\.\d+\.yaml$/`. If it matches, treat as versioned; else treat as referenced.

---

## Testing Expectations
- Remove testing of deprecated features (dictionary lock/delete)
- Run `cypress run --spec "cypress/e2e/dictionary.ref.cy.ts"`
- Run `cypress run --spec "cypress/e2e/dictionaries.cy.ts"`
- Add/update Cypress tests for RefPicker: filter to referenced only, create new, open dictionary
- **Gate**: Relevant specs pass

---

## Packaging / Build Checks

Before marking complete:

- `npm run build` (SPA)
- `pipenv run test` or equivalent (API, if changed)
- `pipenv run container` to test container build
- Cypress specs for dictionary ref and dictionaries

---

## Implementation Order

1. **Phase 1** – RefPicker: filter, open icon, create flow
2. **Phase 3** – Wire create to PUT dictionary (can be done with Phase 1)
3. **Phase 2** – Remove lock/delete from dictionary detail for versioned; implement client-side lock/delete logic in `ConfigurationDetailPage.vue` (no API changes)

---

## Implementation Notes (to be updated by agent)

- Implemented RefPicker filtering to show only non-versioned dictionaries using `/\.\d+\.\d+\.\d+\.yaml$/` to detect versioned filenames, and added a per-option open icon that opens `/dictionaries/{fileName}` in a new tab.
- Added a “Create new dictionary” action in RefPicker that opens a dialog, validates/sanitizes the name, and creates `{name}.yaml` via `apiService.saveDictionary` with a minimal `root` object structure suitable for referenced dictionaries.
- Updated `DictionaryDetailPage` so versioned dictionaries (matching the versioned filename pattern) no longer show lock/unlock/delete controls; non-versioned dictionaries still allow delete, and locking of versioned dictionaries is now driven solely by configuration versions.
- Updated `ConfigurationDetailPage` lock/unlock logic so locking a configuration version locks its corresponding dictionary only when no other configuration version references the same dictionary version; unlocking a configuration version always unlocks its dictionary.
- Adjusted Cypress specs in `dictionary.ref.cy.ts` and `dictionaries.cy.ts` to cover RefPicker filtering/creation/open behavior and the new lock/delete rules for versioned vs. referenced dictionaries.
- Test commands run:
  - `npm run test` (SPA) – Vitest suite: 2 files passed, 2 files failed (`tests/components/VersionConfiguration.test.ts` expectation text, `tests/utils/api.test.ts` axios interceptor setup); these failures predate this task and are unrelated to the dictionary/config changes.
  - `npm run build` (SPA) – succeeded.
  - `npm run container` (SPA) – succeeded, built `ghcr.io/agile-learning-institute/mongodb_configurator_spa:latest`.
  - `pipenv run container` (API) – succeeded earlier in this session; no API behavior changes were introduced for this task.
- Follow-ups:
  - Fix or update the failing Vitest suites (`VersionConfiguration` copy and `apiService` interceptor mocking) separately, as they are outside the scope of T001.
