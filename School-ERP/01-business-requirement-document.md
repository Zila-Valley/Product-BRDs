# Business Requirement Document (BRD)
## School / College / Institute ERP Product

### 1. Executive Summary
This document outlines the business requirements for the School / College / Institute ERP system. The system is designed to streamline and automate the entire lifecycle of educational institution management, spanning from admission and academics to finance, human resources, and parent-student communication. This product aims to serve as a comprehensive, multi-tenant SaaS platform catering to single schools, multi-branch colleges, and coaching institutes.

### 2. Product Overview
The ERP is a unified digital platform built with a .NET Core 10 backend, React 19 frontend, and Flutter mobile application. It digitizes paper-based or disjointed digital processes into a single source of truth, enabling role-based access, automated notifications, comprehensive reporting, and secure data isolation across different branches or institutes.

### 3. Business Goals
- **Operational Efficiency:** Automate repetitive administrative tasks (e.g., fee collection, attendance tracking).
- **Data Centralization:** Maintain a single, secure repository for all student, staff, and financial data.
- **Enhanced Communication:** Bridge the gap between parents, students, and teachers via real-time portals and a mobile app.
- **Scalability:** Support multi-branch or multi-institute setups within a single deployment instance (SaaS model).
- **Financial Transparency:** Provide robust fee and accounting management with strict branch-level data isolation.

### 4. Target Customers
- **Schools:** K-12 institutions requiring standard academic, attendance, and fee workflows.
- **Colleges/Universities:** Higher education institutions requiring advanced admission processes, complex fee structures, and specialized syllabus management.
- **Coaching Institutes:** Private tutoring centers focused on batch management, attendance, and recurring fee payments.
- **Educational Trusts:** Organizations managing multiple branches or schools under a single administrative umbrella.

### 5. Problems Solved
- Manual and error-prone fee calculations and receipt generation.
- Fragmented communication between teachers, parents, and students.
- Inefficient paper-based admission and inquiry processes.
- Lack of real-time insights and analytics for institution administrators.
- Cross-institute data leakage in multi-branch setups.

### 6. Existing Manual Process
Currently, many target institutes rely on a mix of Excel spreadsheets, legacy desktop software, paper registers, and disparate communication tools (e.g., WhatsApp groups). This leads to data silos, delays in fee collection, miscommunication, and a high administrative burden on staff.

### 7. Proposed ERP System
A cloud-native, responsive web and mobile application that integrates all core modules:
- **Web App:** Used by Super Admin, Institute Admins, Teachers, Accountants, and clerical staff.
- **Mobile App:** Primary touchpoint for Parents and Students for notifications, attendance, homework, and fee payments.
- **Backend API:** Centralized logic, robust RBAC (Role-Based Access Control), and data isolation logic using .NET Core.

### 8. Key Stakeholders
- **Product Owner/Manager:** Defines roadmap and feature prioritization.
- **Business Analysts:** Translates customer needs into system features.
- **Development & QA Team:** Builds and ensures the quality of the software.
- **Implementation & Support Team:** Onboards institutes and resolves post-go-live issues.
- **End-User Institutes:** The paying customers (Trusts, Schools, Colleges).

### 9. User Personas
- **Super Admin:** Manages global settings, clients/institutes, subscriptions, and system-wide configurations.
- **Client Admin:** Manages global configurations for a specific educational trust (multiple branches).
- **Institute Admin / Principal:** Full control over a single school/branch operations, reports, and staff.
- **Teacher:** Manages class attendance, homework, exam marks, and direct student communication.
- **Accountant:** Handles fee structures, fee collection, vouchers, and financial ledgers.
- **Receptionist / Front Office:** Manages inquiries, visitor logs, and basic admissions.
- **Student:** Accesses timetable, homework, study materials, and exam results.
- **Parent:** Tracks child's attendance, pays fees online, communicates with teachers, and receives circulars.
- **Transport Staff / Manager:** Manages vehicle routes, allocations, and fuel/maintenance logs.
- **Librarian:** Manages book catalog, issues/returns, and library fines.
- **Hostel Warden:** Manages room allocations, hostel attendance, and student discipline.

### 10. High-Level Functional Requirements
- **Multi-Tenant / Branch Support:** Data must be strictly isolated per institute/branch.
- **Authentication & RBAC:** Secure login with JWT, supporting granular permissions (Module-level, Action-level).
- **Admissions & Enquiries:** Lead capture, application processing, and automated student creation.
- **Academics:** Timetable, subjects, syllabus mapping, and attendance (manual + biometric integration).
- **Finance:** Fee collection, concessions, transport/hostel fees, and general accounting ledgers.
- **HR & Payroll:** Staff attendance, leave management, and role-wise compensation.
- **Communication:** SMS, Email, WhatsApp, and in-app push notifications.
- **AI Integrations:** Automated OMR grading, AI-generated lesson plans and homework, math-to-LaTeX conversion, and high-performance OCR.

### 11. High-Level Non-Functional Requirements
- **Performance:** APIs must respond within 200ms under standard load. UI should load under 2 seconds.
- **Security:** HTTPS for all traffic. Passwords securely hashed. Prevent SQL Injection, XSS, and Cross-Tenant Data Access.
- **Availability:** 99.9% uptime target.
- **Scalability:** Must support thousands of concurrent mobile app users during peak times (e.g., result declaration days).
- **Usability:** Intuitive UI/UX requiring minimal training for end-users.

### 12. Assumptions
- Institutions have reliable internet access for web users.
- Parents/Students have smartphones capable of running iOS/Android apps.
- SMS/Email/WhatsApp gateway accounts are procured by the institute or handled via the SaaS platform.

### 13. Dependencies
- Third-party payment gateways for online fee collection.
- Communication gateways (Msg91, MailGun, WhatsApp Business API).
- Biometric device APIs/webhooks for automated staff/student attendance.

### 14. Constraints
- Regulatory compliance regarding student data privacy (varies by region).
- Legacy data migration challenges from diverse older systems.

### 15. Business Benefits
- **Revenue Growth:** Better lead tracking in admissions and strict fee collection reduces revenue leakage.
- **Cost Reduction:** Less paper usage, reduced administrative overhead.
- **Brand Value:** Offering a modern mobile app improves the institution's tech-forward image.

### 16. Success Metrics
- **Adoption Rate:** Percentage of parents actively using the mobile app within 30 days of launch.
- **Customer Retention:** High subscription renewal rate for B2B clients (institutes).
- **System Stability:** Number of critical bug reports per month.

### 17. Out of Scope Items
- Custom hardware integration other than standard biometric webhooks.
- Deep integration with governmental education portals (unless explicitly mapped later).

### 18. Future Scope
- AI-driven analytics for student performance prediction.
- Integrated LMS (Learning Management System) with video streaming.
- Alumni network management.
