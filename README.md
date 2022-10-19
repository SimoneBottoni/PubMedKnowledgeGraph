# PubMedKnowledgeGraph

Manager: https://github.com/SimoneBottoni/PubMedKnowledgeGraph_Manager

Worker: https://github.com/SimoneBottoni/PubMedKnowledgeGraph_Worker

WebUI: https://github.com/SimoneBottoni/pubmedknowledgegraphclient

Docker: https://github.com/SimoneBottoni/PubMedKnowledgeGraph_Docker


## Requirements
- [Java](https://www.oracle.com/java/technologies/downloads/) 11+
- NodeJS and NPM available [here](https://nodejs.org/it/download/).
- [Docker](https://docs.docker.com/get-docker/)
- [Postgres](https://www.postgresql.org) 13+
- RabbitMQ (Docker: [rabbitmq:3.9-management](https://hub.docker.com/_/rabbitmq))

- [MetaMapLite](https://metamap.nlm.nih.gov/maven2/gov/nih/nlm/nls/metamaplite/3.6.2rc4/metamaplite-3.6.2rc4-standalone.jar)
- 2022aa UMLS Data (must be requested [here](https://lhncbc.nlm.nih.gov/ii/tools/MetaMap/run-locally/MetaMapLite.html)). 

---

## Project Configuration

### Init
Run Postgres and RabbitMQ in docker using the [run_all.sh](https://github.com/SimoneBottoni/PubMedKnowledgeGraph_Docker/blob/master/run_all.sh) script of https://github.com/SimoneBottoni/PubMedKnowledgeGraph_Docker.

Alternatively, it is possible to run an instance of Postgres and RabbitMQ locally. Configuration files are available [here](https://github.com/SimoneBottoni/PubMedKnowledgeGraph_Docker/tree/master/config).

### Manager
When the Manager is started for the first time, two configuration files will be generated, config / config.properties and config / db.properties. A template of these files is available [here](https://github.com/SimoneBottoni/PubMedKnowledgeGraph_Manager/tree/master/src/main/resources).

In config.properties, it is possible to set the last PubMed file for the current year in the data.baseline.last.file option, and when the system must annotate the articles (during the analysis or after), setting as true the option annotation.during .parsing.enable or the option annotation.after.parsing.start.

An example of config.properties:
```
# Update time
update.cron=* * 1 * * *

# Annotation time
annotation.cron=* * 1 * * *

# Last BaseLine File
data.baseline.last.file=pubmed22n1114.xml.gz

# Annotation Process
annotation.during.parsing.enable=true
annotation.after.parsing.start=false
```

In the db.properties file is necessary to set the DB URL in the spring.datasource.url option, the username, and the password in spring.datasource.username and spring.datasource.password respectively. The RabbitMQ IP address must be set in the spring.rabbitmq.host option with the port, username, and password in spring.rabbitmq.port, spring.rabbitmq.username and spring.rabbitmq.password respectively.

An example of db.properties:
```
# DB Config
spring.datasource.url = jdbc:postgresql://127.0.0.1:5432/pubmed
spring.datasource.username = postgres
spring.datasource.password = postgres

spring.jpa.hibernate.ddl-auto = update


# Batch
spring.rabbitmq.host=127.0.0.1
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
```

To run the system:
```
mvn clean install package
java -jar target/PubMedKnowledgeGraph_Manager-v2.1.jar
```

### Worker(s)
When a Worker is started locally for the first time (the same step can be used to run multiple workers at the same time - not locally), two configuration files will be generated, config / config.properties and config / db.properties. A template of these files is available [here](https://github.com/SimoneBottoni/PubMedKnowledgeGraph_Worker/tree/master/src/main/resources).

In config.properties, it is necessary to set the current path of 2022aa UMLS extracted data necessary to MetaMapLite.

An example of config.properties:
```
# PubMed Data
data.folder.baseline=PubMed/BaseLine/
data.folder.update=PubMed/Update/

# MetaMapLite
metamaplite.models=metamaplite/data/models
metamaplite.index=metamaplite/data/ivf/2020AA/USAbase
metamaplite.data.specialterms=metamaplite/data/specialterms.txt
metamaplite.config=metamaplite/config/metamaplite.properties
```

In the db.properties file is necessary to set the DB URL in the spring.datasource.url option, the username, and the password in spring.datasource.username and spring.datasource.password respectively. The RabbitMQ IP address must be set in the spring.rabbitmq.host option with the port, username, and password in spring.rabbitmq.port, spring.rabbitmq.username and spring.rabbitmq.password respectively.

An example of db.properties:
```
# DB Config
spring.datasource.url = jdbc:postgresql://127.0.0.1:5432/pubmed
spring.datasource.username = postgres
spring.datasource.password = postgres

spring.jpa.hibernate.ddl-auto = update


# Batch
spring.rabbitmq.host=127.0.0.1
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
```

Add ***MetaMapLite.jar*** in metamaplite/lib folder.

To run the system:
```
mvn clean install package
java -jar target/PubMedKnowledgeGraph_Worker-v2.1.jar
```


### Client
To run the client (it needs the Manager to be active):
```
npm install
npm start
```
