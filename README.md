# Web Multi-Tenant Approval and User Management System[Thanks for Senior Developer who approved this flow]

## Overview

**Web** is a multi-tenant enterprise-grade platform designed for managing organizational hierarchies, user roles, and approval workflows in a streamlined and secure manner. Each tenant represents an organization (e.g., Reliance, Birlasoft) with its own users, managers, and admins. The system integrates with **MyBiz User Registration** to synchronize user and role data while maintaining role-based permissions and hierarchical approval logic.

---

## 🏗️ System Architecture

The architecture revolves around three major layers:

1. **Web Superuser Layer** – The superuser (FexStack root admin) oversees all tenants.
2. **Tenant Layer** – Each organization (tenant) manages its internal users, admins, and managers.
3. **User Registration Layer** – Syncs user information between **MyBiz** and **Web**, ensuring consistency in access levels and permissions.

### Tenant Example

* **Tenant - Reliance**

  * Users: `harshit`, `H2` (Manager)
  * Admin: `R`
  * System Admin Account: `reliance-admin (ADMIN_ROLE)`
  * Note: Managers can be temporarily ignored for this flow.

* **Tenant - Birlasoft**

  * Users: `a`, `b`, `c (Manager)`
  * Admin: `birlasoft-admin (ADMIN_ROLE)`

The approval process for each tenant is performed by the **tenant admin**, ensuring isolation and independence of approval logic between organizations.

---

## 🧩 Database Schema

The system uses a normalized **PostgreSQL** schema to manage users, travel requests, bookings, documents, and expenses. Below is the breakdown:

### **Tables Overview:**

#### 1. `users`

Stores all user credentials and profile data.

```text
id (PK)
user_org_id
email
first_name
last_name
gstin
password
created_at
modified_at
is_deleted
```

#### 2. `travel_request`

Represents the main travel request entity linked to a specific user and tenant.

```text
id (PK)
tenant_id
title
current_status
from_date_time
to_date_time
mybiz_requisition_id
created_at
modified_at
user_id (FK → users.id)
mybiz_customer_id
message
osType
```

#### 3. `traveler`

Tracks each traveler linked to a travel request.

```text
id (PK)
name
email
is_primary_pax
created_at
updated_at
travel_request_id (FK → travel_request.id)
```

#### 4. `booking`

Stores details of travel bookings associated with travel requests.

```text
id (PK)
requisition_id
service_id
booking_info
created_at
updated_at
itinerary_id (FK → itinerary.id)
status
```

#### 5. `booking_document`

Captures all documents (like invoices or tickets) related to a booking.

```text
id (PK)
requisition_id
document_type
object_key
service_id
action_type
invoice_number
created_at
booking_id (FK → booking.id)
```

#### 6. `itinerary`

Defines travel itineraries for requests.

```text
id (PK)
travel_info
created_at
updated_at
travel_request_id (FK → travel_request.id)
```

#### 7. `expense_report`

Represents user-submitted travel expense reports.

```text
id (PK)
tenant_id
title
status
from_date_time
to_date_time
created_at
updated_at
user_id (FK → users.id)
message
```

#### 8. `expense_item`

Stores individual expense details belonging to an expense report.

```text
id (PK)
expense_type
description
date
mode
amount
currency
expense_report_id (FK → expense_report.id)
```

---

## 🔁 Approval Workflow

The **approval flow** is structured as follows:

1. A **user** within a tenant (e.g., `harshit` from Reliance) initiates a **Travel Request** or **Expense Report**.
2. The request is then sent to the **tenant’s admin** (`reliance-admin`) for approval.
3. The **admin** validates and approves the request, updating its status.
4. Optionally, managers may be involved for intermediate approval, though this step is currently out of scope.

This workflow ensures that no cross-tenant interference occurs. For example, a Reliance admin cannot approve Birlasoft’s requests.

---

## 🔐 Role Mapping Between Systems

| MyBiz Role               | FexStack Role                       |
| ------------------------ | ----------------------------------- |
| Line1 (Admin User)       | Admin (`ADMIN_ROLE`)                |
| User1 (User/Manager)     | Purchasing User (`PURCHASING_USER`) |
| Line2 / Line3 (Optional) | Optional Hierarchies (Future Use)   |

This mapping ensures consistent user identity and permission synchronization between MyBiz and FexStack.

---

## ⚙️ Key Features

* **Multi-Tenant Architecture** – Each tenant is isolated by schema and logic.
* **Role-Based Access Control (RBAC)** – Supports Admin, Manager, and User roles.
* **Approval Workflow Automation** – Streamlined approvals at the tenant admin level.
* **Audit and History Tracking** – Every action (request creation, modification, approval) is logged.
* **Document Management** – Securely stores and links invoices and travel documents.
* **Expense Management** – Detailed expense tracking with reporting and currency support.

---

## 🧠 Example Scenario

1. **Reliance user `harshit`** creates a **Travel Request** in MyBiz.
2. MyBiz syncs the data with FexStack, associating it with the `tenant_id` for Reliance.
3. The request gets assigned to `reliance-admin` for approval.
4. After validation, the **admin** updates the status, allowing the user to proceed with booking.
5. Later, expense reports are generated, tied to the same tenant for traceability.

---

## 📊 ER Diagram Reference

The ER diagram visualizes relationships among key entities (`users`, `travel_request`, `booking`, `itinerary`, `expense_report`, etc.), ensuring modular and referential data integrity.

---

## 🛠️ Tech Stack

* **Backend:** Spring Boot (Java)
* **Database:** PostgreSQL
* **ORM:** Hibernate / JPA
* **Version Control:** GitHub
* **Architecture Pattern:** Multi-Tenant Monolith with Role-Based Access Control (RBAC)

---

## 🚀 Future Enhancements

* Add **Manager-level intermediate approval** layers.
* Implement **Notification Service** for approvals and rejections.
* Integrate **audit trail dashboards** for analytics.
* Enhance **security** with JWT-based authentication and tenant-level isolation.

---

## 🧾 Conclusion

This repository encapsulates a robust multi-tenant approval system combining organizational role management, workflow automation, and integration readiness with external systems like MyBiz. It demonstrates an enterprise-ready design balancing flexibility, scalability, and security, ideal for corporate travel, procuremen
