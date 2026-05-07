# Developer Onboarding Guide

Welcome to the DevOps Manager project! This guide will help you set up your local environment and understand the codebase.

## Project Overview
DevOps Manager is an internal tool used to manage Dockerized applications (Products) deployed on our Hostinger VPS. It allows non-technical and technical staff to trigger deployments, view logs, take backups, and monitor server health without needing SSH access.

## Prerequisites
- **.NET 8 SDK**
- **Node.js 18+**
- **Docker Desktop** (or Docker Engine on Linux)
- **PostgreSQL 15+**

## Folder Structure
- `/api`: .NET 8 Web API.
- `/web`: React frontend using Vite and TailwindCSS.
- `/api/postgres`: Shared PostgreSQL DB scripts and Docker Compose.
- `/docs`: Project documentation.

## How to Run Backend Locally
1. Navigate to `/api`.
2. Ensure you have a running PostgreSQL instance. Update `appsettings.Development.json` with your connection string.
3. Apply Entity Framework migrations:
   ```bash
   dotnet ef database update
   ```
4. Run the API:
   ```bash
   dotnet run
   ```
5. Swagger will be available at `http://localhost:5000/swagger`.

## How to Run Frontend Locally
1. Navigate to `/web`.
2. Install dependencies:
   ```bash
   npm install
   ```
3. Copy `.env.development` to `.env` if necessary.
4. Start the Vite dev server:
   ```bash
   npm run dev
   ```
5. Access the UI at `http://localhost:4200`.

## How to Run Using Docker
To run the entire stack locally in Docker (simulating production):
1. Navigate to the root directory (or `/api`).
2. Run:
   ```bash
   docker compose build
   docker compose up -d
   ```
*Note: Depending on your host OS, volume mounting `/var/run/docker.sock` may require elevated permissions.*

## How Deployment Commands Work
Deployments are handled by `DeployService.cs`.
1. It looks up the project directory in the database.
2. It executes a series of bash commands using `System.Diagnostics.Process`.
3. Commands executed: `git fetch`, `git pull`, `docker compose build --no-cache`, `docker compose up -d`.

*Important Security Note*: Do not pass user input directly into these command strings to avoid Command Injection vulnerabilities.

## Coding Standards
- **Backend**: Follow standard C# and .NET Core conventions. Use asynchronous programming (`async/await`) everywhere. Use the Repository pattern for data access.
- **Frontend**: Functional components with React Hooks. Use Tailwind utility classes for styling. Do not create custom CSS classes unless absolutely necessary.
- **Logging**: Use Serilog (already configured in `Program.cs`). Log exceptions with full stack traces. Do not log sensitive secrets or environment variables.

## Hostinger VPS Deployment Notes
- The application runs behind **Traefik**.
- The `docker-compose.yml` uses Traefik labels for routing (e.g., `Host(devops-api.tmkcomputers.in)`).
- The container must mount `/var/run/docker.sock` to manage sibling containers on the VPS.
- Currently, the container mounts host binaries (`/usr/bin/docker`). Be aware of this if you update the base OS image of the `.NET` container, as library mismatches can occur.
