# User & Role Management Module

## Purpose
To manage authentication, authorization, organization hierarchy, and granular access control (RBAC).

## Business Users
- System Admins
- HR Admins

## Features Implemented
- User Registration & Authentication (JWT).
- Role Management (ApplicationRole).
- Permission and Module Management.
- Mapping Users to Employees (HR Data).
- Setting up Reporting Hierarchies (`ManagerId`).

## Screens Found in React
- `/employees` -> `UsersPage.jsx`
- `/employee-details/:id` -> `UserDetailsPage.jsx`
- `/role-management` -> `RolesManagementPage.jsx`
- `/module-management` -> `ModuleManagementPage.jsx`
- `/permission-management` -> `PermissionManagementPage.jsx`

## APIs Found in .NET Core
- `AuthController.cs`
- `UsersController.cs`
- `EmployeesController.cs`
- `RolesController.cs`
- `PermissionsController.cs`
- `ModulesController.cs`

## Database Entities Used
- `ApplicationUser`
- `ApplicationRole`
- `Employee`
- `Module`
- `Permission`
- `RolePermission`
- `RoleModule`

## Business Workflow
1. Admin defines `Modules` and `Permissions`.
2. Admin creates a `Role` and assigns `Permissions` to it.
3. User is created and assigned a `Role`.
4. User profile is extended via the `Employee` table (adding Designation, Manager, DOJ).

## Field List (Employee Entity - Key Fields)
- `UserId` (Guid, FK)
- `Gender`, `DateOfBirth`, `Education`
- `TrackLocation`, `SelfieCheckin` (bool)
- `JoiningDate`
- `Designation`
- `ManagerId` (Guid, FK to Employee)
- `BusinessUnitId`, `DivisionId`, `DepartmentId`

## Validation Rules
- Users must have a unique email address.
- Employee hierarchy cannot have circular references (A manages B, B manages A).

## Role/Permission Rules
- Backend uses `PermissionAuthorizationHandler` to validate if the current User's Role contains the required Permission string for the requested endpoint.

## Current Gaps
- UI for handling forgotten passwords/resets exists but may lack robust email delivery confirmation logs.

## Recommended Improvements
- Implement Active Directory / SSO integration (e.g., Azure AD or Google Workspace).

## Acceptance Criteria
- Admin can create a new user and role.
- User can login and only access menus permitted by their role.

## Test Scenarios
1. **RBAC Enforcement**: Create a role without 'Delete' permission. Ensure user with this role receives 403 Forbidden when trying to delete.
2. **Hierarchy**: Ensure a Manager can view profiles of sub-ordinates but not peers.
