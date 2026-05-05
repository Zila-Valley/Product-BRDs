# QA Test Plan

## 1. Testing Objectives
To ensure that Sales Booster CRM functions as per the business requirements, is free of critical defects, maintains data integrity across modules, and performs well under expected loads.

## 2. Scope
- **In-Scope**: Authentication, Lead Management, Attendance Tracking, Sales & Target Management, Internal Chat (SignalR), Background Jobs.
- **Out-of-Scope**: Third-party SMS/WhatsApp gateway physical delivery (mocked instead), server infrastructure setup.

## 3. Test Environments
- **DEV**: Local developer machines connected to local SQL Server.
- **UAT**: Staging environment matching production architecture used for client sign-off.
- **PROD**: Live environment.

## 4. Test Scenarios (Functional)

| Test Case ID | Module | Scenario | Steps | Expected Result | Priority |
|---|---|---|---|---|---|
| TC-01 | Auth | Valid Login | 1. Enter valid email/pwd. 2. Click Login. | User is redirected to Dashboard. Token stored. | High |
| TC-02 | Auth | Invalid Login | 1. Enter invalid pwd. 2. Click Login. | "Invalid credentials" error displayed. | High |
| TC-03 | Leads | Create Lead | 1. Go to Leads. 2. Click Add. 3. Fill required fields. 4. Save. | Lead appears in list. DB updated. | High |
| TC-04 | Leads | Required Validation | 1. Leave Name blank. 2. Save. | Validation error on Name field. | Medium |
| TC-05 | Attendance | Daily Check-in | 1. Click Check-in. 2. Allow Location. | Check-in successful. Coords saved. | High |
| TC-06 | Attendance | Selfie Verification | 1. Attempt Check-in without camera permission. | Check-in blocked until camera allowed. | Medium |
| TC-07 | Sales | Add Sale | 1. Select Client/Lead. 2. Enter Amount. 3. Save. | Sale recorded. Target progress updated. | High |
| TC-08 | Chat | Send Message | 1. Open Workspace. 2. Type msg. 3. Send. | Message appears immediately for receiver. | High |
| TC-09 | Chat | Attach File | 1. Select File. 2. Send. | File uploads successfully, accessible via link. | Medium |

## 5. API Test Scenarios
- Verify all endpoints return `401 Unauthorized` when called without a valid JWT.
- Verify `403 Forbidden` if a user attempts to access an endpoint they lack `Permission` for.
- Verify `GET` endpoints support pagination (skip/take parameters).
- Verify data type validation (e.g., passing a string to an integer field returns `400 Bad Request`).

## 6. UI Test Scenarios
- Responsive design: Verify Sidebar collapses correctly on mobile/tablet view.
- Data Tables: Verify sorting, pagination, and filtering work without crashing.
- Modals: Verify clicking outside the modal or pressing `ESC` closes it (if intended).
- Loading States: Verify spinners appear during API calls to prevent double-submissions.

## 7. Role/Permission Test Scenarios
- Login as `Sales Executive`: Verify Admin settings (Module/Role management) are completely hidden from UI.
- Direct URL Access: As `Sales Executive`, manually type `/role-management` in URL -> Verify redirect to Dashboard or Unauthorized page.

## 8. Background Job Testing
- **Attendance Job (10 AM/11 AM)**: Manually trigger the Quartz job via Hangfire dashboard -> Verify `EmployeeAttendance` records are correctly generated for the day.
- **Salary Generation**: Trigger 1st of month job -> Verify `MonthlySalary` rows are created based on `SalaryRevision`.

## 9. Bug Reporting Template
When logging bugs in Jira/Trello, use the following format:
**Title**: [Module] Short description of issue
**Environment**: (DEV/UAT/PROD)
**Steps to Reproduce**:
1. 
2. 
**Expected Result**: What should have happened.
**Actual Result**: What actually happened (include screenshots/console errors).
**API Response**: (If applicable, include network tab payload).
