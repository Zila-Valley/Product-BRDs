# UI Screen Documentation

## 1. Design System & Layout
The Web Application is built with React and styled using Tailwind CSS. 
- **Layout:** Standard dashboard layout featuring a fixed left sidebar (collapsible) and a top navigation bar holding user profile and notifications.
- **Responsiveness:** All tables and forms use Tailwind's `md:` and `lg:` breakpoints to ensure usability on tablets and desktops.

## 2. Core Screens

### 2.1 Login Screen (`/login`)
- **Components:** Username/Email input, Password input, "Forgot Password" link, Login Button.
- **Logic:** Authenticates via API, stores JWT in local storage/context, routes user based on role to their respective dashboard.

### 2.2 Admin Dashboard (`/dashboard`)
- **Widgets:**
  - Total Students (Active vs Inactive)
  - Daily Attendance Overview Chart
  - Today's Fee Collection (Value & Trend)
  - Recent Inquiries list
- **Actions:** Quick links to "Admit Student", "Collect Fees".

### 2.3 Student Management (`/students`)
- **List View:** DataGrid/Table displaying Roll No, Name, Class, Section, and Status. Includes global search, filter by Class/Section, and export buttons (CSV/PDF).
- **Profile View:** Detailed tabbed interface:
  - Tab 1: Personal Details
  - Tab 2: Parents & Guardians
  - Tab 3: Academic Records
  - Tab 4: Fee Ledger
  - Tab 5: Documents Vault

### 2.4 Fee Collection Screen (`/fees/collect`)
- **Layout:** Two-pane view. Left pane searches for a student (by name/ID). Right pane displays pending fee heads.
- **Inputs:** Amount paying, Payment Mode (Cash/Bank/Online), Reference Number, Remarks.
- **Outputs:** On success, displays a modal with a "Print Receipt" button.

### 2.5 Role & Permission Matrix (`/settings/permissions`)
- **Layout:** Grid/Matrix where rows are Modules/Actions (e.g., `Create_Student`, `Delete_Fee`) and columns are Roles (e.g., `Admin`, `Teacher`).
- **Interaction:** Checkboxes to assign/revoke permissions. Bulk "Select All" per column.

## 3. General UI Components
- **Modals:** Used for quick add/edit actions (e.g., "Add Subject") to avoid navigating away from list views.
- **Toasts:** React-hot-toast used for success/error notifications at the top right of the screen.
- **Forms:** React Hook Form used alongside Zod for strict client-side validation.
