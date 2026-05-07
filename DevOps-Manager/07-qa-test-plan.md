# QA Test Plan

## 1. Functional Test Cases

| Test Case ID | Feature | Scenario | Expected Result |
|---|---|---|---|
| TC-F-01 | Product Management | Create a new project with valid details. | Project saves successfully and appears in the list. |
| TC-F-02 | Environment Management | Add a UAT environment to a project. | Environment saves and directory path is validated. |
| TC-F-03 | Deployment | Trigger deployment for a valid UAT environment. | System pulls latest code, rebuilds, starts container, logs show success. |
| TC-F-04 | Deployment | Trigger deployment for a non-existent branch. | Deployment fails gracefully; error is logged in UI; no crash. |

## 2. API Test Cases

| Test Case ID | Endpoint | Scenario | Expected Result |
|---|---|---|---|
| TC-A-01 | `POST /api/Deploy/execute` | Call without Auth token. | 401 Unauthorized. |
| TC-A-02 | `POST /api/Deploy/execute` | Call with Developer role for PROD. | 403 Forbidden. |
| TC-A-03 | `GET /api/Monitoring/system` | Fetch system health. | Returns 200 OK with Disk and RAM usage in valid format. |

## 3. UI Test Cases

| Test Case ID | Screen | Scenario | Expected Result |
|---|---|---|---|
| TC-U-01 | Dashboard | Load dashboard with multiple projects. | Cards load correctly, CPU/RAM stats populate within 5 seconds. |
| TC-U-02 | Logs Viewer | Open logs for a running container. | Last 500 lines display; text filter highlights correct terms. |
| TC-U-03 | Cleanup | Click "Prune Docker Resources". | A preview modal appears showing what will be deleted. |

## 4. Security Test Cases

| Test Case ID | Feature | Scenario | Expected Result |
|---|---|---|---|
| TC-S-01 | Deployment | Attempt command injection in Branch Name (e.g., `main; rm -rf /`). | Backend rejects input; command is not executed. |
| TC-S-02 | Secrets | View Deployment Logs in UI/API. | Database passwords and API keys do not appear in plain text. |
| TC-S-03 | Path Traversal | Create project with directory `../../etc/`. | System rejects invalid path; directory must be within allowed bounds. |

## 5. RBAC Test Cases

| Test Case ID | Role | Scenario | Expected Result |
|---|---|---|---|
| TC-R-01 | Developer | Attempt to delete a project. | Action button is hidden; API returns 403 Forbidden. |
| TC-R-02 | SuperAdmin | Assign "DevOpsAdmin" role to a User. | Role assigns successfully; user gains prune/backup access. |

## 6. Production Safety Test Cases

| Test Case ID | Feature | Scenario | Expected Result |
|---|---|---|---|
| TC-P-01 | PROD Deploy | Trigger deployment for PROD environment. | A confirmation modal or approval workflow halts immediate execution. |
| TC-P-02 | Database Restore | Upload backup file to restore PROD DB. | System prompts user to type the database name explicitly to confirm. |
| TC-P-03 | Docker Cleanup | Execute system prune. | System displays exactly which active containers/volumes will NOT be touched. |
| TC-P-04 | Audit Logs | SuperAdmin restarts a PROD container. | Action is permanently recorded in Audit Logs with Timestamp, User, and IP. |
