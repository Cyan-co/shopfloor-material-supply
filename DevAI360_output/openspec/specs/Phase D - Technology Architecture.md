# Phase D: Technology Architecture (TA)

## 1. Infrastructure Overview

This document specifies the infrastructure and operational architecture for the Shopfloor Material Supply System. All infrastructure should be managed as code (IaC).

### Container Platform
-   **Runtime:** Docker will be used to containerize the Spring Boot backend and the Angular frontend applications.
-   **Orchestration:**
    -   **Local/Dev:** `docker-compose` will be used for local development environments.
    -   **Staging/Prod:** Kubernetes is the target orchestration platform for automated deployment, scaling, and management.
-   **Registry:** All Docker images will be stored in a private container registry (e.g., GitHub Container Registry, Artifactory).

### Deployment Environments

| Environment | Purpose | Scaling | Notes |
|---|---|---|---|
| **DEV** | Local developer machines | 1 replica per service | Uses `docker-compose`. Connects to a shared dev database. |
| **TEST** | Automated integration & E2E testing | 1 replica per service | Deployed via CI/CD. Resets database on each run. |
| **STAGING** | Pre-production user acceptance testing (UAT)| 2 replicas per service | Mirrors production configuration. Uses a production-like database. |
| **PROD** | Live production environment | 3+ replicas (auto-scaled) | Highly available, monitored, and backed up. |

---

## 2. Network Architecture

### Ingress Configuration
-   **API Gateway:** All incoming traffic from end-users MUST pass through the **Bosch Px Proxy**.
-   **TLS Termination:** The Px Proxy is responsible for TLS termination. All traffic within the cluster can be unencrypted, but mTLS is recommended for future high-security needs.
-   **DNS:** The application will be accessible via a standard DNS name, e.g., `shopfloor-supply.bosch.com`.

### Service Communication
-   The Angular frontend, running in the user's browser, communicates with the backend via the public-facing API Gateway.
-   Internal communication between any future backend microservices will be handled via Kubernetes service discovery (e.g., `http://order-service:8080`).

---

## 3. Database Infrastructure

### PostgreSQL Configuration
-   **Version:** 15+
-   **Deployment:** Deployed as a stateful set in Kubernetes or consumed as a managed service (e.g., AWS RDS).
-   **Backup Strategy:** Daily automated snapshots with Point-in-Time Recovery (PITR) enabled. Backups must be retained for at least 30 days.
-   **Replication:** A primary-replica setup is required for the PROD environment to ensure high availability.

---

## 4. Security Infrastructure

### Authentication & Authorization
-   **Protocol:** OAuth 2.0 / OpenID Connect (OIDC) will be the standard for authentication.
-   **Identity Provider (IdP):** An existing enterprise IdP should be used. For the MVP, a simple in-app user store is acceptable as defined in the Data Architecture, but the API must be secured with JWTs.
-   **Token Format:** JSON Web Tokens (JWT). The backend will validate the JWT signature and claims on every request.

### Secrets Management
-   **Storage:** All secrets (database passwords, API keys, etc.) must be stored in Kubernetes Secrets. They will be mounted into application pods as environment variables or files.
-   **Rule:** Secrets must never be stored in Git or baked into Docker images.

---

## 5. Monitoring & Observability

### Metrics
-   **Tool:** Prometheus will be used to scrape metrics from the Spring Boot backend (via Actuator) and the Kubernetes cluster.
-   **Dashboards:** Grafana will be used to visualize key metrics, including request latency (p99), error rates (HTTP 5xx), and resource utilization.

### Logging
-   **Tool:** The ELK Stack (Elasticsearch, Logstash, Kibana) or Loki.
-   **Log Format:** All applications must log in a structured **JSON** format to stdout. Logs must include a timestamp, log level, service name, and a trace ID for correlation.
-   **Retention:** Logs will be retained for a minimum of 30 days.

### Alerting
-   **Tool:** Alertmanager (part of the Prometheus ecosystem).
-   **Critical Alerts:**
    -   API error rate > 5% over a 5-minute period.
    -   p99 request latency > 2 seconds.
    -   Service is down (no running pods).

---

## 6. CI/CD Pipeline

### Tool
-   A GitHub Actions workflow will be created to automate the build, test, and deployment process.

### Pipeline Stages
1.  **Build:** Compile Java code, run Maven/Gradle build.
2.  **Unit Test:** Run all JUnit tests.
3.  **Code Quality Scan:** Integrate with SonarQube to fail the build on critical quality issues.
4.  **Build Image:** Build and push Docker images to the container registry.
5.  **Deploy to TEST:** Automatically deploy to the TEST environment and run integration tests.
6.  **Deploy to STAGING:** If TEST passes, automatically deploy to STAGING.
7.  **Deploy to PROD:** This step requires **manual approval** after successful UAT in STAGING.

---

## 7. Disaster Recovery

-   **RTO (Recovery Time Objective):** 4 hours.
-   **RPO (Recovery Point Objective):** 15 minutes.
-   **Strategy:** In case of a full cluster failure, the recovery strategy is to redeploy the entire application from code and Docker images using the IaC scripts. The database will be restored from the latest successful backup (PITR).
