# R211 – API: Deprecate list configurations, migrations, test data endpoints

**Status**: Shipped
**Task Type**: Chore
**Run Mode**: Sequential

## Goal

Mark the list endpoints for configurations, migrations, and test data as deprecated in the API.

## Implementation notes

- Added `deprecated: true` and deprecation descriptions to OpenAPI for:
  - GET /api/configurations/ – use GET /api/collections/ instead
  - GET /api/test_data/ – access via configuration versions
  - GET /api/migrations/ – access via configuration versions
- Endpoints remain functional for backward compatibility (SPA uses getTestData/getMigrations in VersionInformationCards)
