
**Project Overview**
- **Name:**: vproapp-devops-cicd — a Java Spring-based web application with containerized infrastructure and CI/CD.
- **Purpose:**: Demonstrates a production-like setup including application code, Docker images, orchestration, and CI pipeline configuration used for building, testing and deploying the service.

**Architecture**
- **Application:**: Java web application (Spring MVC / Spring components) packaged with `mvn` and deployed as Docker containers.
- **Supporting services:**: RabbitMQ, Elasticsearch, Memcached, and a backing database (SQL) are included in the repository via Docker tooling and configuration.
- **Orchestration:**: Docker Compose configuration and per-service Dockerfiles are provided to run the system locally or in CI.

**Tools & Technologies**
- **Language & Build:**: Java (Maven) — `pom.xml` coordinates dependencies and build lifecycle.
- **Frameworks:**: Spring (MVC, Security, RabbitMQ integration) — configured via XML under `src/main/webapp/WEB-INF`.
- **Messaging:**: RabbitMQ — utilities and configuration present (`appconfig-rabbitmq.xml`, `RabbitMqUtil.java`).
- **Search:**: Elasticsearch — client utils and controller endpoints exist (`ElasticsearchUtil.java`, `ElasticSearchController.java`).
- **Caching:**: Memcached utilities (`MemcachedUtils.java`).
- **Containerization:**: Dockerfiles under `Docker-files/` and `Docker-files/app`, `Docker-files/db`, `Docker-files/web` plus `docker-compose.yml` and multiple deployment yml files.
- **CI/CD:**: Jenkins pipeline file (`Jenkinsfile`) is included for automated builds and deployments.
- **Orchestration / Compose:**: `docker-compose.yml` and environment-specific compose files (e.g., `vproapp-service.yml`, `rmq-CIP-service.yml`).
- **Version Control & Repo:**: Git repository; follow branching and PR workflow used by your org.

**Repository Layout**
- **Top-level:**: `pom.xml`, `docker-compose.yml`, `Jenkinsfile`, various `*-CIP.yml` and `*-dep.yml` files.
- **Docker files:**: `Docker-files/` — image definitions for app, db, web.
- **Source:**: `src/main/java/...` — Java sources organized under `com.visualpathit.account` (controllers, models, repository, services, utils, validator).
- **Resources & Config:**: `src/main/resources/` and `src/main/webapp/WEB-INF/` contain application properties and XML Spring configurations.
- **Tests:**: `src/test/java/` contains unit and controller tests.

**Prerequisites**
- **Install:**: `Java` (JDK), `Maven`, `Docker` and `docker-compose`, `Git`, and a Jenkins instance for CI use. Ensure Docker Desktop is running if using Docker on Windows.

**Build & Run (locally)**
- **Build:**: `mvn clean package` — compiles and packages the application.
- **Run unit tests:**: `mvn test`.
- **Run with Docker Compose:**: `docker-compose up -d --build` — builds images and starts containers defined in `docker-compose.yml`.
- **Stop and remove containers:**: `docker-compose down`.

**Docker / Images**
- **Build image (app):**: `docker build -t vproapp:local -f Docker-files/app/Dockerfile .` (adjust name/tag as desired).
- **Compose files:**: Use provided compose manifests to bring up full stacks or subsets for CI/deployment scenarios.

**CI/CD (Jenkins)**
- **Pipeline:**: `Jenkinsfile` contains the pipeline definition used to build, test, and publish artifacts or images. Adjust credentials and agents to match your Jenkins environment.

**Testing**
- **Unit & Integration:**: Test classes exist under `src/test/java`. Use `mvn -DskipTests=false test` to run them locally.

**Important Files (quick reference)**
- **`pom.xml`**: Maven build and dependency management.
- **`Jenkinsfile`**: CI pipeline definition.
- **`docker-compose.yml`**: Local orchestration for multi-container runs.
- **`Docker-files/`**: Build context and Dockerfiles for app, db and web images.
- **`src/main/webapp/WEB-INF/*.xml`**: Spring configuration files (MVC, security, RabbitMQ, root context).

**How to contribute / extend**
- **Code changes:**: Create a feature branch, write unit tests, update `pom.xml` only when necessary, and open a pull request for review.
- **Add services:**: Add or modify `docker-compose` service definitions and Dockerfiles under `Docker-files/` for new components.
- **CI updates:**: Update `Jenkinsfile` to add new stages or publish steps consistent with team policies.

