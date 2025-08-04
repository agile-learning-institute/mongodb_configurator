# MongoDB Configurator

The MongDB Configurator is a utility for creating and managing Versioned MongoDB Configurations, specifically schema validation, indexing, and version migrations when needed. The configurator provides a containerized packaging of database configurations, and a web application that makes it easy to create and test those configurations. 

## Quick Start

### Prerequisites
- [Docker Desktop](https://docs.docker.com/get-started/get-docker/) - it's free (for you)
- [Mongo Compass](https://www.mongodb.com/try/download/compass) - Optional
  - Playground connection string: ``mongodb://localhost:27017/``

### Run the Configurator Playground
Use the commands below to download this [docker-compose](./docker-compose.yaml) and run the Configurator. 
```bash
mkdir Configurator
cd Configurator
curl https://raw.githubusercontent.com/agile-learning-institute/mongodb_configurator/refs/heads/main/docker-compose.yaml > docker-compose.yaml
docker compose up -d
```
Then visit http://localhost:8082 to access the Configurator. 

### Shutodwn
When you are done playing, you can use this command to shut down the service.
```bash
docker compose down
```

**Note**: Shutting down the service will reset the playground to it's starting state. If you want to save all the files you have created in the playground, see [the API Explorer](http://localhost:8081/docs/index.html) for information on API commands you can use to get the documents. 

If you think you will want to save your configuration, you should create your own configuration repository by creating a new GitHub repo using [this Template](https://github.com/agile-learning-institute/mongodb_configurator_template).

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
