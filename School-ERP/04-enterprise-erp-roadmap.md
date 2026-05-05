# Enterprise ERP Roadmap

## Epics & User Stories

### Epic 1: Multi-Branch & SaaS Stabilization
- **US-1.1:** As a Super Admin, create a new Client to manage multiple branches.
- **US-1.2:** As a Client Admin, view an aggregated dashboard of all branches.
- **US-1.3:** As an Institute Admin, isolate staff views to specific branches.

### Epic 2: Comprehensive Fee Management
- **US-2.1:** Automatically apply sibling discounts.
- **US-2.2:** Support online fee payment via the mobile app.
- **US-2.3:** Generate multi-part fee receipts for offline payments.

### Epic 3: Mobile App Parent/Student Portal
- **US-3.1:** Receive instant push notifications for absences.
- **US-3.2:** View and download daily homework assignments.
- **US-3.3:** Track live location of the school bus.

### Epic 4: Advanced Academic & Examination
- **US-4.1:** Enter marks via a grid-view UI for fast entry.
- **US-4.2:** Design custom report card templates.
- **US-4.3:** Support objective online quizzes directly from the student portal.

## Jira Ticket Backlog

| Ticket ID | Type | Priority | Module | Title | Description |
|---|---|---|---|---|---|
| ERP-101 | Epic | High | Architecture | Cross-Branch Data Aggregation | Dashboard for Client Admins. |
| ERP-102 | Story | High | Security | Role Permission Matrix UI | Matrix UI for module permissions. |
| ERP-103 | Story | High | Finance | Automated Tax (TDS) in Payroll | Deduct TDS based on percentage slabs. |
| ERP-104 | Story | Medium | Transport | Live GPS Bus Tracking | Third-party GPS hardware API integration. |
| ERP-105 | Bug | High | Admissions | Inquiry Form State Loss | Save draft data locally before submission. |
| ERP-106 | Improvement | Medium | UI/UX | Dark Mode Support | Tailwind dark variants integration. |
| ERP-107 | Task | High | DevOps | Automated DB Backups | Configure daily PostgreSQL dumps. |
| ERP-108 | Story | Low | Academics | AI Timetable Generator | Generate schedules without teacher conflicts. |
| ERP-109 | Bug | High | Core | N+1 Query in Student List | Use `.Include(s => s.Class)` for eager loading. |
| ERP-110 | Story | Medium | Mobile | Push Notification Service | Firebase Cloud Messaging (FCM) integration. |
