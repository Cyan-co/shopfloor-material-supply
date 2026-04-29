# Phase B: Business Architecture (BA)

## 1. Business Domain

### Domain
The business domain is **Shopfloor Material Logistics**. This encompasses the processes, rules, and actors involved in requesting materials from a central warehouse and delivering them to a specific production line within a manufacturing facility.

### Process Description
The core process is the lifecycle of a **Delivery Order**. It begins when a Production Line User identifies a need for materials and submits a request. The request is then picked up by the Warehouse, prepared for delivery, and transported. The process concludes when the Production Line User confirms the materials have been received. The entire lifecycle is monitored and managed by Administrators to ensure smooth operations.

---

## 2. Business Actors & Roles

This section defines the actors who interact with the system and their specific responsibilities.

| Role | Actor | Responsibilities |
|---|---|---|
| **Production Line User** | Shopfloor Staff | - Create new Delivery Orders when materials are needed. <br>- View the status of their own outstanding orders. <br>- Confirm the receipt of a delivery, marking the order as 'Completed'. |
| **Warehouse User** | Warehouse Staff | - Monitor the queue for new Delivery Orders. <br>- Change the order status to 'In Preparation' when they begin working on it. <br>- Change the order status to 'In Transit' when the materials are dispatched. |
| **Administrator** | Supervisor / Manager | - View all Delivery Orders across the entire system, regardless of status. <br>- Manually edit the status of any order to correct errors or handle exceptions. <br>- Delete orders that were created in error. |

---

## 3. Business Process Flow (The Golden Path)

This is the standard, successful lifecycle of a Delivery Order from creation to completion.

`NEW` → `IN_PREPARATION` → `IN_TRANSIT` → `COMPLETED`

-   **NEW:** A Production Line User submits a valid material request.
-   **IN_PREPARATION:** A Warehouse User has acknowledged the request and is gathering the materials.
-   **IN_TRANSIT:** The materials have left the warehouse and are on their way to the production line.
-   **COMPLETED:** The Production Line User has confirmed the materials have been received.

---

## 4. Business Rules

These rules are mandatory and must be enforced by the backend business logic. They are non-negotiable constraints on system behavior.

| Rule ID | Rule Description | Implementation Constraint |
|---|---|---|
| **BR-001** | **State Transition Control (Warehouse)** | A Warehouse User can only transition an order from `NEW` to `IN_PREPARATION`, and from `IN_PREPARATION` to `IN_TRANSIT`. They cannot move an order to any other state. |
| **BR-002** | **State Transition Control (Production)** | A Production Line User can only transition an order from `IN_TRANSIT` to `COMPLETED`. They can only act on orders destined for their own production line. |
| **BR-003** | **State Immutability** | An order in the `COMPLETED` state is considered final and cannot be altered by any user, including Administrators. |
| **BR-004** | **Admin Override Authority** | An Administrator can change the status of any order *except* for a `COMPLETED` one. This allows for manual correction of in-process orders. |
| **BR-005** | **Deletion Constraint** | An Administrator can only delete orders that are in the `NEW` state. This prevents the accidental deletion of orders that are already being processed. |
| **BR-006** | **Role-Based Access (RBAC)** | A user's role MUST be checked before any create, update, or delete operation is performed. The action must be denied if the user's role does not have the required permission as defined in BR-001, BR-002, BR-004, and BR-005. |
| **BR-007** | **Request Integrity** | A new Delivery Order request is only valid if it contains a Material ID, a Quantity greater than zero, and a valid Destination Production Line. |
