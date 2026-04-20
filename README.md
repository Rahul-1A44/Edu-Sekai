Markdown
# EDU Sekai: Multi-Tenant School Management System

EDU Sekai is a comprehensive SaaS platform for educational institutions, built using schema-based multi-tenancy to provide complete data isolation while maintaining a centralized identity and billing system.

---

## Project Overview

This repository contains three main components:

### 1. Backend (Django REST Framework)
**Location:** `./backend`  
**Port:** `http://localhost:8000`  
**Purpose:** Centralized API server, identity provider, and multi-tenant schema orchestrator.

### 2. SaaS Frontend (Next.js)
**Location:** `./frontend`  
**Port:** `http://localhost:3000`  
**Purpose:** Public-facing platform for marketing, registration, and payment processing.

### 3. Tenant Dashboard (Next.js)
**Location:** `./tenant_frontend`  
**Port:** `http://[school].localhost:3555`  
**Purpose:** School-specific management interface for daily operations and LMS.

---

## 📸 Screenshots & Demo

### Landing & Authentication
The platform features a dedicated SaaS landing page for school onboarding and a secure, subdomain-aware login system.

| Institution Registration | User Login |
| :---: | :---: |
| <img width="100%" alt="Registration" src="https://github.com/user-attachments/assets/016f3761-43f8-4b3f-81cd-e40ee20280fe" /> | <img width="100%" alt="Login" src="https://github.com/user-attachments/assets/45d2a023-7792-49ec-b5d0-9c02c50ee6ab" /> |

### Administrative Dashboard & LMS
School administrators can manage the entire institution, while instructors and students interact through a modular LMS.

| Student Directory | Study Materials |
| :---: | :---: |
| <img width="100%" alt="Student Directory" src="https://github.com/user-attachments/assets/eaedff8c-31a1-41c5-8cdb-f9a30ffd81c4" /> | <img width="100%" alt="Study Materials" src="https://github.com/user-attachments/assets/2743a093-3caf-4d21-96e4-fe2d760afdf2" /> |

### Advanced Access Control (RBAC)
Detailed permission management allows owners to toggle specific capabilities for staff and students within their unique schema.

| Role Management | Permission Configuration |
| :---: | :---: |
| <img width="100%" alt="Role Management" src="https://github.com/user-attachments/assets/c8d7bf41-0423-441f-b0cb-64f162c6c6e3" /> | <img width="100%" alt="Permissions" src="https://github.com/user-attachments/assets/d7f3f1b2-6d5a-40df-9798-403c4c203bfe" /> |

---

## Core Architecture Concepts

### Multi-Tenancy Strategy
The system uses **schema-based isolation** where each school's data lives in a completely separate PostgreSQL schema. This provides stronger isolation than row-level filtering approaches.



### The Soft-Link Pattern
**The Challenge:** PostgreSQL Foreign Keys cannot span schemas. A Foreign Key from a tenant table to the public `User` table would fail.

**The Solution:** Instead of database-level Foreign Keys, we use **UUID Soft Links**. Tenant profiles store `user_id` as a UUID referencing the public schema, maintained through application logic.

### Username System
1. **Local Username:** School-specific (e.g., `johnprasaddoe`).
2. **Global Username:** System-wide unique ID (e.g., `johnprasaddoe_a1b2c3`) stored in the public schema for authentication.

---

## Getting Started

### Prerequisites
* Docker and Docker Compose
* Node.js 18+
* PostgreSQL 15+ (handled by Docker)

### Quick Start

1. **Clone the repository**
   ```bash
   git clone [https://github.com/Rahul-1A44/Edu-Sekai](https://github.com/Rahul-1A44/Edu-Sekai)
   cd Edu-Sekai
Start the backend

Bash
cd backend
docker compose up --build
Start the Frontends
In two separate terminals:

Bash
# For SaaS Landing Page
cd frontend && npm install && npm run dev

# For Tenant Dashboard
cd tenant_frontend && npm install && npm run dev
Development Tools
Database Seeding
Populate mock data for testing:

Bash
docker compose exec backend python manage.py populate_test_data --schema=<schema_name>
Audit & Maintenance
Check and fix orphaned profiles (UUID soft links):

Bash
docker compose exec backend python manage.py audit_orphans --fix
Security & Isolation
Data Isolation: Hard isolation at the PostgreSQL schema level via search_path.

Authentication: JWT tokens stored in HttpOnly cookies.

Authorization: Granular RBAC checks on every protected endpoint.

Protection: XSS protection, CSRF mitigation, and PBKDF2 hashin
