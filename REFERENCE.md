# MongDB Configurator 

The MongDB Configurator is a utility for creating and managing Versioned MongoDB Configurations, specifically schema validation, indexing, and version migrations. The configurator provides a containerized packaging of database configurations, and a web application that makes it easy to create and test those configurations. 

## Configuration Database
Is just a set of folders and documents typically stored in a Git repository to drive deployment automation. The configuration database consist of [Collection Configurations](#configurations), [Dictionaries](#dictionaries), [Type Definitions](#types), and [Test Data](#test-data)

### Configurations
Collection Configurations are the cornerstone of your configuration database. 
Configurations for a collection define the version history of that collection.
 
#### Version Numbers
The Configurator uses a four-component versioning scheme: `major.minor.patch.enumerator`.
- **Major**: Breaking changes to schema structure.
- **Minor**: Backward-compatible feature additions.
- **Patch**: Bug fixes and minor improvements.
- **Enumerator**: Version of Enumerators used in rendering.

Examples:
- `users.0.0.1.1` - First version of a users collection.
- `users.1.2.3.4` - Version 1.2.3 of the dictionary with version v4 enumerators.
- `users.2.0.0.4` - Major version upgrade, still uses v4 enumerators.

#### Version configuration options.
  - **Add Indexes**: New indexes to create with this version.
  - **Drop Indexes**: Existing indexes to be removed.
  - **Aggregations**: Aggregation pipelines to transform data.
  - **Test Data**: Test data file to be loaded (if enabled).

#### Version processing.
When configuration processing occurs, the following steps are executed for each version.
  - **Remove existing schema validation**.
  - **Drop indexes** (if provided).
  - **Run aggregations** (if provided).
  - **Add indexes** (if provided).
  - **Apply new schema validation**.
  - **Load test data** (if provided and enabled).
  - **Update deployed version number**.

### Dictionaries
Define the versioned data schema for the MongoDB collections. A dictionary name includes it's dictionary version (1.2.3 without enumerators). Dictionaries implement the [Simple Schema](./SIMPLE_SCHEMA.md) standard to define data structures. Once packaged dictionaries are immutable.

### Types
[Simple Schema](./SIMPLE_SCHEMA.md) custom types for the database. Once packaged Types are immutable. 

### Test Data
Test data for collections. This is also the home of the enumerators.json file use in rendering and configuration. Test Data files are not required to fit strict versioning standards in the file names, but it is recommended. 

### Enumerators
The enumerators.json file is loaded into ENUMERATORS_COLLECTION_NAME as part of any processing. 

## Troubleshooting
TODO Describe read-only files.

## Advanced Database Configuration

The API is configured through configuration files or environment variables.
- If you create a configuration file at the root of the /input it will always be used. 
- If no configuration file is found, and environment variable can be used. 
- If no environment variable is found, the default value is used.
- The /config endpoint will report the values for these configuration items.

| Config Item    | Description | Default |
|----------------|------------------|---------|
| `INPUT_FOLDER` | API Port number | `8081` |
| `LOGGING_LEVEL` | Logging level (DEBUG, INFO, WARNING, ERROR) | `INFO` |
| `MONGO_DB_NAME` | MongoDB database name | `stage0` |
| `MONGO_CONNECTION_STRING` | MongoDB connection string | `mongodb://root:example@localhost:27017/` |
| `ENUMERATORS_COLLECTION_NAME` | Enumerators Collection name | `Enumerators` |
| `VERSION_COLLECTION_NAME`| Version Collection name | `CollectionVersions` |
| `API_PORT` | API Port number | `8081` |
| `SPA_PORT` | SPA Port number | `8082` |
| `AUTO_PROCESS` | Process configurations | `false` |
| `EXIT_AFTER_PROCESSING` | Exit after processing | `false` |
| `LOAD_TEST_DATA` | Load Test data | `false` |
| `BUILT_AT` | Built At Date-Time | DO NOT SET THIS VALUE |

