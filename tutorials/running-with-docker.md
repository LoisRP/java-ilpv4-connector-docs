---
description: Running with Docker
---

# Run with Docker

## Prerequisites

* Docker must be installed and running.

## Start a connector \(using in-memory database and in-memory Redis\)

The connector requires a SQL database and Redis to run. By default, the docker image will run using an in-memory H2 database and in-memory Redis cache. This mode is intended to quickly get a connector up and running, but all data will be lost on container restarts. As such, this mode should not be used for any real world applications.

To start the latest nightly image of the java-ilpv4-connector in an interactive terminal, run:

```text
docker run -p 8080:8080 -it interledger4j/java-ilpv4-connector:nightly
```

You should see a log message like the following once the container has successfully started:

```text
Started ConnectorApplication in 6.912 seconds
```

You can now send HTTP requests to the connector to verify it's working:

```text
curl  http://localhost:8080/accounts -H 'Authorization: Basic YWRtaW46cGFzc3dvcmQ='
```

## Start a connector \(using external Postgres database and external Redis\)

This mode requires having a Postgres database and Redis cache already running. This mode allows the java-ilpv4-connector to be restarted without loss of data \(so long as the Postgres database and Redis cache are not lost\).

### Run Postgres and Redis in Docker

If you already have a Postgres database and Redis cache running, skip this section. Otherwise, you can start up a Postgres container and Redis container in Docker.

To start a Postgres database, run:

```text
docker run -d -p 5432:5432 postgres
```

To start a Redis cache, run:

```text
docker run -d -p 6379:6379 redis
```

You can verify the containers are running via:

```text
docker ps
```

### Configure java-ilpv4-connector to connect to external Postgres and Redis instances:

The docker container needs to be configured with the postgres and redis connection details. This is done by passing environment variables in the docker run command. Docker supports passing each environment variable as a separate argument, or by providing a env file that contains environment variable mappings.

For example, here is how to configure and run the connector using separate env args:

```text
docker run -p 8080:8080 -e spring.profiles.active=migrate -e redis.host=host.docker.internal -e spring.datasource.url=jdbc:postgresql://host.docker.internal:5432/ -e spring.datasource.username=postgres -e spring.datasource.password=postgres -it interledger4j/java-ilpv4-connector:nightly
```

Or more conveniently, we can put all the same args into a file named `connector.env` with the following contents:

```text
spring.profiles.active=migrate
redis.host=host.docker.internal
spring.datasource.url=jdbc:postgresql://host.docker.internal:5432/
spring.datasource.username=postgres
spring.datasource.password=postgres
```

and start the connector via:

```text
docker run -p 8080:8080 --env-file ~/connector.env -it interledger4j/java-ilpv4-connector:nightly
```

## Configuration flags available via docker

java-ilpv4-connector uses [Spring Boot for external configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config). Spring properties specific to the java-ilpv4-connector can be found under [Connector Configuration Properties](../operating-a-connector/configuration.md). Spring makes it easy to set/override a Spring property via environment variables. For example, you can specify a Spring property via yaml configuration like this:

```text
interledger:
    connector:
        nodeIlpAddress: test.xpring-dev.java1
```

Or, you can set the property in an env file to docker like this:

```text
interledger.connector.nodeIlpAddress=test.xpring-dev.java1
```

For Spring properties that use a yaml list like this:

```text
interledger:
  globalRoutingSettings:
      staticRoutes:
        - targetPrefix: test.connie.alice
          peerAccountId: alice
```

The equivalent env-file override is:

```text
interledger.connector.globalRoutingSettings.staticRoutes.0.targetPrefix=test.connie.alice
interledger.connector.globalRoutingSettings.staticRoutes.0.peerAccountId=alice
```
