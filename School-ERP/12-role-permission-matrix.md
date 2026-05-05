# Role & Permission Matrix

## 1. Overview
The School ERP utilizes a granular, policy-based Role-Based Access Control (RBAC) system. Every module has specific actions (`Create`, `Read`, `Update`, `Delete`, `Approve`, `Export`) that are defined as unique permissions. These permissions are grouped into Roles, which are then assigned to Users.

## 2. Standard System Roles
The system seeds the following default roles, though Institute Admins can create custom roles dynamically.

- **Super Admin:** Global access. Manages subscriptions, clients, and global settings. Bypasses branch filters.
- **Client Admin:** Manages multiple branches under a single educational trust. Can view aggregated reports.
- **Institute Admin / Principal:** Full administrative access, but strictly scoped to their assigned `BranchId`.
- **Teacher:** Access to their assigned classes, timetable, attendance, marks entry, and homework.
- **Accountant:** Full access to the Fee module, Vouchers, and Ledgers. No access to academic modifications.
- **Receptionist / Front Office:** Access to Inquiries, Visitor Logs, and basic Admissions.
- **Student / Parent:** Portal/Mobile app access. View-only access to their specific profile, timetable, homework, and fee payment.

## 3. Permission Matrix

The table below outlines the default mapping of standard permissions to core system roles. *(Note: ✓ indicates Full Access, 👁 indicates Read-Only Access, and - indicates No Access).*

| Module / Action | Super Admin | Institute Admin | Teacher | Accountant | Receptionist |
|---|---|---|---|---|---|
| **Identity & Access** | | | | | |
| Manage Roles & Permissions | ✓ | ✓ | - | - | - |
| Manage Staff Users | ✓ | ✓ | - | - | - |
| **Academic Master** | | | | | |
| Manage Classes & Sections | ✓ | ✓ | 👁 | - | - |
| Manage Timetable | ✓ | ✓ | 👁 | - | - |
| **Admissions** | | | | | |
| Manage Inquiries | ✓ | ✓ | - | - | ✓ |
| Approve Admissions | ✓ | ✓ | - | - | - |
| **Student Management** | | | | | |
| View Student Profiles | ✓ | ✓ | ✓ (Assigned Only) | 👁 | 👁 |
| Edit Student Profiles | ✓ | ✓ | - | - | - |
| **Attendance** | | | | | |
| Mark Student Attendance | ✓ | ✓ | ✓ | - | - |
| View Attendance Reports | ✓ | ✓ | ✓ | - | - |
| **Fees & Finance** | | | | | |
| Configure Fee Structures | ✓ | ✓ | - | ✓ | - |
| Collect Fees | ✓ | ✓ | - | ✓ | - |
| View Financial Ledgers | ✓ | ✓ | - | ✓ | - |
| Delete/Reverse Transactions | ✓ | ✓ | - | - | - |
| **Examinations** | | | | | |
| Manage Exam Schedules | ✓ | ✓ | 👁 | - | - |
| Enter Marks | ✓ | ✓ | ✓ | - | - |
| Publish Results | ✓ | ✓ | - | - | - |
| **HR & Payroll** | | | | | |
| Approve Leaves | ✓ | ✓ | - | - | - |
| Generate Payroll | ✓ | ✓ | - | 👁 | - |
| **Communication** | | | | | |
| Send Bulk SMS/Email | ✓ | ✓ | - | - | - |
| Post Notice Board | ✓ | ✓ | ✓ | - | 👁 |

## 4. Technical Implementation Notes
- **Policies:** In the backend, these permissions map to Policies using the `PermissionEnum` (e.g., `[Authorize(Policy = "perm:ManageFees|RequireAll=True")]`).
- **Dynamic Roles:** Since the `RolePermissions` table allows dynamic mapping, a school can create a hybrid role (e.g., "Accountant + Receptionist") simply by checking the required boxes in the web UI.
- **Contextual Access:** Even with permission (e.g., "View Student Profiles"), the user's `BranchId` is always injected into the EF Core query filter. A user can only execute their permissions on data within their designated branch.
