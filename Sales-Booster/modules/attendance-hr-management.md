# Attendance & HR Management Module

## Purpose
To track daily physical attendance of field and office staff, manage shifts, leaves, and calculate monthly salaries based on attendance rules.

## Business Users
- HR Admins
- Employees (for check-in)
- Managers (for approvals)

## Features Implemented
- Geo-fenced/Location-based Check-in/Check-out.
- Selfie verification on Check-in.
- Shift timing assignments.
- Leave policies, balances, and request workflow.
- Daily automated attendance processing (calculating Late, Early, Payable Hours).
- Monthly salary generation.

## Screens Found in React
- `/attendance` -> `AttendancePage.jsx`
- `/track-map/:attendanceId/:date/:employeeId` -> `AttendanceTrackingMap.jsx`
- `/leave` -> `LeaveRequestsPage.jsx`
- `/settings-shift` -> `ShiftPage.jsx`
- `/AbsentEmployees` -> `AbsentEmployeesPage.js`
- `LeaveManagementContainer.js`

## APIs Found in .NET Core
- `AttendanceController.cs`
- `EmployeeAttendanceController.cs`
- `LeaveRequestsController.cs`
- `ShiftsController.cs`
- `SalaryController.cs`

## Database Entities Used
- `Attendance`
- `EmployeeAttendance`
- `Shift`
- `LeaveRequest`
- `LeaveBalance`
- `Holiday`
- `MonthlySalary`

## Business Workflow
1. HR configures Shifts, Holidays, and Leave Policies.
2. Employee opens web/app and clicks Check-in. System logs GPS coordinates.
3. Employee clicks Check-out at EOD, optionally adding an EOD Work Update.
4. Quartz Jobs run daily at 10 AM / 11 AM to process `Attendance` into `EmployeeAttendance`, applying Shift rules to determine 'Late' or 'Absent' marks.
5. On the 1st of the month, Quartz Job generates `MonthlySalary` based on `EmployeeAttendance` and `SalaryRevision`.

## Validation Rules
- Check-out time must be greater than Check-in time.
- Location coordinates must be within allowed radius if geo-fencing is strictly enforced.
- Leave request dates must not overlap with existing approved leaves.

## Role/Permission Rules
- Employees can only view their own attendance and salary.
- HR/Managers can view and approve leaves for their direct reports.

## Current Gaps
- Complex leave accrual rules (e.g., carrying forward casual leaves) may be hardcoded or missing.
- No explicit Biometric device integration (relies on browser/mobile GPS).

## Recommended Improvements
- Implement a calendar view for managers to see team availability.
- Allow manual attendance regularization requests by employees for missed punches.

## Acceptance Criteria
- Valid check-in saves exact latitude and longitude.
- Background job correctly flags an employee as late if check-in is past shift start time.

## Test Scenarios
1. **Punctuality Check**: Log check-in 1 minute after shift start -> Verify `IsLate` flag is true after job runs.
2. **Leave Balance**: Request leave -> Verify balance is deducted upon Manager approval.
