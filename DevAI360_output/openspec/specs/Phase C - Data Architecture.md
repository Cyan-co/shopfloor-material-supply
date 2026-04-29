# Phase C: Data Architecture (DA)

## 1. Data Architecture Overview

### Data Principles
- **Single Source of Truth:** Each business entity (e.g., a Delivery Order) will have a single, authoritative record in the database.
- **Data Integrity:** Constraints (e.g., NOT NULL, foreign keys) will be enforced at the database level to ensure the data is always valid.
- **Audit Trail:** All changes to critical data, especially order status, will be logged for accountability and traceability.
- **Soft Delete:** Orders will be soft-deleted to allow for recovery and to maintain historical data integrity.

### Technology Stack
- **Database:** PostgreSQL 15+
- **ORM:** Spring Data JPA
- **Migrations:** Flyway for schema migrations.
- **Caching:** Not required for MVP.

---

## 2. Logical Data Model

### 2.1 Core Entities

| Entity | Description |
|---|---|
| **User** | Represents an individual who can log in to the system (Production, Warehouse, or Admin). |
| **DeliveryOrder** | Represents a single material supply request from creation to completion. |

### 2.2 Entity Relationships

`User (1) ---- (N) DeliveryOrder` (One user can create many orders)

---

### 2.3 Entity Attributes

**Entity: User**
| Attribute | Type | Nullable | Description |
|---|---|---|---|
| id | UUID | No | Primary key |
| username | String | No | Unique login identifier |
| password_hash | String | No | Hashed password |
| role | String | No | User role ('PRODUCTION_LINE', 'WAREHOUSE', 'ADMIN') |
| created_at | TIMESTAMP | No | Creation timestamp |
| updated_at | TIMESTAMP | No | Last update timestamp |

**Entity: DeliveryOrder**
| Attribute | Type | Nullable | Description |
|---|---|---|---|
| id | UUID | No | Primary key |
| material_id | String | No | Identifier for the requested material |
| quantity | Integer | No | The amount of material requested |
| destination | String | No | The production line where the material is needed |
| status | String | No | The current state of the order ('NEW', 'IN_PREPARATION', etc.) |
| created_by_id | UUID | No | Foreign key to the User who created the order |
| created_at | TIMESTAMP | No | Creation timestamp |
| updated_at | TIMESTAMP | No | Last update timestamp |
| is_deleted | Boolean | No | Flag for soft deletion |

---

## 3. Physical Data Model

### 3.1 Database Schema
- **Schema Name:** `shopfloor_supply`

### 3.2 Table Definitions

**Table: users**
| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| username | VARCHAR(255) | UNIQUE, NOT NULL | UQ |
| password_hash | VARCHAR(255) | NOT NULL | - |
| role | VARCHAR(50) | NOT NULL | IDX |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |

**Table: delivery_orders**
| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| material_id | VARCHAR(255) | NOT NULL | - |
| quantity | INT | NOT NULL | - |
| destination | VARCHAR(255) | NOT NULL | - |
| status | VARCHAR(50) | NOT NULL | IDX |
| created_by_id | UUID | FK to users(id), NOT NULL | IDX |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| is_deleted | BOOLEAN | NOT NULL, DEFAULT FALSE | IDX |

### 3.3 Naming Conventions
| Element | Convention | Example |
|---|---|---|
| Table | snake_case, plural | `users`, `delivery_orders` |
| Column | snake_case | `created_at`, `material_id` |
| Primary Key | `id` | `id` |
| Foreign Key | `{table}_id` | `created_by_id` |
| Index | `idx_{table}_{columns}` | `idx_delivery_orders_status` |

### 3.4 Index Strategy
| Table | Index | Columns | Type | Rationale |
|---|---|---|---|---|
| delivery_orders | idx_delivery_orders_status | status | B-tree | To quickly query orders by their current status. |
| delivery_orders | idx_delivery_orders_created_by | created_by_id | B-tree | To efficiently retrieve orders for a specific user. |
| users | idx_users_role | role | B-tree | To quickly find users by their assigned role. |

---

## 4. Data Integrity

- **Referential Integrity:** The `created_by_id` in `delivery_orders` MUST correspond to a valid `id` in the `users` table. `ON DELETE` will be `RESTRICT` to prevent user deletion if they have associated orders.
- **Business Constraints:**
  - `delivery_orders.quantity` must be greater than 0.
  - `users.role` must be one of the predefined roles.
- **Validation:** All validation rules (e.g., non-null fields) will be enforced by database constraints and checked in the application's service layer before attempting a database operation.

---

## 5. Data Lifecycle

- **Data States:** `ACTIVE`, `DELETED` (soft-deleted). Archiving is not in scope for the MVP.
- **Retention Policies:**
  - Soft-deleted records will be purged after 90 days.
  - Completed orders will be kept for 7 years before being archived (post-MVP).
- **Audit Trail:** An `audit_log` table will be created to track all status changes on the `delivery_orders` table, as well as any administrative edits or deletions.

---
This document provides the data architecture blueprint. All database development and data handling logic must conform to these specifications.
