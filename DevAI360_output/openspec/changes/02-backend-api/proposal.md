# Backend API Proposal

This document outlines the backend API for the Shopfloor Material Supply System.

## 1. Endpoints

### 1.1. Authentication
-   `POST /login`: Authenticates a user and returns a JWT.
-   `POST /logout`: Logs out a user.

### 1.2. Delivery Orders
-   `GET /orders`: Retrieves a list of delivery orders.
    -   Query Params: `status`, `production_line_id`
-   `POST /orders`: Creates a new delivery order.
-   `GET /orders/{order_id}`: Retrieves a specific delivery order.
-   `PUT /orders/{order_id}`: Updates the status of a delivery order.
-   `DELETE /orders/{order_id}`: Deletes a delivery order (Admin only).

## 2. Services

### 2.1. OrderService
-   `createOrder(material_id, quantity, production_line_id, user_id)`
-   `getOrders(status, production_line_id)`
-   `getOrderById(order_id)`
-   `updateOrderStatus(order_id, status)`
-   `deleteOrder(order_id)`

### 2.2. UserService
-   `authenticate(username, password)`
-   `get_user_by_id(user_id)`

## 3. Business Logic

-   Only 'Production' role can create orders.
-   Only 'Warehouse' role can update order status to 'In Preparation' and 'In Transit'.
-   Only 'Production' role can update order status to 'Completed'.
-   Only 'Admin' role can delete orders and update to any status.
