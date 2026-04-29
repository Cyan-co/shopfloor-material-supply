# Software Requirements Specification (SRS) for Shopfloor Material Supply System

**Version:** 0.1
**Author:** Business Consultant
**Date:** 2024-05-21

---

## 1. Introduction

### 1.1 Purpose
This document outlines the functional and non-functional requirements for a new web application: the Shopfloor Material Supply System. The system will streamline the process of requesting and delivering materials from the warehouse to the production line, providing visibility and administrative control over the entire workflow.

### 1.2 Project Scope
The project will deliver a web application accessible to three user roles: Production Line Users, Warehouse Users, and Administrators. 

**In-Scope:**
- Creation of material supply requests.
- A workflow for processing delivery orders from creation to completion.
- Role-based access control for the defined user types.
- Administrative capabilities to monitor, edit, and delete orders.

**Out-of-Scope:**
- Inventory management (assuming this is handled by another system).
- User management (unless specified in OPL-002).
- Direct integration with other enterprise systems (unless specified).

---

## 2. Overall Description

### 2.1 User Classes and Characteristics
- **Production Line User:** Staff working on the factory floor who need to request materials. They require a simple, fast interface to submit requests and confirm receipt.
- **Warehouse User:** Staff responsible for fulfilling requests. They need a clear view of incoming orders and the ability to update the order status as they complete tasks.
- **Administrator:** Managers or supervisors who need to oversee the entire process, with the ability to intervene by modifying or deleting orders for exception handling.

### 2.2 Design and Implementation Constraints
- The application must be a web application, accessible from standard browsers within the facility.

---

## 3. System Features

### 3.1 Feature: Material Request
- **Description:** A Production Line User can create a new request for materials.
- **FR-001:** As a Production Line User, I can create a material supply request.

### 3.2 Feature: Order Fulfillment
- **Description:** A Warehouse User can view and process open delivery orders.
- **FR-002:** As a Warehouse User, I can view a list of open Delivery Orders.
- **FR-003:** As a Warehouse User, I can update the status of an order.

### 3.3 Feature: Order Completion
- **Description:** A Production Line User can confirm the receipt of materials to close the order.
- **FR-004:** As a Production Line User, I can mark an order as "Received" to complete it.

### 3.4 Feature: Administrative Oversight
- **Description:** An Administrator can monitor and manage all orders in the system.
- **FR-005:** As an Admin, I can view all orders and their current status.
- **FR-006:** As an Admin, I can manually edit the status of any order.
- **FR-007:** As an Admin, I can delete any order.

---

## 6. Quality Attributes

### 6.1 Security
- **SYS-001:** The system must restrict order editing and deletion functions to users with the "Admin" role.

### 6.2 Usability
- **SYS-002:** The interface must be clear and intuitive for non-technical staff on the production line and in the warehouse.

---

## Appendix A: Open Points List (OPL)

This list contains critical questions that must be answered to complete the requirements.

- **OPL-001 (HIGH):** What specific information must be included in a "material supply requirement" (e.g., Material ID, quantity, destination line, requested-by time)?
- **OPL-002 (HIGH):** How will users be authenticated (e.g., unique app logins, company single sign-on)?
- **OPL-003 (HIGH):** What is the complete lifecycle of an order? We have "New" -> "In Preparation" -> "In Transit" -> "Completed". Do we need "Cancelled" or "On Hold"?
- **OPL-004 (HIGH):** When an Admin edits or deletes an order, should a reason be recorded for an audit trail?
- **OPL-005 (MEDIUM):** Should the system generate notifications (e.g., to the warehouse when a new order is placed, or to the line when an order is in transit)?
- **OPL-006 (MEDIUM):** What specific data or views does the Admin need to effectively "monitor the whole process"? (e.g., a real-time dashboard, a filterable list of all orders, key performance metrics).
- **OPL-007 (LOW):** Are there any specific performance or availability requirements (e.g., expected number of daily orders, required uptime)?

