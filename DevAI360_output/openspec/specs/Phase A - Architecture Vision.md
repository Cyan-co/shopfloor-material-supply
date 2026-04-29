# Phase A: Architecture Vision

## 1. Vision and scope

### Vision
To create a digitized, real-time, and accountable workflow for shopfloor material supply, replacing the manual request process. The system aims to significantly reduce operational delays, minimize fulfillment errors, and provide transparent data for continuous process optimization.

### Scope
The initial release will be a Minimum Viable Product (MVP) focused on the core material request and fulfillment lifecycle.

**In Scope (MVP):**
-   **User Roles:** Production Line User, Warehouse User, and Administrator.
-   **Core Workflow:**
    -   Production users can create material requests (Material ID, Quantity, Destination).
    -   Warehouse users can view and process new orders (New → In Preparation → In Transit).
    -   Production users can confirm receipt of orders (In Transit → Completed).
-   **Admin Functions:**
    -   View all orders, regardless of status.
    -   Filter orders by status and search by Order ID.
    -   Manually edit or delete any order.

**Out of Scope (Post-MVP):**
-   Real-time KPI dashboard.
-   Automated user notifications.
-   Enterprise Single Sign-On (SSO) integration.
-   Inventory management features.

### Stakeholders
-   **Production Line Users:** Staff who request materials for their production lines.
-   **Warehouse Users:** Staff responsible for fulfilling material requests.
-   **Administrators:** Supervisors or managers who oversee the end-to-end process and manage system data.

---

## 2. Architecture principles (impact on implementation)

These principles are mandatory and must be enforced during development and code review.

| # | Principle | Rationale | Implementation Impact |
|---|---|---|---|
| 1 | **Simplicity and Usability First** | The primary users are from the shopfloor and may not be tech-savvy. The UI must be intuitive to ensure high adoption and minimize errors. | - Frontend development must prioritize a clean, uncluttered interface. <br>- Workflows must be linear and require minimal clicks. <br>- Use standard, universally understood icons and labels. No complex UI patterns. |
| 2 | **Secure by Default** | The system manages operational data and must prevent unauthorized actions. Access control is critical. | - API endpoints for administrative actions (edit, delete) MUST be protected and require the 'Admin' role. <br>- Role-based access control (RBAC) must be implemented at the API gateway or backend service layer. <br>- All data in transit must be encrypted (HTTPS). |
| 3 | **Stateless Services** | To ensure scalability and reliability, backend services should not store session state. This allows for horizontal scaling and simplifies failover. | - All backend APIs must be RESTful and stateless. <br>- Any required state (e.g., user authentication) must be managed via tokens (e.g., JWT) passed in the request header, not stored on the server. |
| 4 | **Standardized Technology Stack** | To ensure consistency, maintainability, and alignment with enterprise standards, a specific technology stack is mandated. | - **Backend:** Must be implemented using **Java 21**. <br>- **Frontend:** Must be implemented using **Angular 17**. <br>- **API Gateway:** All external traffic must be routed through the **Bosch Px Proxy**. <br>- No other frameworks or major libraries are to be introduced without a formal exception approval. |
| 5 | **Reliability over Feature-Completeness** | The system is critical for production line operations. Uptime and data integrity are more important than advanced features in the initial phase. | - The application must handle failures gracefully (e.g., database connection loss). <br>- A comprehensive logging strategy must be implemented for all services to facilitate quick debugging. <br>- Automated tests (unit, integration) are required for all critical business logic. |

---

## 3. How to use this document

-   **When to read:** This document must be the starting point for all design and development activities. It should be reviewed before beginning any new feature or making significant technical changes.
-   **How to apply:** The "Implementation Impact" column is not a suggestion; it is a set of rules. Code reviews must explicitly check for compliance with these principles.
-   **Conflict Resolution:** If a requirement in the PRD appears to conflict with a principle, the principle takes precedence. The conflict must be flagged to the Solution Architect for resolution before proceeding with implementation.

---

## 4. References

This document is the first of a series that will define the complete system architecture.

-   Phase B – Business Architecture (Forthcoming)
-   Phase C – Application & Data Architecture (Forthcoming)
-   Phase D – Technology Architecture (Forthcoming)
-   Phase F – Migration Plan (Forthcoming)
-   Phase G – Governance (Forthcoming)
