# Business Requirements Document (BRD) - School ERP System

## 1. Executive Summary
This document serves as the comprehensive Business Requirements Document (BRD) for the School Enterprise Resource Planning (ERP) system. The system is designed to digitize, streamline, and centralize the operational, academic, and financial workflows of a multi-branch educational institution. By offering role-based access to administrators, teachers, students, parents, and support staff, the platform aims to enhance communication, reduce manual administrative overhead, and provide actionable insights into school performance.

## 2. System Overview
The School ERP is a cloud-based application architected as a Multi-Tenant SaaS solution (capable of handling multiple clients/schools and their respective branches). 
- **Backend Infrastructure:** .NET Core RESTful API utilizing Entity Framework Core with PostgreSQL.
- **Frontend Infrastructure:** React Vite single-page application (SPA).
- **Core Capabilities:** Academic management, admissions, fee collection, HR/payroll, examinations, library, transport, hostel, and inventory management.

## 3. Stakeholders
- **School Management / Trustees:** Require high-level dashboards, financial oversight, and multi-branch management.
- **Principal / Administrators:** Need control over daily operations, academic planning, and staff management.
- **Teachers:** Require tools for attendance marking, homework assignment, syllabus tracking, and exam grading.
- **Students & Parents:** Need portals for viewing academic progress, paying fees, tracking attendance, and accessing notices.
- **Non-Teaching Staff (Accountants, Librarians, Transport/Hostel Wardens, Receptionists):** Require dedicated modules to manage their specific operational domains.

## 4. User Roles & Permissions
The system employs a robust Role-Based Access Control (RBAC) mechanism.

| Role | Core Responsibilities & Access |
| :--- | :--- |
| **SuperAdmin** | System-wide configuration, tenant (client) onboarding, and global settings. |
| **ClientAdmin / BranchAdmin** | Full control over a specific school/branch, including module configuration and user management. |
| **Principal** | Academic oversight, staff attendance monitoring, and administrative reporting. |
| **Teacher** | Class/Section management, student attendance, homework grading, and exam marks entry. |
| **Student** | Viewing timetables, homework, exam results, and accessing the online exam portal. |
| **Parent** | Viewing child's progress, fee payment history, attendance, and communication with teachers. |
| **Accountant** | Fee collection, concession approvals, voucher entry, and financial ledger management. |
| **HR** | Staff management, leave approvals, and payroll processing. |
| **Librarian** | Book cataloging, issuing, reservations, and fine management. |
| **Receptionist / FrontOffice** | Managing inquiries, visitor logs, postal logs, and phone call logs. |
| **Hostel Warden** | Room allocation, hostel attendance, and swap requests. |
| **Transport In-charge** | Route planning, vehicle allocation, maintenance logging, and fuel tracking. |

## 5. Module-wise Breakdown

### 5.1 Administration & Core Setup
- Tenant and Branch Management
- Academic Year Configuration
- Role and Permission Management (RBAC)
- Department and Designation Setup

### 5.2 Admission Management
- Pre-admission Inquiries and Follow-ups
- Admission Application Processing
- Document Verification
- Student Enrollment

### 5.3 Academic & SIS (Student Information System)
- Class, Section, and Subject mapping
- Timetable and Class Routine generation
- Syllabus and Topic Completion tracking
- Student and Parent profiling

### 5.4 Human Resources & Payroll
- Staff Profiling and Directory
- Staff Attendance Tracking
- Leave Management
- Payroll calculation and Role-wise compensation

### 5.5 Fee Management & Finance
- Fee Head and Structure configuration
- Fee Collection, Installments, and Fines
- Fee Concession Requests
- Financial Accounting (Ledgers, Vouchers)

### 5.6 Examination Management
- Exam Scheduling and Grouping
- Admit Card generation
- Offline Exam Marks Entry and Tabulation Sheets
- Online Examination Portal (Configs, Attempts, Results)

### 5.7 Support Modules (Library, Transport, Hostel, Inventory)
- **Library:** Books catalog, Issue/Renewal, Member tracking.
- **Transport:** Routes, Stops, Allocations, Vehicle Maintenance.
- **Hostel:** Room Types, Allocation, Attendance.
- **Inventory/Assets:** Suppliers, Item Stocks, Issues to staff/students.

