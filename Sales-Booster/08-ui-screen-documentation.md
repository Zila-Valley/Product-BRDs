# UI Screen Documentation

This document inventories the frontend React screens (pages) available in the Sales Booster CRM, their purpose, and status based on the routing configuration.

## Screen Inventory

| Route | Screen Name | Purpose | Auth Required | Status |
|---|---|---|---|---|
| `/login` | Login Page | User authentication. | No | Implemented |
| `/dashboard` | Dashboard | Main landing page showing high-level metrics. | Yes | Implemented |
| `/clients` | Clients Page | Manage client master data. | Yes | Implemented |
| `/settings` | Settings Page | System configurations. | Yes | Implemented |
| `/role-management` | Roles Management | Create and manage user roles. | Yes | Implemented |
| `/module-management` | Module Management | Manage system modules for RBAC. | Yes | Implemented |
| `/permission-management`| Permissions | Assign specific permissions to roles. | Yes | Implemented |
| `/employees` | Users Page | Manage application users and employee records. | Yes | Implemented |
| `/employee-details/:id`| User Details | Deep dive into a specific employee's profile. | Yes | Implemented |
| `/leads` | Leads Page | View and manage prospective customers. | Yes | Implemented |
| `/sales` | Sales List | View recorded sales transactions. | Yes | Implemented |
| `/target` | Employee Targets | View sales/collection targets. | Yes | Implemented |
| `/attendance` | Attendance | Daily check-in/out tracking view. | Yes | Implemented |
| `/leave` | Leave Requests | Approve/Reject leave applications. | Yes | Implemented |
| `/expense` | Expenses List | Track employee expenses/reimbursements. | Yes | Implemented |
| `/communication` | Messaging Layout| Chat interface (Workspaces, Channels). | Yes | Implemented |
| `/exception` | Exception Logs | View system errors (Admin only). | Yes | Implemented |
| `/jobs` | Job Logs | View background job executions. | Yes | Implemented |
| `/business-units` | Business Units | Manage org structure. | Yes | Implemented |
| `/categories` | Categories Page | Manage product categories. | Yes | Implemented |
| `/product` | Product Page | Manage product catalog. | Yes | Implemented |

## Common Screen States

### 1. Loading State
All screens use a centralized `GlobalLoader.jsx` or localized spinners while `axios` calls are pending to prevent user interaction during data fetching.

### 2. Error State
API failures (e.g., 500 Server Error) generally trigger a toast notification (assumed via a library like react-hot-toast or similar based on typical Vite setups) informing the user of the failure without crashing the UI.

### 3. Empty State
Tables (e.g., Leads, Sales) display a "No data found" or empty table layout when the API returns an empty array.

### 4. Layout
Protected screens are wrapped in `<Layout>` which provides a responsive `<Sidebar>` (collapsible) and a `<Header>` (with user profile and logout).

## Suggested UX Improvements
1. **Leads Page**: Convert the standard data table into a Kanban board grouped by `LeadStatus` for better visual pipeline management.
2. **Dashboard**: Ensure charts (if any) are lazy-loaded to improve initial page rendering speed.
3. **Mobile View**: The attendance map tracking page (`/track-map`) needs strict testing on mobile devices for usability by field managers.
