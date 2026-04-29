## Jira-Ready Task Export

Please use Jira's bulk import feature to create these issues.

| Summary | Description | Priority | Dependencies |
| :--- | :--- | :--- | :--- |
| Backend - Data Model and Database Setup | Implement the data model and create the database schema. | Must Have | |
| Backend - Authentication | Implement the authentication endpoints and logic. | Must Have | Backend - Data Model and Database Setup |
| Backend - Delivery Order API | Implement the CRUD API for delivery orders. | Must Have | Backend - Data Model and Database Setup |
| Frontend - Login and User Views | Implement the login view and the role-specific views for Production, Warehouse, and Admin users. | Must Have | Backend - Authentication |
| Frontend - Order Management | Implement the functionality for creating, viewing, and updating delivery orders. | Must Have | Backend - Delivery Order API, Frontend - Login and User Views |
| Integration with Bosch Px Proxy | Configure the application to work with the Bosch Px Proxy. | Must Have | Backend - Delivery Order API |
