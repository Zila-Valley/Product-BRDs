# Enterprise DevOps Manager - Implementation Roadmap

## Phase 1: Discovery, Stabilization, and Security (Current)
**Objective**: Stabilize the existing codebase, remove bloat, fix critical broken features, and secure command execution.

**Features / Tasks**:
- **Backend Tasks**:
  - Rewrite `DatabaseController.cs` to use `pg_dump` and `pg_restore` for PostgreSQL instead of SQL Server commands.
  - Implement a secure command execution wrapper to prevent command injection.
  - Fix Dockerfile to install `docker-cli` and `git` inside the container instead of mounting host binaries.
- **Frontend Tasks**:
  - Delete legacy ERP/CMS pages (`Courses`, `Students`, `FeeManagement`, etc.).
  - Ensure the Project and Environment registries function correctly without legacy dependencies.
- **Testing Requirements**: Verify Docker socket mount works with the newly installed CLI. Test database backup outputs a valid `.sql` or `.dump` file.

## Phase 2: Runtime Management
**Objective**: Give developers and DevOps personnel visibility into running applications without requiring SSH access.

**Features / Tasks**:
- **Container Logs Viewer**: Add an API endpoint `GET /api/Monitoring/logs/{containerName}` to fetch the last 500 lines of logs. Add a React UI component with auto-refresh and text filtering.
- **Container Lifecycle**: Implement Start/Stop/Restart buttons per container on the Project Details page.
- **Health Checks**: Introduce HTTP health check pings for configured product URLs.
- **Testing Requirements**: Ensure logs only show the targeted container. Ensure Stop/Start updates the UI status in real-time.

## Phase 3: Backup and Restore Enterprise Workflow
**Objective**: Bulletproof the database backup and restore process.

**Features / Tasks**:
- **Scheduled Backups**: Use `Quartz.NET` to trigger daily database backups.
- **Backup Retention**: Implement logic to delete backups older than 7 days.
- **Download/Restore UI**: Create a React screen to view available backups, download them locally, or restore them.
- **Restore Confirmation**: Implement a mandatory "Type the database name to confirm" dialog before executing a restore.
- **Testing Requirements**: Perform a full backup and restore cycle on a dummy database to guarantee data integrity.

## Phase 4: Monitoring and Cleanup
**Objective**: Prevent VPS disk space exhaustion and monitor resource usage effectively.

**Features / Tasks**:
- **Resource Dashboard**: Add visual charts for CPU/RAM and Disk Usage.
- **Cleanup Center**: Modify the existing "Prune All" logic to first return a dry-run/preview of what will be deleted and the estimated space saved.
- **Alerting**: Integrate Slack/Email notifications when Disk Usage exceeds 85%.
- **Testing Requirements**: Create dummy images/containers and verify they appear in the cleanup preview and are successfully removed.

## Phase 5: Enterprise Features
**Objective**: Add enterprise-grade governance and security features.

**Features / Tasks**:
- **Production Approval Workflow**: When deploying to PROD, require a second authorized user (SuperAdmin) to approve the request before execution.
- **Rollback Manager**: Track Docker image tags. Add a UI button to redeploy the previous tag instantly.
- **Secrets Management**: Move environment variables out of raw `docker-compose.yml` into the database, masking sensitive values in the UI.
- **Audit Reports**: Generate PDF/CSV reports of all deployments, cleanups, and restores.
- **Testing Requirements**: Verify that a Developer role cannot bypass the PROD approval workflow. Verify secrets are not leaked in the `/api/Deploy/logs` endpoint.
