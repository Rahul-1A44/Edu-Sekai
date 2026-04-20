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
 |<img width="989" height="532" alt="image" src="https://github.com/user-attachments/assets/016f3761-43f8-4b3f-81cd-e40ee20280fe" />


### Administrative Dashboard
School administrators can manage the entire institution, from student directories to granular system roles.

| Student Directory | Roles & Permissions |
| :---: | :---: |
| ![Student Directory](screenshots/image_066e0b.png) | ![Roles](screenshots/image_066e6c.png) |

### Learning Management System (LMS)
Each tenant schema includes a full LMS suite for distributing study materials and managing academic assignments.

| Study Materials | Assignments & Submissions |
| :---: | :---: |
| ![Study Materials](screenshots/image_066e29.jpg) | ![Assignments](screenshots/image_066e4b.png) |

### Advanced Access Control
Detailed permission management allows owners to toggle specific capabilities for staff and students within their schema.

<p align="center">
  <img src="screenshots/image_066ea2.png" width="90%" alt="Permissions Detail">
</p>

---

## Core Architecture Concepts

### Multi-Tenancy Strategy

The system uses **schema-based isolation** where each school's data lives in a completely separate PostgreSQL schema. This provides stronger isolation than row-level filtering approaches.

**Two Schema Types:**

1. **Public Schema** - Shared across all tenants
   - User authentication credentials
   - Organization metadata and billing
   - Domain routing tables
   - Payment transaction records

2. **Tenant Schemas** - One per school (e.g., `school_oxford`, `school_medhavi`)
   - Student academic records
   - Staff employment data
   - Roles and permissions
   - Guardian information
   - All school-specific operational data

### The Soft-Link Pattern

**The Challenge:**
Traditional Django uses Foreign Keys to link tables. However, PostgreSQL Foreign Keys cannot span schemas. A Foreign Key from a tenant table to the public `User` table would fail.

**The Solution:**
Instead of database-level Foreign Keys, we use **UUID Soft Links**:
- Tenant profiles store `user_id` as a plain UUID field
- This UUID references a `User.id` in the public schema
- The relationship is maintained through application logic, not database constraints.

### Username System

Users need intuitive usernames within their school context, but the system must ensure global uniqueness across all schools.

**Two-Tier Username Approach:**

1. **Local Username** (School-Specific)
   - Format: `firstnamemiddlenamelastname` (e.g., `johnprasaddoe`)
   - Used for login within a specific school context.

2. **Global Username** (System-Wide)
   - Format: `localusername_randomhex` (e.g., `johnprasaddoe_a1b2c3`)
   - Stored in the public `User` table for global uniqueness.

### Role-Based Access Control (RBAC)

**Tenant-Scoped Permissions:**
As of January 2026, roles and permissions are stored in each tenant's schema rather than globally. This provides complete customization flexibility. Each school receives a replicated set of system roles (Owner, Admin, Teacher, Student) which can then be customized.

---

## Getting Started

### Prerequisites
- Docker and Docker Compose
- Node.js 18+
- PostgreSQL 15+ (handled by Docker)

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

Run npm install && npm run dev in both ./frontend and ./tenant_frontend.

Development Tools
Database Seeding
Populate Mock Data:

Bash
docker compose exec backend python manage.py populate_test_data --schema=<schema_name>
Uses Faker to generate comprehensive test datasets for a specific tenant.

Audit & Maintenance
Check for orphaned profiles:

Bash
docker compose exec backend python manage.py audit_orphans --fix
Ensures referential integrity across schemas by scanning for broken UUID soft links.

Data Isolation Guarantees
Schema-Level Separation:

Each school's tables exist in a separate PostgreSQL schema.

Database connection sets search_path per request based on subdomain.

Example: oxford.localhost sets search_path to school_oxford, physically preventing access to other tenants.

Project Structure
EDU Sekai/
├── backend/            # Django API + Multi-Tenant Logic
├── frontend/           # SaaS Marketing + Registration
└── tenant_frontend/    # School Management Dashboard
Security Highlights
Authentication: JWT tokens stored in HttpOnly cookies.

Authorization: Granular RBAC checks on every endpoint.

Data Isolation: Hard isolation at the PostgreSQL schema level.

Protection: XSS protection, CSRF mitigation, and PBKDF2 hashing.