### 5.8 Front Office & Communication
- Notice Board and Notifications (Email/SMS/WhatsApp templates)
- Visitor Management
- Postal and Call Logs

## 6. Detailed Functional Requirements (FRs)

> [!NOTE]
> The following functional requirements are inferred based on the backend API controllers and database models.

### FR-1: Admission & Student Onboarding
- **FR-1.1:** The system shall allow front office staff to record admission inquiries.
- **FR-1.2:** The system shall allow administrators to process admission applications, upload student documents, and enroll students into specific classes/sections.
- **FR-1.3:** The system shall auto-generate student credentials upon successful admission.

### FR-2: Academic Planning
- **FR-2.1:** Administrators shall be able to define Academic Years, Classes, and Sections.
- **FR-2.2:** Teachers shall be able to track syllabus progress by marking topics as completed.
- **FR-2.3:** The system shall support the generation and viewing of class timetables (routines).

### FR-3: Attendance Management
- **FR-3.1:** Teachers shall be able to mark daily or subject-wise attendance for students in their assigned sections.
- **FR-3.2:** HR/Admins shall be able to log daily attendance for staff members.
- **FR-3.3:** The system shall support specialized attendance tracking for hostel residents.

### FR-4: Fee Management
- **FR-4.1:** The accountant shall be able to define fee structures based on academic structures, classes, and fee groups.
- **FR-4.2:** The system shall automatically calculate fee installments and late fines.
- **FR-4.3:** Students/Parents shall be able to submit fee concession requests, subject to Admin approval.
- **FR-4.4:** The system shall generate fee receipts for all fee transactions.

### FR-5: Examination & Grading
- **FR-5.1:** Teachers shall be able to enter offline exam marks against specific subjects and exam schedules.
- **FR-5.2:** The system shall auto-generate tabulation sheets and final results based on predefined grading schemas.
- **FR-5.3:** The system shall support an online examination portal where students can attempt quizzes and view instant results.

### FR-6: HR & Payroll
- **FR-6.1:** The system shall allow HR to manage leave types and process staff leave requests.
- **FR-6.2:** The system shall generate payroll based on role-wise compensation, staff attendance, and leave deductions.

## 7. User Workflows

### 7.1 Student Admission Workflow
1. **Inquiry:** Parent visits/calls school; Receptionist logs an `AdmissionInquiry`.
2. **Application:** Parent submits an `AdmissionApplication` with required `AdmissionDocuments`.
3. **Review:** Admin reviews application and documents.
4. **Enrollment:** Upon approval, Admin converts application to a `Student` profile, assigns a `Branch`, `Class`, and `Section`.
5. **Fee Allocation:** System automatically allocates the relevant `FeeStructure` to the `StudentFee` ledger.

### 7.2 Fee Collection Workflow
1. **Invoice Generation:** System generates scheduled fee dues (Installments) based on the Academic Year.
2. **Payment:** Parent logs into portal or visits accountant.
3. **Transaction:** Accountant records payment in `FeeTransactions`; system applies any `FeeFines` or approved `FeeConcessionRequests`.
4. **Receipt:** System updates `StudentFee` balance and generates a receipt.
5. **Accounting:** System logs a `VoucherEntry` in the `LedgerAccounts`.

### 7.3 Online Examination Workflow
1. **Configuration:** Teacher creates an `OnlineExamConfig` and populates it from the `QuestionBank`.
2. **Scheduling:** Exam is scheduled and linked to specific classes/sections.
3. **Attempt:** Student logs into the `StudentExamPortal`, starts a `StudentExamAttempt`, and submits `StudentExamAnswers`.
4. **Evaluation:** System auto-evaluates objective questions and stores data in `OnlineExamResults`.

## 8. Data Model Overview

### 8.1 Multi-Tenancy Architecture
- **Global Filters:** Almost all core operational entities implement `IMultiTenant` (`ClientId`), `IAcademicYearFilter` (`AcademicYearId`), and `IBranchFilter` (`BranchId`). This ensures strict data isolation between different schools, branches, and academic sessions.

