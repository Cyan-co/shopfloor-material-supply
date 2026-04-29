# Frontend UI Proposal

This document outlines the frontend UI for the Shopfloor Material Supply System.

## 1. Views

### 1.1. Login View
-   A simple form with username and password fields.

### 1.2. Production Line View
-   A button to create a new material request.
-   A list of their own active orders with status.
-   A button to mark an order as 'Completed'.

### 1.3. Warehouse View
-   A list of all 'New' and 'In Preparation' orders.
-   Buttons to update the status of orders to 'In Preparation' and 'In Transit'.

### 1.4. Admin View
-   A dashboard with a list of all orders.
-   Filters for status and production line.
-   Buttons to edit the status of any order and to delete orders.

## 2. Components

-   `OrderForm`: A form to create a new delivery order.
-   `OrderList`: A component to display a list of orders.
-   `OrderItem`: A component to display a single order.
-   `StatusUpdateButtons`: Buttons to update the status of an order.
-   `AdminDashboard`: The main view for the admin.
