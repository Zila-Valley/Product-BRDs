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
  - **Guardrails**: If the selected service belongs to the **Production (`Prod`)** environment, the `branch` parameter must be `"master"` (case-insensitive). If any other branch is provided, the API rejects the request with an `ArgumentException` stating: *"Production deployment is only allowed from the 'master' branch."*
- **`GET /api/Deploy/logs`**
  - **Purpose**: Paginated list of deployment logs.
  - **Query Params**: `startDate`, `endDate`, `page`, `pageSize`.
- **`GET /api/Deploy/logs/{id}`**
  - **Purpose**: Get specific deployment log.
- **`GET /api/Deploy/logs/project/{projectName}`**
  - **Purpose**: Get logs for a specific project.
- **`POST /api/Deploy/webhook/{serviceId}`**
  - **Purpose**: **Git Webhook Trigger** (anonymous) to automatically build/rebuild container on push events.
  - **Auth**: None (anonymous allowed with `token` verification query param).
  - **Query Params**: `token` (string) - the secure secret token.
  - **Request Body (Optional JSON)**: GitHub/GitLab JSON push payload (e.g. `{"ref": "refs/heads/master"}`).
  - **Response**: `202 Accepted` - returns instantly to avoid push server timeouts; builds container in a background worker thread.
  - **Guardrails**: If the service belongs to the **Production (`Prod`)** environment, the webhook will strictly ignore pushes on any branch other than `"master"` and bypass deployment.
- **`POST /api/ProjectService/{id}/rotate-webhook-token`**
  - **Purpose**: Rotates the secret cryptographically secure webhook token for the specified service configuration.
  - **Auth**: Required (`SuperAdmin` role).
  - **Response**: Updated `ProjectServiceDTO` with new token.
- **`POST /api/ProjectService`** & **`PUT /api/ProjectService/{id}`**
  - **Purpose**: Create or update project service configurations (directories, webhook configs, etc.).
  - **Auth**: Required.
  - **Request Body**: `ProjectServiceInput`
  - **Response**: Created/updated `ProjectServiceDTO`.
  - **Guardrails**: If the service environment is configured as `Prod` and `WebhookEnabled` is true, `WebhookBranch` must be `"master"` (case-insensitive). Saving or updating with any other branch value throws an `ArgumentException` stating: *"Webhook branch for production must be 'master'."*

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

### 3. Database & Upload Backup API
- **`GET /api/Database/backups`**
  - **Purpose**: Get all persisted backup log metadata (from db logs, synced from disk automatically if db is empty).
  - **Auth**: Required (`SuperAdmin` or `DevOpsAdmin` role).
  - **Response**: List of `BackupFileDto` including details such as type (Database or Uploads), size, date, status, database/service identifier, and filePath.
- **`GET /api/Database/backups/download-file`**
  - **Purpose**: Downloads a specific database snapshot `.dump` or uploads folder backup `.zip` file stored on the server disk.
  - **Query Params**: `fileName` (string), `type` ("Database" or "Uploads").
  - **Response**: File stream (binary blob).
- **`DELETE /api/Database/backups`**
  - **Purpose**: Permanently deletes a backup file from the server disk and cleans up its record in the PostgreSQL database logging table.
  - **Query Params**: `fileName` (string), `type` ("Database" or "Uploads").
  - **Response**: Success status confirmation.
- **`POST /api/Database/backups/restore-file`**
  - **Purpose**: Restores a database or uploads directory to a previous state using a backup snapshot existing on the server.
  - **Request Body**: `{ "fileName": "string", "type": "string", "serviceId": "guid?" }`
  - **Response**: Success/Failure status report.

- **`GET /api/Database/backup/download`**
  - **Purpose**: Generates a live PostgreSQL backup using `pg_dump` on the host, creates database logs, and streams the `.dump` file binary back to the client immediately.
  - **Query Params**: `serviceId` (optional GUID to backup a specific microservice database, defaults to Central DevOpsDB if null).
  - **Response**: File binary stream.
- **`POST /api/Database/restore/upload`**
  - **Purpose**: Uploads a `.dump` binary snapshot and restores it onto the PostgreSQL database using `pg_restore`.
  - **Request Body**: Multi-part Form Data (`File` containing dump file, `ServiceId` optional GUID).
  - **Response**: Success/Failure status report.

- **`GET /api/Database/uploads/backup/download`**
  - **Purpose**: Compresses any custom service uploads folder (or host directory mapped to a container, e.g., `/app/wwwroot/uploads`) into a `.zip` file on-the-fly and downloads it.
  - **Query Params**: `serviceId` (optional GUID to back up a specific container uploads path, defaults to Central DevOps uploads folder).
  - **Response**: Compressed ZIP binary stream.
- **`POST /api/Database/uploads/restore/upload`**
  - **Purpose**: Restores a compressed `.zip` directory structure back to the specified service uploads path, clearing existing files first for consistency.
  - **Request Body**: Multi-part Form Data (`file` containing zip file, `serviceId` optional GUID).
  - **Response**: Extraction success status report.

### 4. Backup Scheduling & Trigger API
- **`GET /api/Database/cron/config`**
  - **Purpose**: Retrieves the current automated nightly schedule settings, including the Quartz Cron expression, the next calculated IST run time, and whether the service is actively executing in the background.
  - **Response**: `{ "cronExpression": "0 0 2 * * ?", "nextRunTime": "2026-05-09T02:00:00Z", "isExecuting": false }`
- **`POST /api/Database/cron/config`**
  - **Purpose**: Configures a new Quartz Cron schedule for automated nightly backups, validates the expression format, writes to persistence, and restarts the background delay immediately.
  - **Request Body**: `{ "cronExpression": "0 0 2 * * ?" }`
  - **Response**: Updated configurations and next scheduled IST run time.
- **`POST /api/Database/cron/trigger`**
  - **Purpose**: Triggers a manual, immediate scheduled backup run in the background (databases + zips), bypassing the current sleep interval.
  - **Response**: Confirmation message of background job initialization.

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
