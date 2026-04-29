# Phase E: Opportunities and Solutions

## 1. Gap Analysis Summary

### Current State
The current process for shopfloor material supply is entirely manual, relying on verbal communication and paper tracking. There is no existing digital system. This project is **Greenfield**.

### Target State
The target state is a fully-digital, real-time web application as defined in the **Phase B (Business)**, **Phase C (Application & Data)**, and **Phase D (Technology)** architecture documents.

### Gap Summary Table

| # | Gap Area | Current State | Target State | Priority |
|---|---|---|---|---|
| 1 | Core Process Automation | Manual, paper-based process | A web application with a defined state machine for order lifecycle. | High |
| 2 | Data Management | No centralized data | A PostgreSQL database serving as the single source of truth for all orders. | High |
| 3 | User Interfaces | No user interface | Role-based web interfaces for Production, Warehouse, and Admin users. | High |
| 4 | Deployment & Operations | No infrastructure | A containerized application deployed on Kubernetes with full CI/CD and monitoring. | High |

---

## 2. Solution Building Blocks

### 2.1 Application Building Blocks

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| **ABB-01** | **Backend API Service** | A Spring Boot application providing a RESTful API for all business operations. | Gap #1, #3 | Build |
| **ABB-02** | **Frontend Web App** | An Angular single-page application (SPA) providing the user interface for all roles. | Gap #3 | Build |

### 2.2 Data Building Blocks

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| **DBB-01** | **PostgreSQL Schema** | The relational database schema, including tables, indexes, and constraints. | Gap #2 | Build |
| **DBB-02** | **Database Migrations** | A set of Flyway scripts to create and manage the database schema versions. | Gap #2 | Build |

### 2.3 Technology Building Blocks

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| **TBB-01** | **Containerization** | Dockerfiles for both the backend and frontend applications. | Gap #4 | Build |
| **TBB-02** | **CI/CD Pipeline** | A GitHub Actions workflow for automated build, test, and deployment. | Gap #4 | Build |
| **TBB-03** | **Kubernetes Manifests**| YAML files defining deployments, services, ingress, and secrets for the application. | Gap #4 | Build |
| **TBB-04** | **Monitoring Stack** | Configuration for Prometheus (metrics) and Grafana (dashboards). | Gap #4 | Buy/Reuse|

---

## 3. Build vs. Buy Analysis

### Build Decisions

| Component | Rationale |
|---|---|
| **Backend API Service (ABB-01)** | The business logic is specific to the company's shopfloor process and must be custom-built. |
| **Frontend Web App (ABB-02)** | The user interface must be tailored to the specific roles and workflows of the users. |
| **PostgreSQL Schema (DBB-01)** | The data model is derived from the custom business logic. |

### Buy/Reuse Decisions

| Component | Product/Library | Rationale |
|---|---|---|
| **Database System** | PostgreSQL | A mature, enterprise-grade open-source relational database. |
| **Web Frameworks**| Spring Boot, Angular | Standard, well-supported frameworks that accelerate development. |
| **Container Platform**| Docker, Kubernetes | Industry standards for containerization and orchestration. |
| **Monitoring Stack (TBB-04)**| Prometheus, Grafana | Powerful, widely-adopted open-source monitoring solutions. |

---

## 4. Work Packages

### Work Package Definition

| WP ID | Name | Building Blocks | Dependencies | Estimated Effort |
|---|---|---|---|---|
| **WP-01** | **Foundation Setup** | TBB-01, TBB-02, TBB-03, DBB-01, DBB-02 | None | 1 week |
| **WP-02** | **Core API & Workflow**| ABB-01 (partial) | WP-01 | 2 weeks |
| **WP-03** | **Core Frontend UI** | ABB-02 (partial) | WP-02 | 2 weeks |
| **WP-04** | **Admin Functionality**| ABB-01 (partial), ABB-02 (partial) | WP-03 | 1 week |
| **WP-05** | **Operational Readiness**| TBB-04 | WP-04 | 1 week |

### Work Package Sequencing

This sequence ensures foundational elements are built first, followed by core user-facing features.

`WP-01 (Foundation)` → `WP-02 (Core API)` → `WP-03 (Core UI)` → `WP-04 (Admin)` → `WP-05 (Ops)`
