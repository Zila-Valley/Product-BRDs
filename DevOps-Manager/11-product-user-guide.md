# DevOps Manager - User & Admin Guide

## Welcome
Welcome to the DevOps Manager. This tool allows you to safely deploy, monitor, and maintain our product suite running on the Hostinger VPS.

## Table of Contents
1. [Logging In](#1-logging-in)
2. [Managing Products & Environments](#2-managing-products--environments)
3. [Deployments (UAT & PROD)](#3-deployments-uat--prod)
4. [Monitoring & Logs](#4-monitoring--logs)
5. [Database Backups & Restore](#5-database-backups--restore)
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

### 5. Database Backups & Restore
**How to Take a Database Backup:**
1. Navigate to **Database > Backup**.
2. Click **Generate New Backup**.
3. Once completed, click **Download** to save the `.dump` file to your local machine.

**How to Restore a Database Backup:**
1. Navigate to **Database > Restore**.
2. Upload a `.dump` file or select an existing backup from the server.
3. Click **Restore**.
4. **Safety Check**: The system will prompt you to type the exact name of the database. This action is destructive and cannot be undone!

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
