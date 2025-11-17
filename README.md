
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

**Next Steps**
- **Verify:**: Run `mvn test` and `docker-compose up -d --build` to validate local startup.
- **CI:**: Configure Jenkins credentials, agents, and environment variables to enable automated runs.

---
Generated and updated by repository maintainers to reflect the current project structure and tooling.

