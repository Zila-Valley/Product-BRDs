# DevOps Manager - Gap Analysis & Missing Features

This document compares the current implementation of the DevOps Manager against a fully featured Enterprise DevOps admin panel.

## Feature Gap Matrix

| Feature | Current Status | Importance | Risk if Missing | Complexity | Priority |
|---|---|---|---|---|---|
| **Product Registry** | Exists (`ProjectController`) | High | - | Low | - |
| **Environment Registry** | Exists (`ProjectService`) | High | - | Low | - |
| **Container Status** | Partial (via `docker stats`) | High | Blind spots in system health | Low | P1 |
| **Container Logs** | **Missing** | High | Cannot diagnose app errors | Medium | P0 |
| **Backend/Frontend Logs** | **Missing** | High | Cannot diagnose app errors | Medium | P0 |
| **Reverse Proxy Logs** | **Missing** | Medium | Cannot diagnose routing issues | Medium | P2 |
| **Deployment Logs/History** | Exists (`DeployController`) | High | - | Low | - |
| **Git Pull Support** | Exists (`DeployService`) | High | - | Low | - |
| **Docker Compose Up/Restart** | Exists | High | - | Low | - |
| **Rollback Support** | **Missing** | High | Prolonged downtime on bad deploy | High | P1 |
| **Image List & Pruning** | Partial (Global prune only) | Medium | Disk space exhaustion | Medium | P2 |
| **Container/Volume Pruning** | Partial (Global prune only) | Medium | Disk space exhaustion | Medium | P2 |
| **Disk Usage Dashboard** | Exists (`MonitoringController`) | High | - | Low | - |
| **CPU/RAM Usage** | Exists | Medium | - | Low | - |
| **PostgreSQL Backup** | **Broken** (Uses SQL Server) | Critical | Data Loss | Medium | P0 |
| **PostgreSQL Restore** | **Broken** (Uses SQL Server) | Critical | Data Loss | Medium | P0 |
| **Manual Backup Download** | **Broken** | High | Data Loss | Low | P0 |
| **Backup Scheduling** | **Missing** | High | Data Loss | Medium | P1 |
| **Backup Retention Policy** | **Missing** | Medium | Disk Exhaustion | Low | P2 |
| **Environment Variable Management** | **Missing** | High | Hardcoded secrets on server | High | P1 |
| **Secrets Management** | **Missing** | High | Security vulnerability | High | P1 |
| **SSL/Domain Status** | **Missing** | Medium | Expired certificates | Medium | P3 |
| **Health Check Endpoints** | Exists internally | Medium | - | Low | - |
| **Product Uptime Monitoring** | **Missing** | Medium | Unnoticed downtime | Medium | P2 |
| **Notification System** | **Missing** | Medium | Delayed incident response | Medium | P2 |
| **Audit Logs** | **Missing** (Only deploys logged) | High | Lack of accountability | Medium | P1 |
| **RBAC** | Exists (Basic) | High | - | Low | - |
| **Approval Workflow for PROD**| **Missing** | High | Accidental PROD disruptions | High | P1 |
| **Maintenance Mode** | **Missing** | Medium | Poor user experience during DB migration | Low | P2 |

## Critical Gaps Details

### 1. Database Backup/Restore is Broken (P0)
**Issue**: The backend uses Entity Framework Core with `Npgsql` (PostgreSQL), but the `DatabaseController.cs` attempts to execute `BACKUP DATABASE` using `Microsoft.Data.SqlClient`. This code was copied from an MSSQL project and will instantly fail.
**Fix**: Rewrite `DatabaseController.cs` to use `pg_dump` and `pg_restore` via `Process.Start`.

### 2. Container Log Viewing (P0)
**Issue**: Developers currently cannot see why a container crashed without SSHing into the host.
**Fix**: Add an endpoint in `MonitoringController` that executes `docker logs --tail 500 <container_name>` and streams/returns the output.

### 3. Dangerous "Prune All" Implementation (P2)
**Issue**: The `CleanupDockerAsync` runs `docker image prune -a -f` which aggressively deletes all unused images. If a new deployment fails, rolling back will take significantly longer because the previous image was deleted.
**Fix**: Add a "Preview" feature. Show exactly what will be deleted and how much space will be saved before executing the cleanup.

### 4. Rollback Support (P1)
**Issue**: If a deployment introduces a critical bug, there is no one-click way to revert to the previous container image.
**Fix**: Track image tags per deployment and implement a "Redeploy previous version" button.

### 5. Frontend Bloat
**Issue**: The React app contains legacy routes like `Courses`, `Students`, and `FeeManagement`.
**Fix**: Remove these files to reduce bundle size and clarify the project's purpose.
