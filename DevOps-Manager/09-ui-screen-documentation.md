# UI Screen Documentation

## Existing Screens Reverse Engineered
Based on the `AppRoutes.tsx`, the React application contains many screens. Notably, most belong to an ERP/CMS template (e.g., `Courses`, `Students`, `Staff`, `FeeManagement`, `Article`) which are out of scope for DevOps Manager.

The DevOps specific screens found:
1. **`Login` / `Register`**: Authentication.
2. **`Dashboard`**: Main landing after login.
3. **`Project`**: Manages logical projects.
4. **`ProjectService`**: Maps environments (UAT/PROD) and container names to Projects.
5. **`Deploy`**: UI for triggering `POST /api/Deploy/execute`.

## Proposed New Screens

### 1. Dashboard (Revamped)
- **Purpose**: High-level overview of the VPS.
- **Fields/Elements**: Cards showing total VPS RAM usage, CPU usage, Disk Usage. A grid showing "Product Status" (Running/Stopped).
- **API**: `GET /api/Monitoring/system`, `GET /api/Monitoring/projects`.

### 2. Products & Environments
- **Purpose**: Manage the registry.
- **Fields/Elements**: Add Product (Name, Git Repo). Add Environment (Branch, Directory Path, Container Name, Env Variables).
- **API**: `/api/Project`, `/api/ProjectService`.

### 3. Deployment Center
- **Purpose**: Execute deployments safely.
- **Fields/Elements**: Dropdown for Product, Dropdown for Environment, Dropdown for Branch/Tag.
- **Buttons**: `Deploy`. If PROD is selected, `Deploy` button changes color to Red and triggers a confirmation modal.
- **API**: `POST /api/Deploy/execute`.

### 4. Container Monitor & Logs
- **Purpose**: View container health and application output.
- **Fields/Elements**: List of active containers. "Status", "Uptime", "CPU", "RAM".
- **Buttons**: `Restart`, `Stop`, `View Logs`.
- **Logs View**: A dark-themed terminal-like `<pre>` block showing the last 500 log lines with a search bar.
- **API**: `GET /api/Containers/{serviceId}/logs`.

### 5. PostgreSQL Backup & Restore
- **Purpose**: Manage database snapshots.
- **Fields/Elements**: List of existing backups with dates and sizes.
- **Buttons**: `Generate New Backup`, `Download`, `Restore`.
- **Restore Workflow**: Clicking Restore opens a modal requiring the user to type "RESTORE-DB-NAME" to confirm.
- **API**: `/api/Database/backup`, `/api/Database/restore`.

### 6. Cleanup Center
- **Purpose**: Manage VPS disk space.
- **Fields/Elements**: Pie chart of disk usage. List of dangling images and stopped containers.
- **Buttons**: `Analyze Cleanup`, `Execute Cleanup`.
- **API**: `GET /api/Monitoring/docker-cleanup/preview`, `POST /api/Monitoring/docker-cleanup`.

### 7. Deployment History & Audit Logs
- **Purpose**: Traceability and compliance.
- **Fields/Elements**: Data table with columns: `Timestamp`, `User`, `Action/Project`, `Environment`, `Status`, `Execution Time`.
- **Buttons**: `View Details` (opens a modal showing the raw bash output of the deployment).
- **API**: `GET /api/Deploy/logs`, `GET /api/AuditLogs`.
