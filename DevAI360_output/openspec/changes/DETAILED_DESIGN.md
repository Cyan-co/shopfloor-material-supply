# Detailed Design

This document provides a summary of the low-level design for the Shopfloor Material Supply System.

## 1. Data Model

The data model consists of four main entities: User, Material, ProductionLine, and DeliveryOrder. The DeliveryOrder entity tracks the material, quantity, destination, status, and creator of each request.

## 2. Backend API

The backend will be a RESTful API with endpoints for authentication and managing delivery orders. It will include services to handle business logic and role-based access control to restrict actions based on user roles.

## 3. Frontend UI

The frontend will be a single-page application with different views for each user role (Production, Warehouse, Admin). It will provide a simple and intuitive interface for users to perform their tasks.
