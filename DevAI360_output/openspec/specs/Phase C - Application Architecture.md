# Phase C: Application Architecture (AA)

## 1. Component Overview

This architecture specifies a decoupled Single-Page Application (SPA) and REST API backend.

### Backend (Spring Boot 3.x)

-   **Language:** Java 21
-   **Framework:** Spring Boot 3.x
-   **Root Package:** `com.bosch.shopfloor`
-   **Architecture Pattern:** Controller-Service-Repository (3-Tier)

**Naming Conventions:**
-   **Controllers:** Suffix with `Controller` (e.g., `OrderController.java`). Responsible for handling HTTP requests and responses.
-   **Services:** Suffix with `Service` (e.g., `OrderService.java`). Contains all business logic.
-   **Repositories:** Suffix with `Repository` (e.g., `OrderRepository.java`). Manages data persistence.

**Rules:**
-   All business logic, including state transition validation and security checks, MUST reside exclusively in the **Service layer**.
-   Role-Based Access Control (RBAC) MUST be enforced within the Service layer methods, aligning with the rules defined in Phase B.
-   The Controller layer is for request/response handling and translation only; it should not contain business rules.

---

### Frontend Standards (Angular 17+)

-   **Framework:** Angular 17+
-   **Application Prefix:** `app-shopfloor`
-   **Styling:** Tailwind CSS for a utility-first CSS workflow.
-   **Architecture:** Component-based SPA.

**Naming Conventions:**
-   **Components:** `*.component.ts` (e.g., `order-list.component.ts`).
-   **Services:** `*.service.ts` (e.g., `order-api.service.ts`). Responsible for all communication with the backend API.

**Rules:**
-   The UI MUST be reactive to the user's role. Components should hide or disable controls that the user is not authorized to use (e.g., a Production user should not see a "Delete Order" button).
-   The frontend MUST NOT contain any business logic that is a duplicate of the backend's. All state changes must be driven by API calls.

---

## 2. Integration Contract

-   **API Style:** RESTful
-   **Base Path:** `/api`
-   **Data Format:** `application/json` for all request and response bodies.
-   **Authentication:** Bearer Token (JWT) in the `Authorization` header.

---

## 3. Endpoints (Derived from Business Architecture)

These endpoints are the minimum required to implement the business process defined in Phase B.

| Method | Endpoint | Role | Description |
|---|---|---|---|
| `POST` | `/api/orders` | **Production User** | Creates a new Delivery Order. Request body must contain `materialId`, `quantity`, and `destinationLine`. |
| `GET` | `/api/orders` | **All Roles** | Retrieves a list of orders. The results will be filtered by the backend based on the user's role (Production users see their own orders, Warehouse/Admin see all). |
| `GET` | `/api/orders/{id}` | **All Roles** | Retrieves a single order by its ID. Access is subject to the same role-based filtering as the list view. |
| `PATCH`| `/api/orders/{id}/status`| **Production/Warehouse** | Updates the status of an order according to the business process flow. The request body must contain the new `status` (e.g., `{ "status": "IN_PREPARATION" }`). The backend will enforce valid state transitions. |
| `PUT` | `/api/orders/{id}/status` | **Admin** | Manually overrides the status of any order (except `COMPLETED`). Used for exception handling. |
| `DELETE`| `/api/orders/{id}` | **Admin** | Deletes an order. The backend will enforce the rule that only orders in `NEW` state can be deleted. |

---

## 4. Security Implementation

-   **Enforcement Point:** Security, especially RBAC, is enforced on the **backend** in the Service layer. The frontend is considered untrusted.
-   **Roles:** The roles used for endpoint authorization (`PRODUCTION_USER`, `WAREHOUSE_USER`, `ADMIN`) MUST directly correspond to the Business Actors defined in Phase B.
-   **Authentication:** The identity of the user MUST be established via a valid JWT before any endpoint can be accessed.
