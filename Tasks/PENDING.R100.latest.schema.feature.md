# R100 – Add get jsonschema/latest api endpoint.

**Status**: Pending
**Task Type**: Feature
**Run Mode**: Sequential

## Goal

Update the mongodb_configurator_api to add a new GET ...json_schema/ that does not require a version number parameter, but instead opens the configuration file to find the latest version, and uses that version to generate the schema. Developer Edition docker-compose.yaml, and the welcome page index.html with changes after the architecture.yaml file has been updated with new services.

## Context / Input files

These files must be treated as **inputs** and read before implementation:

- `mongodb_configurator/README.md`
- `mongodb_configurator_api/README.md`

**Target files** (to be updated):

- `mongodb_configurator_api/configurator/routes/configuration_routes.py`
- `mongodb_configurator_api/configurator/services/configuration_services.py`
- `mongodb_configurator_api/configurator/services/configuration_version.py` <if needed>
- `mongodb_configurator_api/test/routes/test_configuration_routes.py`
- `mongodb_configurator_api/test/services/test_configuration_services.py`
- `mongodb_configurator_api/test/services/test_configuration_version.py` <if needed>
- `mongodb_configurator_api/test/stepci/configuration.yaml`
- `mongodb_configurator_api/docs/openapi.yaml`

## Requirements

Update `mongodb_configurator_api` by adding a new API endpoint to get a JSON Schema without providing a version number. The endpoint should return the "latest" version defined for the specified collection. Unit testing should be updated, as well as stepci end-to-end testing.

## Testing expectations

- Unit testing should be updated for new endpoint and service functions
- Stepci end-to-end testing should be updated with new get tests
- **None**

## Packaging / build checks

Before marking this task as completed:
- Run ``make container`` and ensure that the container builds cleanly.
- Use the pipenv commands shown in the readme to run unit testing, and stepci testing and ensure all code passes.

## Dependencies / Ordering

- Should run **after**:
  - **None**
- Should run **before**:
  - **None**

## Implementation notes (to be updated by the agent)

**Summary of changes**
