# Spring Cloud Configuration Server

The **Spring Cloud Configuration Server** provides centralized external configuration management for distributed applications. It retrieves configurations from a **Git-based repository**, enabling applications to dynamically load their properties based on their profile and environment.

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [Clone the Repository](#clone-the-repository)
- [Configuration Management](#configuration-management)
- [Compiling](#compiling)
- [Running the Server](#running-the-server)
- [Accessing Configuration Properties](#accessing-configuration-properties)
- [Refreshing Configurations](#refreshing-configurations)
- [Logging and Monitoring](#logging-and-monitoring)
- [Error Handling](#error-handling)
- [License](#license)

---

## Overview

The **Spring Cloud Configuration Server** acts as a single source of truth for application properties, managing configurations across multiple environments (development, testing, production). It enables dynamic updates and secure management of sensitive configurations.

---

## Architecture

The configuration server fetches and serves configurations to microservices based on their application name, environment profile, and an optional Git branch or label. The microservices retrieve their properties via REST API calls.

```
+-----------------+       +-------------------------------+
|  Microservice  | <-->  |  Spring Cloud Config Server   |
+-----------------+       +-------------------------------+
                                  |
                                  v
                  +---------------------------------+
                  |  Git-Based Configuration Repo  |
                  +---------------------------------+
```

### Git Repository for Configuration Files

This configuration server retrieves properties from the following Git repository:

**Configuration Repository:** [https://github.com/Matt-Hays/configuration-repository.git](https://github.com/Matt-Hays/configuration-repository.git)

---

## Features

- **Centralized Configuration Management**
- **Environment-Specific Configurations**
- **Profile-Based Settings** (e.g., `dev`, `prod`)
- **Git-Based Version Control** for configurations
- **Dynamic Refreshing of Configurations**
- **Secure Property Handling**

---

## Clone the Repository

Since this repository contains multiple submodules, it should be cloned using the following command:

```bash
git clone https://github.com/Matt-Hays/configuration-server.git
cd configuration-server
```

---

## Configuration Management

The `application.yml` file contains the necessary configurations to connect to the external Git repository. By default, the application fetches configuration files from:

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/Matt-Hays/configuration-repository.git
```

### Application Profiles and Labels

Each microservice retrieves its configuration using:

```
http://{config-server-url}/{application}/{profile}/{label}
```

For example, to fetch configurations for the **inventory-service** in the `dev` profile:

```
http://localhost:8888/inventory-service/dev
```

---

## Compiling

Use Maven to build the configuration server:

```bash
mvn clean package
```

For a faster build, skipping tests:

```bash
mvn clean package -DskipTests
```

---

## Running the Server

### Using Maven

Run the configuration server using:

```bash
mvn spring-boot:run
```

By default, the server runs on **port 8888**.

### Using Docker

To build and run as a Docker container:

```bash
mvn clean spring-boot:build-image -DskipTests
```

```bash
docker run -p 8888:8888 docker.io/library/configuration-server:0.0.1-SNAPSHOT
```

---

## Accessing Configuration Properties

Microservices fetch their configurations dynamically using the following request pattern:

```bash
http://localhost:8888/{application}/{profile}
```

Example:

```bash
http://localhost:8888/loyalty-service/prod
```

---

## Refreshing Configurations

To enable dynamic configuration refreshing, ensure each microservice includes **Spring Boot Actuator** with the `refresh` endpoint enabled:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: refresh
```

Then, trigger a refresh using:

```bash
curl -X POST http://localhost:8080/actuator/refresh
```

---

## Logging and Monitoring

To debug configuration retrieval, set logging levels in `application.yml`:

```yaml
logging:
  level:
    org.springframework.cloud.config: DEBUG
```

To view logs in Docker:

```bash
docker logs -f <container_id>
```

---

## Error Handling

Common issues include:

- **Incorrect Git Repository URL:** Verify the `spring.cloud.config.server.git.uri` property.
- **Invalid Configuration Profile:** Ensure the requested profile exists in the repository.
- **Connection Issues:** Check network accessibility between the config server and repository.

---

## License

This project is open-source and available under the [MIT License](LICENSE).