**Operational Notes**
- **Secrets:**: Keep secrets out of the repo. Example files like `app-secret.yml` are present; replace with a secret store for production (Vault, Kubernetes Secrets, etc.).
- **Backups:**: Database backup SQL exists at `Docker-files/db/db_backup.sql` and `src/main/resources/db_backup.sql`.

**Contact**
- **Maintainers:**: Use repository issues and PRs for discussion; add maintainers' contact details here as appropriate.
# vproapp-devops-cicd

[![Build Status](https://img.shields.io/badge/build-unknown-lightgrey.svg)](https://example-ci.example.com)
[![License](https://img.shields.io/badge/license-UNLICENSED-red.svg)](LICENSE)

Production-like Java web application example with containerized infrastructure, orchestration manifests, and CI/CD pipeline configuration.

## Key Features
- Java Spring MVC application with modular services and utilities.
- Messaging with RabbitMQ; search with Elasticsearch; caching with Memcached.
- Dockerized services and `docker-compose` for local and CI integration testing.
- Jenkins pipeline for automated build/test/image workflows.

## Quick Start

Prerequisites:
- Java JDK 11+ (or team's chosen JDK)
- Maven 3.6+
- Docker & Docker Compose
- Git

Build and run locally:

```powershell
mvn clean package
mvn test
docker-compose up -d --build
```

Stop containers:

```powershell
docker-compose down
```

## Recommended Workflow
- Create a feature branch: `git checkout -b feat/your-feature`.
- Implement code and add tests under `src/test/java`.
- Run `mvn test` locally.
- Build Docker images and verify with `docker-compose up`.
- Push branch and open a pull request for review.

## Repository Layout (high level)
- `pom.xml` — Maven build and dependencies
- `Jenkinsfile` — CI pipeline definition
- `docker-compose.yml` — Local orchestration
- `Docker-files/` — Docker build contexts for `app`, `db`, `web`
- `src/main/java/com/visualpathit/account` — application code (controllers, services, repos, utils)
- `src/main/resources` & `src/main/webapp/WEB-INF` — configuration and properties

## Environment & Configuration
- Keep secrets out of source. Use `app-secret.yml` only as an example; replace with your secret store.
- Primary application configuration: `src/main/resources/application.properties`.
- DB backup: `Docker-files/db/db_backup.sql` and `src/main/resources/db_backup.sql`.

Common environment variables (examples):

```text
DB_HOST=postgres
DB_PORT=5432
RABBITMQ_HOST=rabbitmq
ELASTICSEARCH_HOST=elasticsearch
MEMCACHED_HOST=memcached
SPRING_PROFILES_ACTIVE=dev
```

## Docker & Compose

Build single image (app):

```powershell
docker build -t vproapp:local -f Docker-files/app/Dockerfile .
```

Use compose to start full stack:

```powershell
docker-compose up -d --build
```

## CI/CD (Jenkins)

`Jenkinsfile` in the repo contains the pipeline used for build/test/image publish. Typical Jenkins stages:

- Checkout
- Build (`mvn clean package`)
- Unit tests (`mvn test`)
- Build Docker images
- Publish images to registry (requires credentials)

Example pipeline snippet:

```groovy
stage('Build') {
	steps {
		sh 'mvn -B -DskipTests=false clean package'
	}
}
```

Adjust shell command to Windows agents if necessary (PowerShell) and configure credentials in Jenkins.

## Testing
- Unit tests: `mvn test`
- Add integration tests that start the compose stack with test profiles and run against the services.

## Troubleshooting
- If Docker services fail to start, inspect logs: `docker-compose logs -f`.
- Common problems: port conflicts, insufficient memory for Docker Desktop on Windows, missing credentials for private registries.

## Contributing
- Create a branch, add tests, run `mvn test`, open a PR and request review.
- Keep `pom.xml` changes minimal and documented in the PR description.

## Maintainers
- Use repository issues and PRs to contact maintainers. Add maintainers list or team contacts here.

## Next Steps (suggested)
- Run `mvn test` and `docker-compose up -d --build` locally to validate the stack.
- Configure Jenkins credentials and agents to run the `Jenkinsfile` pipeline.
- Optionally add automated integration tests and a health-check endpoint for readiness probes.

---
_This README was refined to provide clearer onboarding, commands and next actions for contributors and operators._

