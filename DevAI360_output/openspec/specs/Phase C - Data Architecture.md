# Phase C: Data Architecture (DA)

## 1. Data Architecture Overview

### Data Principles
- **Single Source of Truth:** The PostgreSQL database is the definitive source for all application data.
- **Integrity by Design:** Data integrity is enforced at the database level using constraints (PK, FK, CHECK) to prevent invalid data.
- **Auditability:** All state changes to critical entities (Delivery Orders) must be logged for traceability.
- **Security First:** Sensitive data, such as user credentials, must be hashed, not stored in plaintext.

### Technology Stack
- **Database:** PostgreSQL 15+
- **ORM:** Spring Data JPA
- **Migrations:** Flyway for schema version control.
- **Caching:** Not required for MVP.

---

## 2. Logical Data Model

### 2.1 Core Entities

| Entity | Description | Owner |
|---|---|---|
| **User** | Represents an actor in the system (Production, Warehouse, Admin). | System |
| **DeliveryOrder** | Represents a single material supply request and its entire lifecycle. | Business |

### 2.2 Entity Relationships

A User can create many Delivery Orders.

```
User (1) ----< (N) DeliveryOrder
```

---

## 3. Physical Data Model

### 3.1 Naming Conventions

| Element | Convention | Example |
|---|---|---|
| Table | `snake_case`, plural | `delivery_orders`, `users` |
| Column | `snake_case` | `created_at`, `order_status` |
| Primary Key | `id` | `id` |
| Foreign Key | `{table_name_singular}_id` | `user_id` |
| Index | `idx_{table}_{columns}` | `idx_orders_status` |

### 3.2 Table Definitions

**Table: `users`**

| Column | Type | Constraints | Index |
|---|---|---|---|
| `id` | `UUID` | PK, NOT NULL | PK |
| `username` | `VARCHAR(255)` | UNIQUE, NOT NULL | UQ |
| `password_hash`| `VARCHAR(255)` | NOT NULL | - |
| `role` | `VARCHAR(50)` | NOT NULL, CHECK (role IN ('PRODUCTION', 'WAREHOUSE', 'ADMIN')) | IDX |
| `created_at` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | - |
| `updated_at` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | - |

**Table: `delivery_orders`**

| Column | Type | Constraints | Index |
|---|---|---|---|
| `id` | `UUID` | PK, NOT NULL | PK |
| `material_id`| `VARCHAR(100)` | NOT NULL | - |
| `quantity` | `INTEGER` | NOT NULL, CHECK (quantity > 0) | - |
| `destination_line`| `VARCHAR(100)`| NOT NULL | - |
| `status` | `VARCHAR(50)` | NOT NULL, CHECK (status IN ('NEW', 'IN_PREPARATION', 'IN_TRANSIT', 'COMPLETED')) | IDX |
| `requester_id`| `UUID` | NOT NULL, FK to users(id) ON DELETE RESTRICT | FK |
| `created_at` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | IDX |
| `updated_at` | `TIMESTAMPTZ` | NOT NULL, DEFAULT NOW() | - |

### 3.3 Index Strategy

| Table | Index | Columns | Type | Rationale |
|---|---|---|---|---|
| `users` | `idx_users_role` | `role` | B-tree | To quickly find users by their role. |
| `delivery_orders`| `idx_orders_status`| `status` | B-tree | To efficiently query orders by their current status, which is a primary use case for warehouse users. |
| `delivery_orders`| `idx_orders_requester_id`| `requester_id`| B-tree | Foreign key index to support joins and queries for a specific user's orders. |
| `delivery_orders`| `idx_orders_created_at`| `created_at`| B-tree | To allow efficient sorting of orders by creation time. |

---

## 4. Data Integrity

- **Referential Integrity:** The `requester_id` in `delivery_orders` must always point to a valid record in the `users` table. `ON DELETE RESTRICT` is used to prevent a user from being deleted if they have associated orders.
- **Business Constraints:**
    - `users.role` is restricted to the three valid roles.
    - `delivery_orders.quantity` must be a positive integer.
    - `delivery_orders.status` is restricted to the four valid states in the lifecycle.

---

## 5. Data Migration

- **Tool:** Flyway will be used to manage all schema changes.
- **Strategy:** Migrations will be written in SQL and follow a versioned naming convention (e.g., `V20240521_001__create_users_table.sql`).
- **Rule:** All schema changes, without exception, must be deployed via a Flyway migration script. Manual changes to the database schema are forbidden in all environments.

---

## 6. Audit Trail

For the MVP, a simple audit trail will be implemented for status changes on the `delivery_orders` table.

**Table: `order_status_history`**

| Column | Type | Description |
|---|---|---|
| `id` | `UUID` | Primary key |
| `order_id` | `UUID` | The order that was changed (FK to delivery_orders.id) |
| `old_status`| `VARCHAR(50)`| The status before the change. Can be NULL for initial creation. |
| `new_status`| `VARCHAR(50)`| The status after the change. |
| `changed_by_id`| `UUID` | The user who performed the action (FK to users.id) |
| `timestamp` | `TIMESTAMPTZ` | When the change occurred. |

This table will be populated by a trigger in the backend service logic whenever an order's status is modified.
