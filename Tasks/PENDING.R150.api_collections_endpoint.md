# R150 – Add GET /api/collections/ endpoint (API)

**Status**: Pending
**Task Type**: Feature
**Run Mode**: Sequential

## Goal

Add an API endpoint that returns a collection-centric view of the configuration repository. This endpoint simplifies the SPA's dictionary-first refactor by providing pre-computed "latest" information per collection, avoiding client-side parsing of dictionary filenames and version logic.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator_api/README.md`
- `mongodb_configurator_api/docs/openapi.yaml`
- `mongodb_configurator_api/configurator/services/configuration_services.py`
- `mongodb_configurator_api/configurator/services/configuration_version.py`
- `mongodb_configurator_api/configurator/utils/version_number.py`

**Target files** (to be updated):

- `mongodb_configurator_api/configurator/routes/configuration_routes.py` (or new `collection_routes.py`)
- `mongodb_configurator_api/configurator/server.py` (route registration)
- `mongodb_configurator_api/docs/openapi.yaml`
- `mongodb_configurator_api/tests/` (unit and stepci tests)

## Requirements

Add `GET /api/collections/` that returns an array of collection summaries. Each item should include:

- `collection_name` – derived from configuration `name`
- `configuration_file` – e.g. `sample.yaml`
- `latest_dictionary_file` – e.g. `sample.0.1.0.yaml` (from config's latest version)
- `latest_version` – e.g. `0.1.0.0` (config version string)
- `_locked` – whether the latest version is locked (optional)

The endpoint should iterate over configurations, and for each config derive the latest version from the `versions` array (chronological order). Use `VersionNumber.get_schema_filename()` to compute the dictionary filename for the latest version.

## Testing expectations

- Unit tests for the new route and service logic
- Stepci black-box test for the new endpoint

## Packaging / build checks

Before marking this task as completed:

- Run `pipenv run container` and ensure the container builds cleanly
- Run `pipenv run test` and `pipenv run stepci` (with appropriate `INPUT_FOLDER`)

## Dependencies / Ordering

- Should run **after**: None
- Should run **before**: R200 (SPA refactor) – recommended but not strictly required; SPA can derive this client-side if needed

## Implementation notes (to be updated by the agent)

**Summary of changes**
