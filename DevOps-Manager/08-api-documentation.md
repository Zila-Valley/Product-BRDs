# API Documentation

## Existing APIs Discovered

### 1. Deploy API
- **`GET /api/Deploy/projects`**
  - **Purpose**: Get all available projects for deployment.
  - **Auth**: Required.
  - **Response**: Array of `ProjectServiceDTO`.
- **`POST /api/Deploy/execute`**
  - **Purpose**: Execute deployment script.
  - **Auth**: Required (`SuperAdmin` role).
  - **Request Body**: `{ "serviceId": "guid", "branch": "string" }`
  - **Response**: `DeploymentLogDTO`.
- **`GET /api/Deploy/logs`**
  - **Purpose**: Paginated list of deployment logs.
  - **Query Params**: `startDate`, `endDate`, `page`, `pageSize`.
- **`GET /api/Deploy/logs/{id}`**
  - **Purpose**: Get specific deployment log.
- **`GET /api/Deploy/logs/project/{projectName}`**
  - **Purpose**: Get logs for a specific project.

### 2. Monitoring API
- **`GET /api/Monitoring/system`**
  - **Purpose**: Get VPS disk and RAM usage.
  - **Auth**: Required (`SuperAdmin` role).
  - **Response**: `{ "diskSpace": "...", "ramUsage": "..." }`
- **`GET /api/Monitoring/project/{serviceId}`**
  - **Purpose**: Get Docker stats (CPU/Memory/Uptime) for a specific container.
  - **Auth**: Required (`SuperAdmin` role).
- **`GET /api/Monitoring/projects`**
  - **Purpose**: Get stats for all project containers.
- **`POST /api/Monitoring/docker-cleanup`**
  - **Purpose**: Executes `docker system prune` and other cleanup commands.
  - **Auth**: Required (`SuperAdmin` role).
  - **Response**: `{ "output": "string" }`

### 3. Database API
- **`GET /api/Database/backup/download`**
  - **Purpose**: Generates and downloads a `.bak` file. *(Note: Currently implemented for SQL Server, must be fixed for PostgreSQL)*.
- **`POST /api/Database/restore/upload`**
  - **Purpose**: Uploads and restores a database backup. *(Note: Currently implemented for SQL Server)*.

---

## Proposed New APIs

### 1. Container Management
- **`GET /api/Containers/{serviceId}/logs`**
  - **Purpose**: Fetch the last 500 lines of `stdout`/`stderr` from the Docker container.
- **`POST /api/Containers/{serviceId}/restart`**
  - **Purpose**: Restart a specific Docker container.
- **`POST /api/Containers/{serviceId}/stop`**
  - **Purpose**: Stop a specific Docker container.

### 2. Rollback Management
- **`GET /api/Deploy/images/{serviceId}`**
  - **Purpose**: List available Docker images/tags for a specific service.
- **`POST /api/Deploy/rollback`**
  - **Purpose**: Revert a container to a previous image tag.

### 3. Enhanced Cleanup
- **`GET /api/Monitoring/docker-cleanup/preview`**
  - **Purpose**: Run docker prune in dry-run mode to see what will be deleted and space saved.

### 4. Audit & Approvals
- **`GET /api/AuditLogs`**
  - **Purpose**: Fetch logs of user actions (login, restart, deploy, prune).
- **`POST /api/Deploy/approve/{logId}`**
  - **Purpose**: Approve a pending PROD deployment request.

### 5. Health Checks
- **`GET /api/Health/endpoints`**
  - **Purpose**: Ping defined application URLs to check if they return 200 OK.
