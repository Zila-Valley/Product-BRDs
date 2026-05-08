# DevOps Manager - User & Admin Guide

## Welcome
Welcome to the DevOps Manager. This tool allows you to safely deploy, monitor, and maintain our product suite running on the Hostinger VPS.

## Table of Contents
1. [Logging In](#1-logging-in)
2. [Managing Products & Environments](#2-managing-products--environments)
3. [Deployments (UAT & PROD)](#3-deployments-uat--prod)
4. [Monitoring & Logs](#4-monitoring--logs)
5. [Database & Uploads Backup Management](#5-database--uploads-backup-management)
6. [Server Maintenance & Cleanup](#6-server-maintenance--cleanup)
7. [Audit Logs](#7-audit-logs)
8. [Safety Instructions for Production](#8-safety-instructions-for-production)

---

### 1. Logging In
1. Navigate to the DevOps Manager URL (e.g., `https://devops-api.tmkcomputers.in`).
2. Enter your credentials.
3. Your role (Developer, DevOpsAdmin, SuperAdmin) dictates which tabs and buttons are visible to you.

### 2. Managing Products & Environments
**How to Add a Product:**
1. Navigate to **Products**.
2. Click **Add New Product**.
3. Enter the Product Name and Git Repository URL.

**How to Add Environments:**
1. Select a Product and go to the **Environments** tab.
2. Click **Add Environment**.
3. Select the type (UAT or PROD).
4. Enter the target Git Branch (e.g., `main` for PROD, `develop` for UAT).
5. Specify the Host Directory Path (e.g., `/var/www/myproject-uat`).
6. Specify the Docker Container Name (e.g., `myproject_uat_api`).

### 3. Deployments (UAT & PROD)
**How to Deploy UAT:**
1. Navigate to the **Deployment Center**.
2. Select your Product and select the **UAT** environment.
3. Click **Deploy**.
4. The system will pull the latest code and rebuild the container. You can watch the output in the terminal window below.

**How to Deploy PROD:**
1. Navigate to the **Deployment Center**.
2. Select your Product and select the **PROD** environment.
3. Click **Deploy to Production**.
4. **Important**: A secondary confirmation prompt will appear. Ensure the target branch is correct before clicking Confirm.

### 4. Monitoring & Logs
**How to Restart Containers:**
1. Go to **Container Monitor**.
2. Locate the container in the list.
3. Click the **Restart** button. Wait for the status to change to `Running`.

**How to View Logs:**
1. In the **Container Monitor**, click **View Logs** next to the target container.
2. A dark terminal window will open showing the last 500 lines of output.
3. Use the search box to filter for `Exception` or `Error`.

### 5. Database & Uploads Backup Management - [COMPLETED]

The Database and Uploads manager lets you safeguard PostgreSQL schemas and isolated Docker uploads folders on the server.

#### A. PostgreSQL Database Snapshots
1. **Take a Live Backup**: Navigate to **Database > Database Snapshots** tab. Choose a database service (or the Central DevOps DB) and click **Direct Backup & Download**. This runs a native `pg_dump` on the host and downloads the resulting `.dump` database snapshot.
2. **Download Backups from Server**: In the **Existing Server Backups** table, look for a row with a **Database** type. Click the **Download** (cloud download) icon to stream that snapshot from the server disk to your browser.
3. **Delete from Server Storage**: Click the **Delete** (trash can) icon to purge the file and its log record permanently from the VPS to free up space.

#### B. Upload Folders ZIP Backup (Volume Mapping Support)
Our microservices utilize volume maps such as `/home/tmk/data/salesbooster/uploads:/app/wwwroot/uploads` to isolate documents.
1. **Take an Uploads Zip Backup**: Select the **Upload Volume Folders** tab. Click **Direct Backup & Download** to pack and compress all documents into a single `.zip` file on-the-fly and download it locally.
2. **Download from Server**: Click the **Download** icon on any row of type **Uploads** to stream previous zip archives from the server.
3. **Execute Upload Restore**: Select a `.zip` file to upload and click **Restore Uploads**. The system will clear current files in that container mapped path and unpack the archive.

#### C. Automated Schedule Configuration (Quartz Cron Expressions)
Scheduled nightly backup jobs run automatically in the background to snapshot both PostgreSQL databases and all active microservices upload folders.
1. **Update Cron Schedule**:
   - In the **Database** header, click the **Edit** (pencil) icon on the schedule banner.
   - Select a pre-configured schedule card (e.g. **Nightly at 02:00 AM**, **Midnight Daily**, **Noon Daily**, or **Every 12 Hours**) or type a custom Quartz Cron expression.
   - Click **Apply Schedule** to save configurations. The background service instantly recalculates the next execution date/time in IST.
2. **Trigger Scheduled Backup Now**:
   - Click **Trigger Scheduled Now** on the active banner to run the entire backup schedule (databases + uploads zips) immediately.
   - The status beacon will glow amber and display `Executing Backup...` in real-time until the background process finishes.

#### D. Safe Restore Protection Shields
Restoring a backup (either database dumps or uploads folders) is a destructive operation.
1. Click **Restore** next to any existing backup row on the server.
2. An **Enterprise Overwrite Shield** modal will appear.
3. Read the caution details, check the acknowledgment box, and **type the exact database/uploads name** in uppercase to execute the restore process.

### 6. Server Maintenance & Cleanup
**How to Check Disk Usage:**
1. Navigate to the **Dashboard**.
2. Check the **Disk Usage** gauge. If it is over 80%, consider running a cleanup.

**How to Cleanup Unused Docker Resources:**
1. Navigate to **Cleanup Center**.
2. Click **Analyze Space**. The system will preview how many unused images and stopped containers can be removed.
3. Click **Execute Prune**. This will free up disk space on the Hostinger VPS.

### 7. Audit Logs
**How to Read Deployment & Audit Logs:**
1. Navigate to **Audit Logs**.
2. Here you can see every action taken by every user (e.g., "John deployed App-PROD", "Jane restarted Container-UAT").
3. Click **View Details** to see the exact shell output of a deployment.

### 8. Safety Instructions for Production
- **Never expose raw passwords**: Do not echo passwords in environment variables that might be printed to logs.
- **Double-Check Branch Names**: Always ensure you are deploying the `main`/`master` branch to PROD.
- **Maintenance Mode**: If you are performing a database migration, toggle the Maintenance Mode in the settings to gracefully inform end-users.
- **Backups First**: Always generate a fresh Database Backup before deploying a major release to PROD.
