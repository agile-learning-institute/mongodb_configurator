# R160 – Extend create_collection API to create config, dictionary, and test_data

**Status**: Pending
**Task Type**: Feature
**Run Mode**: Sequential

## Goal

Extend `POST /api/configurations/collection/{name}` to create configuration, dictionary, **and** test_data files. Add a default new-dictionary template that can be overridden by APIs that extend the API container.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator_api/README.md`
- `mongodb_configurator_api/configurator/services/template_service.py`
- `mongodb_configurator_api/configurator/utils/config.py`
- `mongodb_configurator_api/configurator/utils/version_number.py`
- `mongodb_configurator_api/configurator/routes/configuration_routes.py`
- `mongodb_configurator_api/Dockerfile`
- `mongodb_configurator_api/docs/openapi.yaml`

**Target files** (to be updated):

- `mongodb_configurator_api/configurator/services/template_service.py`
- `mongodb_configurator_api/configurator/utils/config.py` (add template path if needed)
- `mongodb_configurator_api/configurator/routes/configuration_routes.py` (optional: accept description in request body)
- `mongodb_configurator_api/docs/openapi.yaml`
- `mongodb_configurator_api/tests/services/test_template_service_operations.py`
- `mongodb_configurator_api/tests/routes/test_configuration_routes.py`
- New: `mongodb_configurator_api/configurator/templates/default_new_dictionary.yaml` (bundled default)

## Requirements

### 1. Extend create_collection to create test_data

- `TemplateService.create_collection(collection_name)` must create:
  1. Configuration file (`{name}.yaml`)
  2. Dictionary file (`{name}.1.0.0.yaml` – version 1.0.0.0)
  3. Test data file (`{name}.1.0.0.0.json` – empty array `[]`)

- Use version **1.0.0.0** for new collections (major.minor.patch.enumerator).
- Configuration version must include `test_data: {name}.1.0.0.0.json`, `migrations: []`, `add_indexes: []`, `drop_indexes: []`, and reference the dictionary `{name}.1.0.0.yaml` (derived from version).

### 2. Default new-dictionary template (overridable)

- **Built-in default**: A default dictionary template bundled in the package at `configurator/templates/default_new_dictionary.yaml`. This defines the initial `root` structure for new dictionaries (e.g. `_id`, `name`, `last_saved` or similar minimal schema).
- **Override path**: `{INPUT_FOLDER}/api_config/templates/default_new_dictionary.yaml`
  - At runtime, if this file exists, load it and use it instead of the built-in default.
  - Extending APIs can `COPY` their own template to this path in their Dockerfile to override the default.
- **Substitution**: The template may use placeholders (e.g. `{{collection_name}}`, `{{description}}`) that are replaced when creating a new collection.

### 3. Override mechanism for extenders

- Ensure `api_config/templates/` directory exists or is created when the override is used.
- Document in README how extenders can provide a custom default dictionary template by copying a file to `/input/api_config/templates/default_new_dictionary.yaml` in their extended image.

## Testing expectations

- Unit tests for `TemplateService.create_collection` verify config, dictionary, and test_data are created.
- Unit tests verify override: when override file exists, its content is used.
- Stepci or integration test for the create_collection endpoint with the new behavior.

## Packaging / build checks

Before marking this task as completed:

- Run `pipenv run container` and ensure the container builds cleanly.
- Run `pipenv run test` and stepci tests.

## Dependencies / Ordering

- Should run **after**: None
- Should run **before**: R200 (SPA refactor) – New Collection dialog will use this API.

## Implementation notes (to be updated by the agent)

**Summary of changes**
