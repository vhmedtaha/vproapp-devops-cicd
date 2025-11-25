
# vproapp-devops-cicd

[![Build Status](https://img.shields.io/badge/build-automated-blue.svg)](#) [![License](https://img.shields.io/badge/license-UNLICENSED-red.svg)](#)

Production-grade reference showing how to build, containerize, test, and deploy a Spring-based Java web application with Docker, Jenkins CI/CD, RabbitMQ, Elasticsearch/Memcached integrations, and Kubernetes running on your own three-node EC2 cluster.

---

## Table of Contents

1. [Project Highlights](#project-highlights)
2. [Repository Map](#repository-map)
3. [Stack Overview](#stack-overview)
4. [Prerequisites](#prerequisites)
5. [Local Build & Test](#local-build--test)
6. [Docker & Compose Workflow](#docker--compose-workflow)
7. [CI/CD with Jenkins](#cicd-with-jenkins)
8. [AWS Deployment (Self-Managed Kubernetes on EC2)](#aws-deployment-self-managed-kubernetes-on-ec2)
9. [Configuration & Secrets](#configuration--secrets)
10. [Testing](#testing)
11. [Troubleshooting](#troubleshooting)
12. [Next Steps](#next-steps)
13. [Maintainers & Contact](#maintainers--contact)

---

## Project Highlights

- **Full-stack sample** combining Java web app code, Spring MVC/Security configuration, JSP views, and RabbitMQ/Memcached/Elasticsearch utilities.
- **Container-first** with dedicated Dockerfiles for the app, database, and web tier + `docker-compose.yml` for quick spins.
- **Infrastructure-as-code** manifests covering RabbitMQ, database, memcached, and app deployments (`*dep.yml`, `*-CIP.yml`).
- **CI/CD ready** via a declarative `Jenkinsfile` that builds, tests, containerizes, pushes to AWS ECR, and deploys to Kubernetes.
- **Realistic AWS story**: push images to ECR and deploy to a kubeadm-managed three-node EC2 Kubernetes cluster.

## Repository Map

| Path | Description |
| --- | --- |
| `pom.xml` | Maven project descriptor, dependencies, plugins. |
| `src/main/java/com/visualpathit/account/...` | Controllers, services, repositories, validators, and utility classes for the core app. |
| `src/main/resources/` | Application properties, validation bundles, logback config, DB seed SQL. |
| `src/main/webapp/WEB-INF/` | Spring XML configs (`appconfig-*`), JSP views (`views/*.jsp`), `web.xml`. |
| `src/main/webapp/resources/` | Static assets (CSS, JS, images). |
| `src/test/java/` | Unit and MVC tests (controllers, models, test view resolver). |
| `Docker-files/app/Dockerfile` | Builds the Java web app image. |
| `Docker-files/web/Dockerfile` & `nginvproapp.conf` | Nginx front-end image and config. |
| `Docker-files/db/Dockerfile`, `db_backup.sql` | Database container and seed data. |
| `docker-compose.yml` | Local multi-container orchestration: app, db, RabbitMQ, web tier. |
| `Jenkinsfile` | Declarative pipeline for build → image → push → deploy. |
| `app-secret.yml` | Sample secret manifest (placeholder values). |
| `db-CIP.yml`, `mc-CIP.yml`, `mcdep.yml`, `rmq-CIP-service.yml`, `rmq-dep.yml`, `vproapp-service.yml`, `vproappdep.yml`, `vproappdep.yml`, `vprodbdep.yml`, `vproapp-service.yml` | Kubernetes manifests for RabbitMQ, DB, Memcached, app services/deployments. |
| `mcdep.yml`, `mc-CIP.yml` | Memcached deployment & service definitions. |
| `Docker-files/d.txt` | Notes placeholder for Docker builds. |
| `README.md` | (This file) complete documentation.

> Tip: The Kubernetes YAML naming convention in this project uses `-dep` for deployments and `-CIP` for ClusterIP services.

## Stack Overview

- **Application**: Spring MVC/Security, JSP views, RabbitMQ producers/consumers, Memcached + Elasticsearch utilities.
- **Infrastructure**: Docker images (app/web/db), Docker Compose, Kubernetes manifests, AWS ECR registry, three-node EC2 Kubernetes cluster (kubeadm + Calico CNI).
- **CI/CD**: Jenkins pipeline orchestrating Maven build/test, Docker, AWS ECR login/push, and `kubectl apply` deployments.

## Prerequisites

- Java JDK 11+ and Maven 3.6+
- Docker Engine & Docker Compose
- Git
- AWS CLI configured with credentials that can create/use ECR and manage EC2 instances
- `kubectl`, `kubeadm`, and optionally `eksctl`/`kops` depending on your provisioning preference
- Jenkins (or alternative CI) with Docker + kubectl capability

## Local Build & Test

```powershell
# install deps
mvn clean install

# run unit tests
mvn test

# package WAR without tests (optional)

```

Use embedded Spring MVC test utilities under `src/test/java/com/visualpathit/account` for controller and model validation.


mvn clean package -DskipTests
```
```powershell
# build application image
docker build -t vproapp:local -f Docker-files/app/Dockerfile .

# run everything locally
```powershell
# build application image
docker build -t vproapp:local -f Docker-files/app/Dockerfile .

# run everything locally
Pipeline stages (see `Jenkinsfile`):

# watch logs


# stop & clean
1. **Checkout** source code.
```
2. **Build & test** using Maven.
3. **Docker build** for app/web images.
4. **AWS ECR push** using `aws ecr get-login-password` for auth.
5. **Deploy** via `kubectl apply -f <manifest>` targeting the EC2-based cluster.

Important Jenkins setup:

- Credentials: AWS access key/secret, Docker registry (if using non-ECR), kubeconfig secret or SSH key for control-plane access.
- Tools: `docker`, `kubectl`, `awscli`, and optionally `helm` installed on agents.
- Optional: configure Jenkins shared library for reusable deploy logic.

Example deploy stage:

```groovy
stage('Deploy') {
  steps {
    sh 'kubectl apply -f vproappdep.yml'
    sh 'kubectl apply -f vproapp-service.yml'
    sh 'kubectl rollout status deployment/vproapp'
  }
}
```

## AWS Deployment (Self-Managed Kubernetes on EC2)

This project assumes a three-node Kubernetes cluster (one control-plane, two workers) that you manage on EC2 using `kubeadm`.

1. **Provision EC2 instances** (Ubuntu 22.04 or Amazon Linux 2), open ports 22/6443/nodeports, attach IAM roles for ECR pulls.
2. **Install container runtime + kubeadm/kubelet/kubectl** on all nodes, disable swap.
3. **Initialize control plane**:

```powershell
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

4. **Install Calico CNI**:

```powershell
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

5. **Join workers** using the `kubeadm join ...` command output in step 3.
6. **Verify** `kubectl get nodes` shows all three nodes ready.
7. **Ensure image pulls**:
   - Attach IAM roles with `AmazonEC2ContainerRegistryReadOnly`.
   - or run `aws ecr get-login-password ... | docker login ...` on each node.
   - or create Kubernetes `imagePullSecrets` from ECR credentials.
8. **Deploy manifests**:

```powershell
kubectl apply -f db-CIP.yml
kubectl apply -f vprodbdep.yml
kubectl apply -f rmq-CIP-service.yml
kubectl apply -f rmq-dep.yml
kubectl apply -f mc-CIP.yml
kubectl apply -f mcdep.yml
kubectl apply -f vproapp-service.yml
kubectl apply -f vproappdep.yml
```

9. **Monitor rollout**:

```powershell
kubectl rollout status deployment/vproapp
kubectl get pods -o wide
kubectl logs deployment/vproapp -c vproapp
```

10. **Expose externally** by adding an ingress controller, ALB, or ELB pointing to `vproapp-service.yml` (ClusterIP) or create a LoadBalancer service.

## Configuration & Secrets

- `application.properties` controls JDBC, messaging, and security parameters.
- `app-secret.yml` serves as a template for Kubernetes secrets (replace placeholders before applying).
- Database credentials/connection strings appear in compose and deployment manifests — move them to secrets or parameter stores for production.
- Environment variable hints:

```text
SPRING_PROFILES_ACTIVE=prod
DB_HOST=vprodb
DB_USERNAME=vpro
DB_PASSWORD=changeMe
RABBITMQ_HOST=rmq
MEMCACHED_HOST=mc
ELASTICSEARCH_HOST=es
```

## Testing

- Unit tests: `mvn test`
- Controller tests: `src/test/java/com/visualpathit/account/controllerTest/...`
- Model tests: `src/test/java/com/visualpathit/account/modelTest/...`
- Add integration tests that run against docker-compose by leveraging Maven profiles.

## Troubleshooting

- `ImagePullBackOff`: verify ECR URI, IAM permissions, or check `imagePullSecrets` configuration.
- `CrashLoopBackOff`: inspect `kubectl logs` for stack traces, confirm DB/RabbitMQ endpoints.
- `Pending` pods: ensure Calico is installed and nodes have enough resources.
- Jenkins failures: re-check AWS credential binding and Docker daemon availability on agents.

## Next Steps

1. Add architecture diagram (`docs/architecture.png`) and link it here.
2. Introduce Helm charts for parameterized deployments.
3. Enable automated integration tests inside the Jenkins pipeline.
4. Configure monitoring (Prometheus/Grafana) and logging (ELK) for the cluster.

## Maintainers & Contact

- Owner: `vhmedtaha`
- Contributions: open issues or PRs with clear descriptions and test evidence.
- For questions: use repository issues or reach out via LinkedIn/GitHub.

---

_This README covers every major component in the repository—application code, Docker artifacts, Kubernetes manifests, CI/CD pipeline, and deployment process—so you can understand, run, and extend the project with confidence._

