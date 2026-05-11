# Business Requirement Document (BRD)

## 1. Business Goal
To provide a secure, centralized, and automated DevOps Manager for deploying, monitoring, and managing containerized products on a Hostinger VPS. The platform aims to reduce manual server intervention, simplify environment management (UAT/PROD), and provide non-technical and technical stakeholders with visibility into application health and logs.

## 2. Stakeholders
- **Founders / Management**: Require visibility into uptime, resource usage, and audit trails.
- **DevOps Team**: Require tools for managing deployments, backups, and server resources without SSH access.
- **Development Team**: Require easy UAT deployments, log access, and restart capabilities.
- **QA Team**: Require control over UAT environment resets and deployments.

## 3. Target Users & Roles
### Super Admin
- Full access to all features.
- Can create projects, environments, and assign roles.
- Can execute PROD deployments and destructive cleanup tasks.

### DevOps Admin
- Can manage container states, prune Docker resources, and view system metrics.
- Can manage database backups and restores.

### Developer
- Can deploy to UAT/DEV environments.
- Can view logs for backend/frontend containers.
- Can restart specific services.

### QA User
- Can deploy tagged releases to QA/UAT environments.
- Can view application logs to diagnose test failures.

### Viewer/Auditor
- Read-only access to deployment history, audit logs, and project status.

## 4. Feature Requirements

### Product Management
- Ability to register new products.
- Define git repositories, branches, and working directories for each product.

### Environment Management
- Support for multiple environments per product (DEV, QA, UAT, PROD).
- Environment-specific variables management (with masking for secrets).

### Deployment Requirements
- One-click deployment based on selected branch/tag.
- Automated `git pull`, `docker compose build`, and `docker compose up`.
- Deployment history tracking with success/failure statuses and executed logs.
- Mandatory approval workflow or confirmation for PROD deployments.
- **Production Guardrails**: Production environment deployments are strictly restricted to the `master` branch only. Webhook target branches for production services must also be locked to `master` only.

### Container Runtime Management
- Dashboard listing all containers with CPU, Memory, and Uptime metrics.
- Actions to Start, Stop, and Restart specific containers.

### Log Viewing Requirements
- Web-based log viewer for container logs (last 500 lines).
- (Future) Live log streaming via WebSockets/SignalR.
- Filtering and search capabilities within logs.

### Disk Monitoring Requirements
- Dashboard showing Hostinger VPS disk usage.
- Alerts when disk usage exceeds a defined threshold (e.g., 85%).
- UI to prune unused Docker images, stopped containers, and dangling volumes (with preview).

### Backup/Restore Requirements
- Automated scheduled backups of the common PostgreSQL database.
- Manual one-click backup generation.
- Secure download of backup files.
- Manual restore functionality with strict confirmation workflow.

### Security Requirements
- Role-Based Access Control (RBAC).
- Secrets management: Passwords and keys must be masked in the UI and never printed in deployment logs.
- Safe command execution: Shell commands must be strictly parameterized to prevent injection.
- Production safety locks.

### Audit Trail Requirements
- Logging of all sensitive actions (deployments, restarts, database restores, cleanups).
- Log details: User, Action, Timestamp, IP Address, Target Resource, Result.

### Notification Requirements
- Slack/Email notifications on deployment success/failure.
- System alerts for high CPU, low disk space, or container crashes.

## 5. Non-Functional Requirements
- **Performance**: The web UI should be snappy; log loading should be paginated or limited to prevent browser crashes.
- **Reliability**: The DevOps Manager must remain available even if other products on the VPS fail.
- **Security**: Must run behind a secure reverse proxy (Traefik) with SSL/TLS enabled.