### 8.2 Key Entity Relationships
- `Client` (1) ↔ (M) `Branch`
- `ApplicationUser` (1) ↔ (M) `ApplicationRole` (via IdentityUserRole)
- `Class` (1) ↔ (M) `Section` (1) ↔ (M) `Student`
- `Student` (1) ↔ (M) `StudentFee` (1) ↔ (M) `FeeTransaction`
- `Exam` (1) ↔ (M) `ExamSchedule` ↔ (M) `ExamMark`

## 9. Business Rules (Inferred)
1. **Tenant Isolation:** A user belonging to Client A cannot access data from Client B (enforced via EF Core Global Query Filters).
2. **Academic Year Context:** Most transactions (Fees, Attendance, Exams) are strictly bound to the active Academic Year. Historical data requires switching the academic year context.
3. **Managerial Hierarchy:** `ApplicationUser` has a self-referencing `ManagerId`, indicating a reporting hierarchy (e.g., Teachers report to Principal).
4. **Fee Concessions:** Concessions must go through a request-approval workflow (`FeeConcessionRequest`) before altering the fee ledger.
5. **Role-Module Mapping:** Access to specific application modules (and API endpoints) is dynamically controlled by `RoleModule` and `RolePermission` tables, rather than hardcoded authorizations.

## 10. Non-Functional Requirements (NFRs)
- **Security:** Standard ASP.NET Core Identity with JWT Refresh Token architecture (`ApplicationUserRefreshToken`). 
- **Scalability:** PostgreSQL database utilized, capable of handling large-scale multi-tenant data.
- **Performance:** Bulk operations are supported for data imports (`BulkImportTask`), indicating optimization for large school setups.
- **Auditability:** Entities inherit from `IBaseEntity` tracking `CreatedAt`, `CreatedBy`, `UpdatedAt`, and `UpdatedBy`. `RecentActivity` and `ExceptionLog` tables indicate a focus on system auditing.

## 11. Assumptions
- **Frontend Routing matches Backend Roles:** It is assumed that the React routing (`/pages/school/admin`, `/hr`, `/student`, etc.) strictly relies on backend role validation to prevent unauthorized access.
- **Data Deletion:** The system primarily uses Soft Deletes (implied by typical `IsActive` flags on `IBaseEntity` and `DeleteBehavior.Restrict` mappings) to preserve historical financial and academic records.

## 12. Gaps & Risks

> [!WARNING]
> The following technical and business risks were identified during architectural analysis.

1. **Cascade Deletion Risks:** While most EF Core relations are set to `DeleteBehavior.Restrict`, `RoleModule` and `RolePermission` use `DeleteBehavior.Cascade`. Deleting a Role will instantly wipe all its permissions without warning.
2. **Missing Validations (Assumed):** Complex business rules like "Cannot issue a library book if the student has unpaid fines" or "Cannot take exam if fees are pending" are often missed in basic CRUD APIs. These need rigorous QA testing.
3. **Concurrency Issues in Inventory & Fees:** Without explicit concurrency tokens or database locks, simultaneous fee payments or library book issues could result in race conditions.
4. **Hardcoded Roles:** While the database has dynamic roles, the existence of `AppRoles.cs` with constants (e.g., `AppRoles.SuperAdmin`) suggests that certain business logic is tightly coupled to hardcoded string values, limiting complete role dynamicism.

## 13. Recommendations for Improvement
- **Notification Engine Standardization:** Unify `CommunicationLogs`, `PostalLogs`, and `TestWhatsAppController` into a centralized, event-driven notification microservice to handle Email, SMS, WhatsApp, and Push Notifications.
- **API Standardization:** Ensure all API controllers utilize standardized pagination, filtering, and sorting DTOs, as list endpoints tend to degrade in performance as a school's data grows.
- **Implement Caching:** Master data (Academic Years, Classes, Fee Heads, Roles) should be cached using Redis or In-Memory caching to reduce PostgreSQL database hits on every request.
- **Missing Module - Alumni Management:** Consider adding a module to track graduated students for networking and donation purposes.
- **UX Improvement:** Provide a unified "Dashboard" for Parents encompassing Fees, Attendance, and Exam Results in a single glance, rather than requiring navigation through separate modules.
