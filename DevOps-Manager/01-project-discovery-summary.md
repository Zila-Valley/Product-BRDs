# Project Discovery Summary

## Current Project Purpose
The **DevOps Manager** is designed to be a centralized deployment and management application for other Dockerized products deployed on the same Hostinger VPS. It provides a REST API backend (.NET Core) and a React frontend to trigger deployments, monitor containers, and potentially manage PostgreSQL backups and overall system health.

## Current Folder Structure
- `/api`: The .NET Core 8 backend. Includes Controllers, Infrastructure (Services/Repositories), Data models, and configuration.
- `/api/postgres`: Contains a common PostgreSQL `docker-compose.yml` and setup scripts.
- `/web`: The React frontend built with Vite and TailwindCSS, including pages and components.
- `/docs`: Documentation directory (newly created).

## Backend Architecture Discovered
- **Framework**: .NET Core (likely 8.0 based on modern syntax).
- **Database**: PostgreSQL (via `Npgsql.EntityFrameworkCore.PostgreSQL`), but there is conflicting logic in `DatabaseController.cs` which attempts to use `Microsoft.Data.SqlClient` and SQL Server commands (`BACKUP DATABASE`).
- **Design Pattern**: Controller-Service-Repository pattern.
- **Authentication**: JWT-based authentication with role-based access control (RBAC).
- **External Integrations**: Email (MailGun), SMS (Msg91), WhatsApp Webhook integrations.
- **Monitoring/Deploy**: Uses `ProcessStartInfo` to execute `bash` commands (e.g., `docker`, `git`) directly within the container.

## Frontend Architecture Discovered
- **Framework**: React with Vite.
- **Styling**: TailwindCSS.
- **Routing**: `react-router-dom` with public and protected routes.
- **Observation**: The current frontend codebase contains many extraneous routes and pages (e.g., `Courses`, `Students`, `Staff`, `FeeManagement`, `Article`) suggesting it was cloned from an existing School ERP or CMS project and retrofitted with DevOps features (`Deploy`, `Project`, `ProjectService`).

## Existing Deployment Flow
1. User triggers deployment via `/api/Deploy/execute`.
2. `DeployService.cs` fetches the project path from the database.
3. It spawns a bash process executing:
   - `git fetch origin`
   - `git checkout <branch>`
   - `git pull origin <branch>`
   - `docker compose stop <service>`
   - `docker compose build --no-cache <service>`
   - `docker compose up -d --force-recreate <service>`

## Existing Docker / Docker Compose Setup
- The DevOps API itself is dockerized with `devops_api` and `devops_api_uat` services.
- **Docker Socket Mount**: It mounts `/var/run/docker.sock` to allow the container to control the host's Docker daemon.
- **Host Tooling Mounts**: It directly mounts `/usr/bin/docker`, `/usr/bin/git`, `/root/.ssh` to use the host's tools inside the container.
- **Reverse Proxy**: Labels indicate the use of Traefik (`devops-api.tmkcomputers.in`).

## How Products/Environments Are Currently Managed
- Managed via `Project` and `ProjectService` entities in the database.
- Each `ProjectService` maps to a physical directory (e.g., `/var/www/myproject`) and a Docker container name.
- Environments (e.g., UAT, PROD) are likely distinguished by the `Environment` property on the `ProjectService`.

## Current Limitations
- **Database Backup Logic**: The `DatabaseController` attempts SQL Server commands (`BACKUP DATABASE`) instead of PostgreSQL (`pg_dump`), rendering it broken.
- **Frontend Bloat**: The React app contains dozens of unrelated pages (School ERP/CMS).
- **Hardcoded Paths**: Some paths like `/var/www` and `/var/opt/mssql/backup` are hardcoded.

## Risky Implementation Areas
- **Host Tooling Mounts**: Mounting `/usr/bin/docker` and `/usr/bin/git` can cause compatibility issues between the host OS and the container's base OS (e.g., missing shared libraries).
- **Command Execution**: Running raw shell commands via `ProcessStartInfo` is brittle. If a project path has spaces or injection characters, it could lead to arbitrary command execution.
- **Docker Socket**: Exposing the Docker socket gives the container root-level access to the host.

## Recommended Next Steps
1. Clean up the React frontend to remove unrelated domain pages.
2. Fix the `DatabaseController` to use `pg_dump` and `pg_restore` instead of SQL Server commands.
3. Secure the deployment command execution by strictly validating inputs and escaping arguments.
4. Consider an alternative to mounting host binaries (e.g., installing `docker-cli` and `git` directly in the DevOps container's Dockerfile).
