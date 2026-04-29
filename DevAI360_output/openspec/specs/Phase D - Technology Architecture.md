# Phase D: Technology Architecture (TA)

## 1. Infrastructure Overview

### Container Platform
- **Runtime:** Docker for containerizing the Spring Boot backend and the Angular frontend.
- **Orchestration:** Docker Compose will be used for local development and testing environments. For Staging and Production, the application will be deployed to a Kubernetes cluster.
- **Registry:** A private container registry (e.g., GitHub Container Registry, Docker Hub Private, or Bosch internal registry) will be used to store the Docker images.

### Deployment Environments
| Environment | Purpose | Scaling | Resources |
|---|---|---|---|
| **DEV (Local)** | Local development | 1 replica | Minimal (Docker Compose) |
| **TEST** | CI/CD Integration Testing | 1 replica | Minimal (Kubernetes) |
| **STAGING** | Pre-production validation | 2 replicas | Production-like (Kubernetes) |
| **PROD** | Production | 3+ replicas (auto-scaling) | Full (Kubernetes) |

---

## 2. Network Architecture

### Ingress Configuration
- **Load Balancer:** A standard cloud load balancer (e.g., AWS ALB, Azure Load Balancer) will be managed by a Kubernetes Ingress Controller (e.g., NGINX).
- **TLS Termination:** TLS will be terminated at the Ingress Controller. Certificates will be managed using Let's Encrypt with cert-manager.
- **DNS:** A subdomain will be used, e.g., `shopfloor-supply.your-company.com`.

### Service Communication
- **Internal:** Within the Kubernetes cluster, services will communicate using standard Kubernetes DNS.
- **External:** All external traffic will be routed through the Bosch Px Proxy, which will then forward requests to the application's Ingress Controller.

### Network Policies
- By default, all pod-to-pod communication will be denied.
- Explicit `NetworkPolicy` resources will be created to allow traffic from the Ingress Controller to the frontend and backend services, and from the frontend service to the backend service.

---

## 3. Database Infrastructure

### PostgreSQL Configuration
- **Version:** 15+
- **Connection Pooling:** The Spring Boot application will use HikariCP for connection pooling. For high-concurrency scenarios, an external pooler like PgBouncer will be considered.
- **Backup Strategy:** Daily automated snapshots with Point-in-Time Recovery (PITR) enabled, retained for 30 days.
- **Replication:** For the Production environment, a primary-replica setup will be used for high availability.

---

## 4. Security Infrastructure

### Authentication & Authorization
- **Protocol:** The system will use a simple, token-based authentication for the MVP. JWTs (JSON Web Tokens) will be issued upon successful login.
- **Identity Provider:** For the MVP, a simple internal user store will be used. Post-MVP, this will be integrated with an enterprise OIDC provider.
- **Token Storage:** JWTs will be stored in a secure, HttpOnly cookie to mitigate XSS risks.

### Secrets Management
- **Storage:** Kubernetes Secrets will be used to store all sensitive information, such as database credentials, API keys, and JWT signing keys.
- **Access:** Secrets will be mounted into pods as environment variables or files with restricted access.

### TLS Configuration
- **Minimum:** TLS 1.2 will be enforced for all external traffic.
- **Certificates:** Managed by Let's Encrypt.

---

## 5. Monitoring & Observability

### Metrics
- **Tool:** Prometheus will be used to scrape metrics from the Spring Boot backend (via Actuator) and the Kubernetes cluster.
- **Dashboards:** Grafana will be used to visualize key metrics, including request latency, error rates, and JVM performance.

### Logging
- **Tool:** A cluster-level logging solution like the ELK stack (Elasticsearch, Logstash, Kibana) will be used.
- **Log Format:** Logs will be in a structured JSON format to enable easy parsing and querying.

### Alerting
- **Tool:** Prometheus Alertmanager will be used to send alerts to a designated channel (e.g., Slack, email).
- **Key Alerts:**
  - High API error rate (>5% over 5 minutes).
  - High request latency (p99 > 1.5s).
  - Service unavailability.

---

## 6. CI/CD Pipeline

### Pipeline Stages
`Build` -> `Test` -> `Security Scan` -> `Deploy to STAGING` -> `Manual Approval` -> `Deploy to PROD`

### Stage Requirements
| Stage | Actions | Gate |
|---|---|---|
| **Build** | Compile code, run unit tests, build Docker image. | All unit tests must pass. |
| **Test** | Run integration and API tests against a test database. | All integration tests must pass. |
| **Security Scan** | Scan for vulnerabilities in dependencies and container images. | No critical vulnerabilities found. |
| **Deploy STAGING** | Automatically deploy the new version to the Staging environment. | Successful security scan. |
| **Deploy PROD** | A manual approval step is required before deploying to Production. | Successful staging deployment and QA sign-off. |

---

## 7. Disaster Recovery

### Backup Strategy
| Component | Frequency | Retention | Recovery Time Objective (RTO) |
|---|---|---|---|
| **Database** | Daily full backup | 30 days | < 1 hour |
| **Configurations** | Stored in Git (IaC) | Indefinite | < 30 minutes |

### Recovery Procedures
- **Application:** A rollback to a previous stable version can be triggered via the CI/CD pipeline.
- **Database:** The database can be restored from the latest snapshot in case of catastrophic failure.
- **Full Disaster:** The entire infrastructure is defined as code (Kubernetes YAML, Terraform), allowing for a full rebuild in a new environment if necessary. The RTO for a full rebuild is estimated at < 4 hours.
