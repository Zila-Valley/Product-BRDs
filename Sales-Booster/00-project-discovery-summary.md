# Project Discovery Summary

## Product Purpose
Sales Booster CRM is a comprehensive Customer Relationship Management and Sales Tracking system designed to streamline sales operations, track lead pipelines, manage employee attendance, manage expenses and travel, and facilitate communication among team members. It also acts as a basic HRMS platform to handle employee shifts, leaves, and salary processing.

## Existing Modules (Discovered from Code)
1. **User & Employee Management**: Management of application users and detailed employee records (Personal details, education, reporting hierarchy).
2. **Client Management**: Onboarding and managing clients, assigning business units and primary users.
3. **Organization Structure**: Setup of Business Units, Divisions, and Departments.
4. **Role-Based Access Control (RBAC)**: Fine-grained permissions, roles, and module access control.
5. **Lead & Customer Management**: Lead capturing, categorization (LeadCompanyCategory), assignment to employees and business units, status tracking, and conversion tracking.
6. **Sales & Collection Management**: Sales target setting, collection targets, tracking individual sales transactions, and recording collections.
7. **Attendance & Leave Management**: Check-in/check-out tracking, geo-location tracking, selfie check-in, late/early flags, shift management, leave policies, and holiday calendar.
8. **Expense & Travel Management**: Travel logs, travel summaries, expense tracking, and categorization.
9. **HR & Payroll**: Salary structure setups, salary revisions, and monthly salary generation.
10. **Communication & Collaboration**: Real-time messaging (SignalR Hubs), Channels, Workspaces, pinned messages, reactions, mentions, and delivery statuses.
11. **Calendar & Tasks**: Calendar events, task tracking, and recent activity logs.
12. **Reporting**: Dashboard analytics, reporting exports, exception logs, and job execution logs.

## Existing User Roles
Roles are managed dynamically via the `ApplicationRole` entity, however, implied roles based on the hierarchy and models include:
- System Admin
- Client Admin / Primary User
- Manager / Team Lead
- HR Admin
- Sales Executive / Employee

## Existing Business Flows
- **Lead Flow**: Capture Lead -> Assign to Employee -> Track Status (LeadStatus: New, etc.) -> Convert to Sale.
- **Sales Flow**: Product definition -> Set Targets -> Log Sale Transaction -> Track Collections against Sales.
- **Attendance Flow**: Shift Definition -> Daily Check-in/out (with Lat/Long) -> EOD Work Update -> Attendance Calculation (Late, Early, Payable Hours) -> Leave Adjustment.
- **Expense Flow**: Submit Expense/Travel Log -> Approval (implied) -> Reimbursement tracking.
- **Salary Flow**: Base Salary / Revision config -> Monthly Salary Generation job (Quartz/Hangfire).
- **Communication Flow**: Workspaces -> Channels -> Messages -> Mentions / Reactions -> Real-time delivery via SignalR.

## Existing API Capabilities (.NET Core 8)
- Complete CRUD for all entities.
- JWT-based Authentication with Refresh Tokens.
- SignalR hubs for real-time messaging.
- Hangfire & Quartz for scheduled jobs (Salary generation, attendance generation, daily travel summary, target renewals).
- File upload handling for attachments and message attachments.
- Global Exception handling and logging (Serilog).
- Integration stubs (WhatsApp Webhook, Email Sender - Mailgun/SendGrid, SMS - Msg91).

## Existing Frontend Screens (React + Vite)
- Authentication: Login, Signup, Forgot Password, Reset Password.
- Dashboard: Main analytics dashboard and specific attendance tracking map.
- Administration: Users, RBAC (Roles, Modules, Permissions), Settings.
- Organization: Business Units, Departments, Clients, Country/State/District masters.
- HRMS & Attendance: Leave Requests, Attendance List, Attendance Settings, Shift Settings, Holiday Calendar, Absent Employees.
- CRM & Sales: Leads, Targets, Sales List, Product & Category management.
- Expense & Travel: Expense List, Expense Types.
- Communication: Messaging Layout, My Messages.
- System Logs: Exception Logs, Job Logs.

## Current Technical Stack
**Backend**:
- .NET Core 8.0 Web API
- Entity Framework Core (SQL Server)
- Identity Framework for User/Role management
- Hangfire & Quartz.NET for Background Jobs
- SignalR for WebSockets
- QuestPDF for PDF generation
- Serilog for Logging
- AutoMapper for DTO mapping

**Frontend**:
- React 18+ (with Vite)
- React Router DOM
- TailwindCSS (implied by typical Vite setups, and `.env` configs)
- Axios for API calls
- State Management: Context API / Local State

## Current Gaps and Risks
- **Testing**: No visible automated test suites (Unit or Integration tests) in the standard project structure.
- **CRM Completeness**: Core CRM features like Opportunity Pipeline stages, Email integration for leads, and Quotation generation appear limited or missing.
- **Tight Coupling**: Monolithic architecture could become difficult to maintain as the module count (HR, CRM, Chat) grows.
- **Database Scalability**: Heavy tracking of real-time lat/long and chat messages may bloat the SQL database quickly if archiving or NoSQL isn't used for high-volume logs.
- **Documentation**: Inline code documentation and API XML comments are sparse or missing.
