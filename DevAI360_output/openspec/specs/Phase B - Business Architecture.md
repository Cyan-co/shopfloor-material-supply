# Phase B: Business Architecture (BA)

## 1. Business Domain

### Domain
The core business domain is **Shopfloor Material Logistics**. This covers the workflow of requesting, preparing, delivering, and receiving materials within a manufacturing environment, specifically between the warehouse and the production lines.

### Process description
The process begins when a production line operator identifies a need for materials and initiates a digital request. This request, termed a "Delivery Order," enters a queue for the warehouse. A warehouse operator then picks up the order, prepares the materials, and marks the order as in transit. The process completes when the production line operator confirms the materials have been received. An administrator oversees this entire process, with the ability to intervene if necessary.

---

## 2. Business Actors & Roles

- **Production Line User:**
  - **Responsibilities:**
    - Create new material supply requests (Delivery Orders).
    - View the status of their outstanding requests.
    - Confirm the receipt of a delivery to close the order.

- **Warehouse User:**
  - **Responsibilities:**
    - Monitor the queue of new Delivery Orders.
    - Accept an order and begin the material preparation process.
    - Update the order status as it moves from preparation to delivery.

- **Administrator:**
  - **Responsibilities:**
    - Monitor all Delivery Orders across the entire system, regardless of status.
    - Manually edit the status of any order to correct errors or handle exceptions.
    - Delete orders if they are duplicated or created in error.

---

## 3. Business Process Flow (The Golden Path)

The lifecycle of a Delivery Order follows a strict, linear progression:

`NEW` -> `IN_PREPARATION` -> `IN_TRANSIT` -> `COMPLETED`

---

## 4. Business Rules

These rules are mandatory and must be enforced by the backend system logic.

- **State Transition Constraints:**
  - An order in `NEW` can only transition to `IN_PREPARATION`. This action is restricted to Warehouse Users.
  - An order in `IN_PREPARATION` can only transition to `IN_TRANSIT`. This action is restricted to Warehouse Users.
  - An order in `IN_TRANSIT` can only transition to `COMPLETED`. This action is restricted to Production Line Users.
  - The `COMPLETED` state is final and cannot be changed, except by an Administrator.

- **Role-Based Access Control (RBAC) Rules:**
  - Only Production Line Users can create orders with the initial `NEW` status.
  - Only Warehouse Users can transition an order from `NEW` to `IN_PREPARATION` and from `IN_PREPARATION` to `IN_TRANSIT`.
  - Only Production Line Users can transition an order from `IN_TRANSIT` to `COMPLETED`.
  - Only Administrators can view all orders, edit any order's status at any time, or delete an order.

- **Immutability Rules:**
  - Once an order is created, the core request details (Material ID, Quantity, Destination Production Line) cannot be edited. If changes are needed, the existing order must be deleted by an Admin and a new one created.

- **Exception Handling:**
  - If an order needs to be moved to a non-linear state (e.g., from `IN_TRANSIT` back to `IN_PREPARATION`), this action can only be performed by an Administrator.
  - Deletion of any order, regardless of state, is a privileged action restricted to Administrators. A confirmation prompt must be displayed before the action is finalized.
