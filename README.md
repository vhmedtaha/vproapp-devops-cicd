
<!-- README: polished to be actionable for developers and operators -->

# vproapp-devops-cicd

[![Build Status](https://img.shields.io/badge/build-unknown-lightgrey.svg)](https://example-ci.example.com) [![License](https://img.shields.io/badge/license-UNLICENSED-red.svg)](LICENSE)

Production-like Java web application example with containerized infrastructure, orchestration manifests, and CI/CD pipeline configuration.

## Table of Contents
- [Overview](#overview)
- [Quick Start](#quick-start)
- [Development Workflow](#development-workflow)
- [Repository Layout](#repository-layout)
- [Configuration & Environment](#configuration--environment)
- [Docker & Compose](#docker--compose)
- [CI/CD (Jenkins)](#cicd-jenkins)
- [Testing](#testing)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [Maintainers & Contact](#maintainers--contact)

## Overview

- **Name:** `vproapp-devops-cicd`
- **Stack:** Java (Maven), Spring (MVC, Security), RabbitMQ, Elasticsearch, Memcached, Docker, Jenkins
- **Purpose:** Reference repo showing how to build/test/package a Spring web app, run supporting services in containers, and stitch everything together with CI/CD.

## Quick Start

Prerequisites:
- Java JDK 11+
- Maven 3.6+
- Docker Desktop (with Docker Compose)
- Git

Build the project and run unit tests:

```powershell
mvn clean package
mvn test
```

Start the full stack with Docker Compose:

```powershell
docker-compose up -d --build
```

Check logs:

```powershell
docker-compose logs -f
```

Stop and remove containers:

```powershell
docker-compose down
```

Notes:
- The repository includes sample compose manifests and service-specific deployment YAML files. Use them as a starting point and customize environment variables and resource limits for your environment.

## Development Workflow

- Create a feature branch: `git checkout -b feat/your-feature`
- Implement code under `src/main/java` and add tests to `src/test/java`
- Run `mvn test` locally and fix issues
- Build Docker image(s) and validate using `docker-compose`
- Push and open a PR for review

Good commit flow:

```text
git add .
git commit -m "feat: short description of change"
git push origin feat/your-feature
```

## Repository Layout

- `pom.xml` — Maven build and dependencies
- `Jenkinsfile` — CI pipeline definition
- `docker-compose.yml` — Default local orchestration
- `Docker-files/` — Docker build contexts for `app`, `db`, `web`
- `src/main/java/com/visualpathit/account` — application code (controllers, services, repos, utils)
- `src/main/resources` & `src/main/webapp/WEB-INF` — application properties and Spring XML configs
- `src/test/java` — unit and integration tests

## Configuration & Environment

- Primary app config: `src/main/resources/application.properties`
- Example secrets: `app-secret.yml` (do not store real secrets in repo)
- DB backup files: `Docker-files/db/db_backup.sql` and `src/main/resources/db_backup.sql`

Common environment variables (examples):

```text
DB_HOST=postgres
DB_PORT=5432
RABBITMQ_HOST=rabbitmq
ELASTICSEARCH_HOST=elasticsearch
MEMCACHED_HOST=memcached
SPRING_PROFILES_ACTIVE=dev
```

Tip: On Windows use PowerShell to export env vars for compose or use a `.env` file referenced by `docker-compose.yml`.

## Docker & Compose

Build the application image locally:

```powershell
docker build -t vproapp:local -f Docker-files/app/Dockerfile .
```

Start the whole stack (recommended for local integration testing):

```powershell
docker-compose up -d --build
```

Start a single service (example: only app):

```powershell
docker-compose up -d --build app
```

Useful commands:

```powershell
docker ps
docker-compose logs -f <service-name>
docker-compose down --volumes
```

Ports (typical examples):

- Application: `8080`
- RabbitMQ management: `15672`
- Elasticsearch: `9200`
- Memcached: `11211`

Adjust ports in compose files when they conflict with local services.

## CI/CD (Jenkins)

The included `Jenkinsfile` demonstrates a pipeline for building, testing and packaging the application and building Docker images. Typical stages:

- Checkout
- Build (`mvn -B -DskipTests=false clean package`)
- Unit tests (`mvn test`)
- Build Docker images
- Publish images to a registry (requires credentials)

Example Windows-aware stage (PowerShell agent) for Jenkins:

```groovy
stage('Build (Windows)') {
  steps {
    powershell 'mvn -B -DskipTests=false clean package'
  }
}
```

Configure Jenkins with credentials for your Docker registry and proper build agents (Linux or Windows) depending on your chosen image base.

## Testing

- Unit tests: `mvn test`
- Integration tests: add tests that start the compose stack (using Testcontainers or remote compose) and run against endpoints

Recommended: add a lightweight health-check endpoint and include a smoke-test stage in CI that verifies the app responds on `GET /health`.

## Troubleshooting

- If containers fail to start, run: `docker-compose logs -f` and inspect the failing service logs
- If Maven build fails, re-run with `-X` to enable debug output: `mvn -X clean package`
- On Windows, ensure Docker Desktop has enough memory/CPU and that WSL2 integration is enabled (if using WSL2)
- Port conflicts: change host ports in `docker-compose.yml` or stop the conflicting service

## Contributing

- Fork or branch from `main` and open a PR
- Add tests, keep changes small and well-documented
- Update documentation and `pom.xml` changes must include rationale

## Maintainers & Contact

- Use repository issues and pull requests for discussion. Provide team contact details or Slack channel here.

## Next Steps (suggested)

- Run `mvn test` and `docker-compose up -d --build` locally to validate the stack
- Configure Jenkins with registry credentials and test pipeline runs
- Add automated integration tests and a simple `/health` endpoint for CI smoke tests

---
_This README is tuned for clearer onboarding, reproducible local testing, and faster CI integration. If you'd like, I can:_

- run `mvn test` here and report failures
- create a Git commit and push a branch with this README update
- add real CI badge links and a maintainers block (provide URLs/contacts)


