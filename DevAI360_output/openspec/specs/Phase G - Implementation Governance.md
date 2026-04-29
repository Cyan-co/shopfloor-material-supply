# Phase G: Implementation Governance

## 1. Governance Overview

### Governance Objectives
-   **Ensure Compliance:** Guarantee that the final implementation aligns with the architecture defined in Phases A through F.
-   **Prevent Architecture Drift:** Use automated checks to detect and prevent deviations from the architectural design over time.
-   **Maintain Quality:** Enforce coding standards, security practices, and performance requirements.
-   **Enable Controlled Evolution:** Provide a clear process for making intentional changes to the architecture.

## 2. Architecture Compliance Criteria

### 2.1 Code Architecture Compliance

| Criterion | Rule | Verification Method |
|---|---|---|
| **Layer Separation** | The Controller layer must not directly access the Repository layer. All business logic must go through the Service layer. | **Automated:** ArchUnit tests in the CI pipeline. |
| **Package Structure** | All Java packages must follow the `com.bosch.shopfloor.*` convention. | **Automated:** Static analysis (SonarQube). |
| **Dependency Control**| No new third-party libraries can be added without Tech Lead approval. | **Automated:** Dependency checker in the CI pipeline. |

### 2.2 API Compliance

| Criterion | Rule | Verification Method |
|---|---|---|
| **API Contract** | All API endpoints must match the OpenAPI (Swagger) specification defined during development. | **Automated:** Contract testing in the CI pipeline. |
| **Security** | All endpoints, except for a public health check, must require authentication and role-based authorization. | **Automated:** Integration tests that check for HTTP 401/403 responses on unauthenticated access. |

### 2.3 Data Compliance

| Criterion | Rule | Verification Method |
|---|---|---|
| **Schema Changes** | All database schema modifications must be performed via a Flyway migration script. | **Manual:** Code review of pull requests. |
| **Data Access** | No raw SQL queries are permitted without explicit approval. Data access must use the Spring Data JPA repositories. | **Automated:** Static code analysis. |

---

## 3. Automated Enforcement

### 3.1 CI/CD Pipeline Gates
The CI/CD pipeline in GitHub Actions is the primary mechanism for automated governance. A pull request cannot be merged to `main` if any of these gates fail.

| Gate | Stage | Failure Action |
|---|---|---|
| **Unit & Integration Tests**| Build | Block merge. |
| **SonarQube Quality Gate**| Build | Block merge if criteria (e.g., coverage < 80%) are not met. |
| **ArchUnit Tests** | Build | Block merge if architectural rules are violated. |
| **Security Scan (SAST)** | Build | Block merge if new critical vulnerabilities are found. |
| **Contract Tests** | Test | Block deployment to Staging if the API implementation deviates from the contract. |

### 3.2 Quality Gates (SonarQube)

| Metric | Threshold |
|---|---|
| Code Coverage | >= 80% |
| Duplicated Lines | <= 5% |
| Maintainability Rating| 'A' |
| Critical Security Issues| 0 |

---

## 4. Review Processes

### 4.1 Code Review (Pull Requests)
-   **Standard Change:** Requires at least **one** approval from a peer developer.
-   **Changes with DB Migration:** Requires approvals from **one peer developer** and the **Tech Lead**.
-   **Changes to Core Security:** Requires approvals from the **Tech Lead** and a designated **Security Champion**.

### 4.2 Architecture Decision Records (ADRs)
Significant architectural changes must be proposed and documented using a lightweight ADR process. An ADR is required for:
-   Adding a new major dependency (e.g., a new database, a message queue).
-   Changing a core architectural pattern.
-   Deprecating an existing API endpoint.

---

## 5. Exception Handling

Deviations from the defined architecture are strongly discouraged. However, if an exception is required, the following process must be followed:
1.  **Request:** The developer creates an ADR documenting the need for the exception, the proposed alternative, and the trade-offs.
2.  **Review:** The ADR is reviewed by the Tech Lead and the Solution Architect.
3.  **Approval:** If approved, the ADR is merged, and the change can be implemented. The exception will be tracked and reviewed periodically.

---

## 6. Roles and Responsibilities

| Role | Governance Responsibilities |
|---|---|
| **Developer** | Write code that adheres to the architecture. Write tests to prove compliance. |
| **Tech Lead** | Conduct code reviews to enforce standards. Approve database changes and ADRs. |
| **Solution Architect** | Own the architecture documents. Review and approve significant architectural changes (ADRs). |
