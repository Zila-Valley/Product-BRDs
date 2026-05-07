# Database Documentation

## Current Database Usage
The backend uses Entity Framework Core with `Npgsql` to connect to a shared PostgreSQL instance. 
Existing tables discovered via the Service/Repository layers:
- Identity tables (`Users`, `Roles`, `UserRoles`)
- Location tables (`Countries`, `States`, `Districts`)
- Project tables (`Projects`, `ProjectServices`)
- Log tables (`DeploymentLogs`, `ExceptionLogs`, `JobExecutionLogs`)

*Critical Note: `DatabaseController.cs` attempts to execute SQL Server commands (`BACKUP DATABASE`) instead of `pg_dump` on this PostgreSQL database. This is a severe architectural gap.*

## Proposed Database Schema for DevOps Manager

### 1. Identity & RBAC
- **Users**: `Id`, `UserName`, `Email`, `PasswordHash`, `IsActive`.
- **Roles**: `Id`, `Name` (e.g., SuperAdmin, DevOpsAdmin, Developer).
- **UserRoles**: Mapping table.
- **Permissions**: Granular actions (e.g., `CanDeployProd`, `CanPruneDocker`, `CanRestoreDB`).

### 2. Product & Environment Registry
- **Products**: 
  - `Id`, `Name`, `Description`, `GitRepositoryUrl`, `CreatedAt`.
- **ProductEnvironments** (Replaces `ProjectServices`): 
  - `Id`, `ProductId`, `EnvironmentType` (DEV, QA, UAT, PROD).
  - `BranchOrTag`, `HostDirectoryPath`, `ContainerName`, `DockerComposeFile`.
- **SecretsMetadata**:
  - `Id`, `ProductEnvironmentId`, `KeyName`, `IsMasked`.
  - *Note: Actual secret values should be encrypted at rest or injected via a secure Vault, not stored in plain text.*

### 3. Deployment & Logs
- **DeploymentRequests**:
  - `Id`, `ProductEnvironmentId`, `RequestedBy`, `TargetCommitHash`, `Status` (Pending, Approved, Executing, Success, Failed).
- **DeploymentLogs**:
  - `Id`, `DeploymentRequestId`, `RawBashOutput`, `DurationSeconds`, `ExecutedAt`.
- **CommandExecutionLogs** (Audit):
  - `Id`, `UserId`, `CommandType` (Restart, Prune, Stop), `Target`, `Timestamp`, `Result`.

### 4. Database & Maintenance
- **ContainerSnapshots**:
  - `Id`, `ProductEnvironmentId`, `ImageTag`, `CreatedAt`.
- **BackupJobs**:
  - `Id`, `DatabaseName`, `TriggeredBy`, `Status`, `CreatedAt`.
- **BackupFiles**:
  - `Id`, `BackupJobId`, `FilePath`, `FileSizeMB`, `RetentionExpiryDate`.
- **RestoreRequests**:
  - `Id`, `BackupFileId`, `RequestedBy`, `ApprovedBy`, `Status`, `RestoredAt`.

## Secure Secret Handling Recommendation
- **Do not store raw secrets in plain text in the `Products` table.**
- If the DevOps Manager needs to generate `.env` files for `docker-compose`, the secrets should be stored using Symmetric Encryption (e.g., AES-256) in the database.
- The encryption key should be stored securely on the host OS as an environment variable (`DEVOPS_MASTER_KEY`) passed to the DevOps backend container.
- When generating the `.env` file during deployment, the backend decrypts the secrets in-memory and writes them to the host directory.
