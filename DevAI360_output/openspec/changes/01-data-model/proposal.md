# Data Model Proposal

This document outlines the data model for the Shopfloor Material Supply System.

## 1. Entities

### 1.1. User
Represents a user of the system.

-   `user_id` (Primary Key)
-   `username`
-   `password` (hashed)
-   `role` (Enum: 'Production', 'Warehouse', 'Admin')

### 1.2. Material
Represents a type of material that can be requested.

-   `material_id` (Primary Key)
-   `name`
-   `description`

### 1.3. ProductionLine
Represents a production line where materials are delivered.

-   `production_line_id` (Primary Key)
-   `name`

### 1.4. DeliveryOrder
Represents a single material supply request.

-   `order_id` (Primary Key)
-   `material_id` (Foreign Key to Material)
-   `quantity`
-   `production_line_id` (Foreign Key to ProductionLine)
-   `status` (Enum: 'New', 'In Preparation', 'In Transit', 'Completed')
-   `created_at`
-   `created_by` (Foreign Key to User)
-   `updated_at`

## 2. Relationships

-   A `User` can create many `DeliveryOrder`s.
-   A `Material` can be in many `DeliveryOrder`s.
-   A `ProductionLine` can have many `DeliveryOrder`s.
