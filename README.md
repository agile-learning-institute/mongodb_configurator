# MongoDB Configurator

The MongDB Configurator is a utility for creating and managing Versioned MongoDB Configurations, specifically schema validation, indexing, and version migrations when needed. The configurator provides a containerized packaging of database configurations, and a web application that makes it easy to create and test those configurations. 

## Quick Start

### Prerequisites
- [Docker Desktop](https://docs.docker.com/get-started/get-docker/) - it's free (for you)

### Run the Configurator Playground
Create a ``Configurator`` folder and download this [docker-compose](./docker-compose.yaml) file there. Use this command to start the service
```bash
docker compose up -d
```
Then visit http://localhost:8082 to access the Configurator. When you are done playing, you can use 
```bash
docker compose down
```
to shut down the containers. 

**Note**: Shutting down the service will reset the playground to it's starting state. If you want to save all the files you have created in the playground, see [the API Explorer](http://localhost:8081/docs/index.html) for information on API commands you can use to get the documents. 

Better yet, if you think you will want to save your configuration, then create your own configuration repository by creating a new GitHub repository using [This Template Repo](https://github.com/agile-learning-institute/mongodb_configurator_template) and follow the instructions in the readme.

## Configurator Components
```mermaid
flowchart LR
    CFG>Configuration 
    Repository]
    API(Configurator
    API)
    SPA([Configurator 
    UI])
    MongoDB[(Mongo 
    Database)]

    CFG <--> API --> MongoDB
    SPA <--> API

    click CFG href "https://github.com/agile-learning-institute/mongodb_configurator_template"
    click API href "https://github.com/agile-learning-institute/mongodb_configurator_api"
    click SPA href "https://github.com/agile-learning-institute/mongodb_configurator_spa"
```
