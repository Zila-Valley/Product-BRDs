# HR, Payroll & Attendance Module

## 1. Module Overview
This module manages the entire lifecycle of school staff (Teachers, Admins, Support Staff), including their daily attendance, leave approvals, and monthly salary processing.

## 2. Business Purpose
To centralize employee records, automate the tedious process of calculating monthly salaries based on leave rules, and eliminate buddy-punching through biometric integrations.

## 3. Users/Roles Involved
- **HR Manager / Institute Admin:** Manages staff profiles, approves leaves, processes payroll.
- **Staff/Teacher:** Applies for leave, views payslips.
- **Super Admin:** Monitors total payroll expenses across branches.

## 4. Features Implemented
- **Staff Profiles:** Stores demographics, qualifications, and role assignments.
- **Attendance Tracking:** Manual grid entry and Webhook endpoint for physical biometric devices.
- **Leave Management:** Defines leave types (Casual, Sick) and tracks balances.
- **Role-wise Compensation:** Configures basic salary, allowances (HRA, DA), and deductions.
- **Payroll Processing:** Generates monthly salary slips considering LWP (Leave Without Pay).

## 5. Detected Screens (Web App)
- `HR > Staff Directory`: List of all employees with quick-action buttons.
- `HR > Staff Attendance`: Date-picker and grid to mark Present/Absent/Half-Day.
- `HR > Leave Approvals`: Dashboard of pending leave requests from staff.
- `Payroll > Generate Payroll`: Monthly wizard to calculate and lock salaries.
- `Payroll > Payslips`: View/Print generated PDF payslips.

## 6. Backend APIs
- `GET /api/Staff`
- `POST /api/StaffAttendance/bulk`
- `POST /api/LeaveRequests/approve/{id}`
- `POST /api/Payroll/generate/{month}/{year}`
- `POST /api/Biometric/webhook` (External API for hardware devices)

## 7. Database Entities
- `Staff`: (Id, UserId, EmployeeId, DepartmentId, DesignationId, BranchId)
- `StaffAttendance`: (Id, StaffId, Date, Status, PunchInTime, PunchOutTime)
- `LeaveRequests`: (Id, StaffId, LeaveTypeId, StartDate, EndDate, Status)
- `RoleWiseCompensations`: (Id, RoleId, BaseSalary, AllowancesJson, DeductionsJson)
- `Payrolls`: (Id, StaffId, Month, Year, GrossSalary, NetSalary, Status)

## 8. Business Rules & Validations
- **Leave Clash:** A staff member cannot apply for leave on dates they are already marked 'Present' or have an existing approved leave.
- **Payroll Lock:** Once a month's payroll is generated and marked 'Paid', the attendance records for that month become immutable.
- **Biometric Sync:** The `ProcessBiometricPunchesJob` (Quartz) runs every 15 minutes to aggregate raw punch data into the `StaffAttendance` table.

## 9. Gaps & Recommendations
- **Gap:** Dynamic Tax (TDS/Income Tax) deductions are not algorithmically calculated based on governmental tax slabs.
- **Recommendation:** Enhance the payroll engine to support complex formula-based deductions, or integrate with a 3rd party tax API.
- **Gap:** Lack of a performance/appraisal tracking system.
- **Recommendation:** Add a basic appraisal module tied to yearly compensation increments.

## 10. Test Scenarios
- Mark a staff member absent for 3 days without approved leave, run the payroll generator, and verify their salary is deducted accurately.
- Trigger the biometric webhook with a mock payload and verify the `PunchInTime` updates correctly on the UI.
- Verify a Staff member can view their own payslip but cannot view the payslips of their peers.
