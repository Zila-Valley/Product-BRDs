# Lead Management Module

## Purpose
To capture, categorize, and track prospective customers from initial contact to conversion.

## Business Users
- Sales Executives
- Sales Managers

## Features Implemented
- Create and edit leads.
- Categorize leads using `LeadCompanyCategory`.
- Assign leads to specific employees and business units.
- Track basic status changes via an Enum (`LeadStatus`).

## Screens Found in React
- `/leads` -> `LeadsPage.jsx`

## APIs Found in .NET Core
- `LeadsController.cs` (`GET`, `POST`, `PUT`, `DELETE` on `/api/leads`)
- `LeadCompanyCategoryController.cs`

## Database Entities Used
- `Lead`
- `LeadCompanyCategory`

## Business Workflow
1. Lead is captured via UI (or imported/API).
2. Assigned to a `BusinessUnit` and an `Employee`.
3. Sales Executive contacts the lead and updates the `Status`.
4. If successful, Lead is converted to a `Client` and a `Sale` transaction is logged.

## Field List (Lead Entity)
- `Name` (string)
- `Address` (string)
- `Mobile` (string)
- `Email` (string)
- `Purpose` (string)
- `Latitude` (double)
- `Longitude` (double)
- `Status` (Enum: LeadStatus)
- `EmployeeId` (Guid, FK)
- `BusinessUnitId` (Guid, FK)
- `ClientId` (Guid, Nullable FK)
- `Remarks` (string)

## Validation Rules
- `Name` and `Mobile` are typically mandatory.
- Must be assigned to an active `EmployeeId` and `BusinessUnitId`.

## Role/Permission Rules
- Sales Executives can view leads assigned to their `EmployeeId`.
- Managers can view leads for their entire `BusinessUnit`.
- Configured via `PermissionAuthorizationHandler` and RBAC setup.

## Current Gaps
- No Kanban board for visual pipeline management.
- No auto-assignment rules based on geography/load.
- No lead scoring mechanism.

## Recommended Improvements
- Implement drag-and-drop Kanban view on frontend.
- Add `LeadSource` tracking to measure marketing ROI.

## Acceptance Criteria
- User can create a new lead successfully with valid data.
- System prevents saving a lead without an assigned Employee or Business Unit.
- Only authorized users can delete a lead.

## Test Scenarios
1. **Create Lead**: Verify successful creation with all mandatory fields.
2. **Status Update**: Verify a Sales Rep can change status from New to In Progress.
3. **Data Isolation**: Verify Sales Rep A cannot see Leads assigned to Sales Rep B (unless Rep A is a manager).
