# SRE Guide

## Overview
If you are responsible for deploying the configurations packaged by the Configurator, you will have a number of options, all related to what the deployment environment should be. This is a configuration as code utility, where the **Configurations** are a collection of yaml and json configuration files that are stored in a git repo. This [GitHub Template](https://github.com/agile-learning-institute/mongodb_configurator_template) repo should be used to create your custom project Configurator Repo. 

When configurations are ready for release, they are packaged as a Docker container that extends the Configurator API container by adding the configurations. See [this example](https://github.com/agile-learning-institute/mongodb_configurator_template/blob/main/Dockerfile) `Dockerfile` from the Template Repository. 

## Deployment Guides
Deployment configurations for different use cases are outlined below.

### Playground
The containers have a playground configuration that allows users to experiment with functionality without having to create a configuration repo. This playground is self-contained and changes are not persisted after containers are shut down. The [playground compose file](./docker-compose.yaml) runs:
- **mongodb**: Simple MongoDB instance
- **configurator_spa**: WebUI service
- **configurator_api** with:
    - `AUTO_PROCESS=false`
    - `EXIT_AFTER_PROCESSING=false`
    - `LOAD_TEST_DATA=true`
    - `ENABLE_DROP_DATABASE=true`
    - `MONGODB_REQUIRE_TLS=false`

### Data Engineer
The data engineer responsible for creating and updating MongoDB configurations will want to use the tool to edit the configuration files in a custom configuration repo. The [docker-compose](https://github.com/agile-learning-institute/mongodb_configurator_template/blob/main/docker-compose.yaml) file from the template repo has a `dev` profile which runs:
- **mongodb**: Simple MongoDB instance
- **configurator_spa**: WebUI service
- **configurator_api** with:
    - The repo config files mapped to `/input`
    - `AUTO_PROCESS=false`
    - `EXIT_AFTER_PROCESSING=false`
    - `LOAD_TEST_DATA=true`
    - `ENABLE_DROP_DATABASE=true`
    - `MONGODB_REQUIRE_TLS=false`

### Software Engineer
Software engineers who work with the configurations created by the Data Engineer will want to have a locally configured database, and be able to use the tool to see the configurations that are loaded. The [docker-compose](https://github.com/agile-learning-institute/mongodb_configurator_template/blob/main/docker-compose.yaml) file from the template repo contains a `deploy` profile which runs:
- **mongodb**: Simple MongoDB instance for local development
- **configurator_spa**: WebUI service
- **custom_api** with packaged configurations and:
    - `AUTO_PROCESS=true`
    - `EXIT_AFTER_PROCESSING=false`
    - `LOAD_TEST_DATA=true`
    - `ENABLE_DROP_DATABASE=false`
    - `MONGODB_REQUIRE_TLS=false`

### Cloud Deployment (Non-Production)
Typical cloud-hosted Dev or Test environments would want to run:
- **configurator_spa**: WebUI service
- **custom_api** with packaged configurations and:
    - `MONGO_CONNECTION_STRING=<your cloud database>`
    - `AUTO_PROCESS=true`
    - `EXIT_AFTER_PROCESSING=false`
    - `LOAD_TEST_DATA=true`
    - `ENABLE_DROP_DATABASE=false`

### Cloud Deployment (Production)
A typical production deployment would not need the SPA, and would use `EXIT_AFTER_PROCESSING=true` to run in batch mode.
- **custom_api** with packaged configurations and:
    - `MONGO_CONNECTION_STRING=<your cloud database>`
    - `AUTO_PROCESS=true`
    - `EXIT_AFTER_PROCESSING=true`
    - `LOAD_TEST_DATA=false`
    - `ENABLE_DROP_DATABASE=false`
