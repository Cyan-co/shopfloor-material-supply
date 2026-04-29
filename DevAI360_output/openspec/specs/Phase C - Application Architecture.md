# Phase C: Application Architecture (AA)

## 1. Component Overview

### Backend (Spring Boot 3.x)

- **Root Package:** `com.bosch.shopfloor.supply`
- **Architecture Pattern:** Controller-Service-Repository

**Naming Conventions:**
- **Controllers:** `DeliveryOrderController.java`, `AdminController.java`
- **Services:** `DeliveryOrderService.java`, `UserService.java`
- **Repositories:** `DeliveryOrderRepository.java`, `UserRepository.java`

**Rules:**
- All business logic, including state transitions and validation, **MUST** reside within the Service layer.
- Role-Based Access Control (RBAC) checks **MUST** be enforced within the Service layer methods, not in the Controller.

---

### Frontend Standards (Angular 17+)

- **App Prefix:** `sfs` (Shopfloor Supply)
- **Styling:** Tailwind CSS for a utility-first styling approach.

**Naming Conventions:**
- **Components:** `order-list.component.ts`, `create-order.component.ts`, `admin-dashboard.component.ts`
- **Services:** `order.service.ts`, `auth.service.ts`

**Rules:**
- The UI **MUST** dynamically show/hide controls based on the user's role (e.g., a Warehouse User should not see the "Delete Order" button).
- The frontend **MUST NOT** contain any business logic that duplicates or bypasses backend rules. It is responsible for presentation only.

---

## 2. Integration Contract

- **API Style:** RESTful
- **Base Path:** `/api/v1`
- **Format:** JSON (application/json)
- **Status Codes:**
  - **200 OK:** Successful GET or PATCH request.
  - **201 Created:** Successful POST request.
  - **204 No Content:** Successful DELETE request.
  - **400 Bad Request:** The request is malformed or violates a business rule (e.g., invalid state transition).
  - **403 Forbidden:** The user is not authorized to perform the action.
  - **404 Not Found:** The requested resource (e.g., an order) does not exist.

---

## 3. Endpoints (Derived from Business Architecture)

### Delivery Orders (`/api/v1/orders`)

- **`GET /api/v1/orders`**
  - **Description:** Get a list of orders.
  - **Permissions:**
    - **Production Line User:** Returns only orders created by them.
    - **Warehouse User:** Returns orders with `NEW` or `IN_PREPARATION` status.
    - **Administrator:** Returns all orders.
  - **Query Params:** `status={status}` to filter by status.

- **`POST /api/v1/orders`**
  - **Description:** Create a new Delivery Order.
  - **Permissions:** **Production Line User** only.
  - **Request Body:** `{ "materialId": "string", "quantity": number, "destination": "string" }`
  - **Behavior:** Creates an order with `NEW` status.

- **`PATCH /api/v1/orders/{id}/status`**
  - **Description:** Update the status of a Delivery Order.
  - **Permissions:**
    - **Warehouse User:** Can transition `NEW` -> `IN_PREPARATION` and `IN_PREPARATION` -> `IN_TRANSIT`.
    - **Production Line User:** Can transition `IN_TRANSIT` -> `COMPLETED`.
    - **Administrator:** Can set any valid status.
  - **Request Body:** `{ "status": "string" }` (e.g., "IN_PREPARATION").
  - **Behavior:** The backend Service layer MUST enforce the state transition rules from Phase B.

- **`DELETE /api/v1/orders/{id}`**
  - **Description:** Delete a Delivery Order.
  - **Permissions:** **Administrator** only.

---

## 4. Application Pattern

- **Decoupled Client-Server Architecture:** An Angular Single-Page Application (SPA) will be the client, interacting with a Spring Boot application that serves the REST API. This ensures a clean separation of concerns between the presentation and business logic layers.

- **Java Package:** `com.bosch.shopfloor.supply`
- **Angular Prefix:** `sfs`

---

## 5. Interface Contract (API)
The API contract is strictly defined by the endpoints in section 3.

- **Base Path:** `/api/v1`
- **Format:** JSON
- **Error Handling:**
  - The API **MUST** return standard HTTP status codes as defined in section 2.
  - Error responses should include a meaningful message, for example: `{"error": "Invalid state transition from NEW to COMPLETED."}`

---

## 6. Security Implementation

- RBAC **MUST** be implemented in the backend's Service layer, likely using Spring Security with method-level annotations (e.g., `@PreAuthorize`).
- User roles (`PRODUCTION_LINE`, `WAREHOUSE`, `ADMIN`) **MUST** directly correspond to the definitions in Phase B.
- The frontend is considered an untrusted client. All authorization and business rule enforcement **MUST** happen on the backend.

---
