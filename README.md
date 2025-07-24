# The MongoDB Configurator

The MongDB Configurator is a utility for creating and managing Versioned MongoDB Configurations, specifically schema validation, indexing, and version migrations when needed. The configurator provides a containerized packaging of database configurations, and a web application that makes it easy to create and test those configurations. 

## Quick Start

### Prerequisites
- [Docker Desktop](https://docs.docker.com/get-started/get-docker/) - it's free (for you)

### Run the Configurator Playground
- Create a ``Configurator`` folder on your computer
- Use the download-raw icon to save this [docker-compose file](https://github.com/agile-learning-institute/mongodb_configurator/blob/main/compose_files/playground/docker-compose.yaml) and place it in that folder.

Open a terminal window and navigate to that folder, and run this command
```bash
docker compose up --detach
```
Then visit http://localhost:8082 to access the Configurator. When you are done playing, you can use 
```bash
docker compose down
```
to shut down the containers. 

Note that this will reset the playground to it's starting state. If you want to save all the files you have created in your configuration, see [the API Explorer](http://localhost:8081/docs/index.html) for information on how to get the documents. 

Better yet, if you think you will want to save your configuration, then create your own configuration database by creating a new GitHub repository using [This Template Repo](https://github.com/agile-learning-institute/mongodb_configurator_template) and follow the instructions in the readme.

## Configurator Components
```mermaid
flowchart LR
    CFG>Configuration 
    Database]
    API(MongoDB 
    Configurator)
    SPA([MongoDB 
    Configurator UI])
    MongoDB[(Mongo 
    Database)]

    CFG <--> API --> MongoDB
    SPA <--> API

    click CFG href "https://github.com/agile-learning-institute/mongodb_configurator_template"
    click API href "https://github.com/agile-learning-institute/mongodb_configurator_api"
    click SPA href "https://github.com/agile-learning-institute/mongodb_configurator_spa"
```

## Packaging
Your data engineer will build a docker image containing a tested configuration, and you can then deploy that container using various configurations. See your configuration repo README for details about how the container is tested and packaged.

For information about a good configuration for use by your development team, see the [developers deployment](./compose_files/developers/README.md) guide. 

For information about a good configuration for production deployment see the [production deployment](./compose_files/production/README.md) guide.

To better understand the playground deployment, see the [playground deployment](./compose_files/playground/README.md) guide.

