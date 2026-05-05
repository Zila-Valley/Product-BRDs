# QA Test Plan

## 1. Test Strategy Overview
The School ERP system will be tested across three main layers: API (Backend), Web UI (Frontend), and Mobile Application. Given the multi-tenant architecture, data isolation is the highest priority testing focus.

## 2. Test Environments
- **DEV:** `dev.api.schoolerp.local` (Unit testing, fast feedback).
- **UAT:** `uat.schoolerp.local` (Integration testing, client beta testing).
- **PROD:** `api.schoolerp.com` (Post-deployment smoke testing).

## 3. Web UI Testing Scenarios
### Critical Business Workflows
- **Login & RBAC:** Verify Super Admin, Institute Admin, and Teacher logins redirect to correct dashboards with restricted menu items.
- **Student Admission:** Complete the full flow from Inquiry -> Admission Form -> Fee Payment -> Student ID generation.
- **Fee Collection:** Process a partial payment, verify balance updates, and ensure the generated receipt matches the payment.
- **Attendance Entry:** Mark a class absent, save, and verify reports update correctly.
- **Exam Results:** Create an exam, assign marks, and generate the final report card PDF.

## 4. API Testing Scenarios
- **Token Validation:** Ensure endpoints reject requests without a JWT or with expired tokens.
- **Data Isolation (Critical):** Log in as `Admin_Branch_A`. Attempt to retrieve or modify data using `ID`s belonging to `Branch_B`. The API must return a 404 or 403, and never return Branch_B data.
- **Data Validation:** Pass invalid payload to `/api/Students` (e.g., missing required names or negative fee amounts) and verify proper 400 Bad Request responses.

## 5. Mobile App Testing Scenarios
- **Device Compatibility:** Test on Android 10+ and iOS 15+.
- **Authentication:** Test login, persistent sessions (shared_preferences), and forced logout on token expiry.
- **Offline Capabilities:** Disconnect internet and verify cached timetable and notice board load via Isar DB.
- **Push Notifications:** Send a notice from Web UI and verify push notification appears on the mobile device within 5 seconds.

## 6. Regression Checklist
- [ ] Database migrations applied successfully on a fresh database.
- [ ] Fee calculations are accurate across complex discount scenarios.
- [ ] Super Admin can still view all branches without isolation errors.
- [ ] Reports export correctly to PDF and Excel formats.
