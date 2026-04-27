# Device-Agnostic Biometric Attendance Integration

Based on a deep architectural analysis of the current backend (`StudentAttendance.cs`, `StaffAttendance.cs`, and `HostelAttendance.cs`), the current Attendance implementation is **fundamentally designed for manual, daily, bulk-entry** and is **currently weak/insufficient for direct biometric (Face/Fingerprint) integration.**

To support biometric devices, the system requires significant structural extensions. Here is a breakdown of the current limitations and the architectural changes needed to make it "biometric-ready":

### 1. Lack of Exact Timestamps (Punch-In / Punch-Out)
- **Current State:** The models only store `AttendanceDate` (a daily snapshot) and a `Status` (Present, Absent, Late). 
- **The Gap:** Biometric devices do not send "Present" or "Absent". They send exact timestamped "Punches". The current models cannot store a `PunchInTime` (e.g., 08:05 AM) and `PunchOutTime` (e.g., 03:15 PM) which are critical for calculating late marks, half-days, and overtime.

### 2. No "Raw Punch Log" Architecture
- **Current State:** The system expects attendance to be directly saved into the `StudentAttendance` or `StaffAttendance` tables.
- **The Gap:** Biometric machines can generate thousands of raw punch events per day. An ERP should never write these directly to final attendance tables. 
- **Requirement:** You need a new intermediate table (e.g., `DevicePunchLogs`) that captures raw data: `[DeviceUserId, PunchTime, DeviceIP, VerificationMode]`. A background service (like a CRON job or Hangfire) then processes these raw logs at the end of the day to generate the final `Status` in the `StaffAttendance` table.

### 3. Missing Biometric ID Mapping on Users
- **Current State:** The `Student` and `Staff` entities rely on GUIDs (`StudentId`, `StaffId`).
- **The Gap:** Face/Fingerprint scanners typically use short integer IDs (e.g., User ID: 1042) to identify people on the hardware. 
- **Requirement:** You must add fields like `BiometricDeviceId` or `RfidNumber` to both the `Student` and `Staff` database tables to map a hardware punch back to the correct user in the ERP.

### 4. No Device Management Module
- **Current State:** The system has no awareness of hardware.
- **The Gap:** If you have multiple devices (e.g., Main Gate, Hostel Gate, Staff Room), you need to know *where* a punch happened. A student punching at the Hostel Gate should update `HostelAttendance`, while a punch at the Main Gate updates `StudentAttendance`.
- **Requirement:** A new `BiometricDevices` entity/table is needed to store Device IP, Serial Number, Location, and Sync Status.

### 5. Lack of "Source" Auditability
- **Current State:** If a record is marked "Present", there is no way to know who or what marked it.
- **Requirement:** You need an `AttendanceSource` column (e.g., `Manual`, `Biometric_Face`, `Biometric_Finger`) in the attendance tables. This is crucial because principals often need to manually override a biometric absence if a machine fails, and the system must audit the difference between a machine punch and a human override.

### Summary Verdict
The current implementation is a solid **Stage 1 (Manual Attendance)**. To reach **Stage 2 (Automated Biometrics)**, you cannot simply tweak the existing controllers. You must build a new **"Biometric Listener Sub-system"** that includes:
1. Webhook API endpoints to receive data from machines (e.g., ZKTeco, Essl).
2. A `RawPunchLogs` table.
3. A Background Job Processor (to convert punches into Present/Absent statuses).
4. Biometric mapping columns on your User tables.

# Implementation Plan

## Goal
To implement a robust, scalable, and non-intrusive biometric attendance module (Face/Fingerprint) that integrates cleanly with the existing School ERP. It will support multiple vendors (e.g., ZKTeco, eSSL, Cloud APIs), process raw punches asynchronously using Quartz, and map devices to students/staff without modifying core domain logic.

## Summary of Existing System Architecture
- **Backend:** .NET Core 10 Web API utilizing Entity Framework Core (PostgreSQL via Npgsql, despite the user prompt mentioning SQL Server, `Program.cs` explicitly uses Npgsql).
- **Architecture Pattern:** Repository Pattern (`IUnitOfWork`, `IGenericRepository`) combined with Service Layer (`IStaffAttendanceService`, `IStudentAttendanceService`).
- **Background Jobs:** Quartz is already configured (`builder.Services.AddQuartz`). We will utilize Quartz instead of Hangfire.
- **Frontend:** React (Vite) organized by roles (`/pages/school/admin`, `/pages/school/hr`, etc.).
- **Current Attendance:** Purely manual, day-based snapshots (`AttendanceDate`, `Status`) mapped directly to `StudentAttendance` and `StaffAttendance` tables.

---

## 1. Database Migrations (Entity Extensions)

> [!CAUTION]
> Existing tables will be extended carefully with nullable columns to ensure 100% backward compatibility with manual attendance. No data migration required for existing records.

### Extend Existing Tables (`StudentAttendance`, `StaffAttendance`, `HostelAttendance`)
- `AttendanceSource` (Enum: `Manual`, `Biometric`) - defaults to `Manual`.
- `BiometricPunchInTime` (DateTime, nullable)
- `BiometricPunchOutTime` (DateTime, nullable)
- `BiometricDeviceId` (Guid, nullable)

### New Tables (New Module namespace)
- **`BiometricDevices`**: Manages physical/cloud devices.
  - `Id`, `Name`, `SerialNumber`, `IPAddress` (nullable), `ProviderType` (eSSL, ZKTeco, CloudAPI), `IsActive`, `ClientId` (IMultiTenant).
- **`BiometricUserMappings`**: Links ERP users to Device IDs.
  - `Id`, `UserId` (Guid), `UserType` (Enum: Student, Staff), `ProviderUserId` (String - device's local ID), `ClientId`.
- **`BiometricPunchLogs`**: Raw logs for asynchronous processing.
  - `Id`, `ProviderUserId` (String), `DeviceId` (Guid, nullable), `PunchTime` (DateTime), `VerificationMode` (Face, Finger, Card), `IsProcessed` (Boolean), `ClientId`.

---

## 2. Backend Implementation (Non-Intrusive Module)

Create a self-contained module under `api/Modules/Biometric/`.

### Abstraction Layer
```csharp
public interface IBiometricProvider
{
    string ProviderName { get; }
    Task<bool> SyncUsersAsync(Guid deviceId);
    Task<List<RawPunchDto>> FetchPunchesAsync(Guid deviceId, DateTime fromDate);
}
```
- **`BiometricProviderFactory`**: Resolves `IBiometricProvider` via DI based on device configuration.

### Providers
- `ZKTecoProvider` (Pull-based via TCP/IP)
- `EsslProvider` (Pull/Push based)
- `CloudWebhookProvider` (Push-based logic)

### Services & Repositories
- **`IBiometricDeviceService`**: CRUD for devices.
- **`IBiometricMappingService`**: Maps Students/Staff to Provider User IDs.
- **`IBiometricLogService`**: Saves raw punches to `BiometricPunchLogs`.

### Webhook API
- `POST /api/biometric/webhook/{provider}`
  - Validates API Key/Signature.
  - Normalizes vendor-specific JSON to standard DTO.
  - Bulk inserts into `BiometricPunchLogs` (Status: Unprocessed).
  - Returns `200 OK` immediately (Idempotent).

### Background Processing (Quartz Job)
Instead of Hangfire, we will use the existing **Quartz** setup.
- **`ProcessBiometricPunchesJob`**: Runs every 15 mins (configurable).
  1. Fetches `BiometricPunchLogs` where `IsProcessed = false`.
  2. Maps `ProviderUserId` to ERP `UserId` using `BiometricUserMappings`.
  3. Calculates First-In/Last-Out logic.
  4. Calls existing `IStudentAttendanceService` or `IStaffAttendanceService` to update/create records with `AttendanceSource = Biometric`.
  5. Marks logs as `IsProcessed = true`.

---

## 3. Frontend Integration (React Vite)

> [!NOTE]
> We will add new pages strictly under administrative menus and extend existing UI without redesigning core components.

### New Pages (`/src/pages/school/admin/biometrics/`)
- **Device Management:** CRUD for biometric devices (IPs, serials, providers).
- **User Mapping Hub:** A dual-pane list UI to link ERP Students/Staff to incoming `ProviderUserIds` from devices.
- **Raw Punch Logs:** Read-only datatable to view raw incoming webhook/pull logs for debugging.

### UI Extensions
- **Daily Attendance View:** Add a small chip/icon next to the status indicating `[Biometric]` vs `[Manual]`. Add an "Override" button for Manual overriding of Biometric data.

---

## 4. Modified & New Files

**Modified (Existing):**
- `ApplicationDbContext.cs` (Register new DbSets)
- `StudentAttendance.cs`, `StaffAttendance.cs` (Add new properties)
- `Program.cs` (Register Biometric services, DI factory, and Quartz Jobs)
- Frontend: `routes.tsx` or `App.tsx` (Add Biometric admin routes)
- Frontend: `StudentAttendance` UI tables (Add Source column).

**New (Backend):**
- `api/Modules/Biometric/Entities/...` (BiometricDevice, BiometricUserMapping, BiometricPunchLog)
- `api/Modules/Biometric/Interfaces/IBiometricProvider.cs`
- `api/Modules/Biometric/Providers/...`
- `api/Modules/Biometric/Services/...`
- `api/Modules/Biometric/Controllers/BiometricWebhookController.cs`
- `api/Modules/Biometric/Controllers/BiometricDeviceController.cs`
- `api/Modules/Biometric/Jobs/ProcessBiometricPunchesJob.cs`

**New (Frontend):**
- `web/src/pages/school/admin/biometrics/DeviceList.tsx`
- `web/src/pages/school/admin/biometrics/UserMapping.tsx`
- `web/src/pages/school/admin/biometrics/PunchLogs.tsx`
- `web/src/services/biometricService.ts`

---

## 5. Risks & Mitigation

| Risk | Mitigation Strategy |
| :--- | :--- |
| **High Webhook Traffic** | The Webhook endpoint does NO processing. It purely saves to `BiometricPunchLogs` and returns 200. Processing is offloaded to Quartz jobs. |
| **Duplicate Punches** | `BiometricPunchLogs` will have a unique constraint on `(DeviceId, ProviderUserId, PunchTime)` to reject duplicates at the DB level. |
| **Timezone Issues** | Devices send local time. The Webhook payload normalizer will convert timestamps to standard UTC or IST based on AppSettings before saving. |
| **Manual Override Conflicts** | If a teacher manually marks a student 'Present', the Quartz job checks `AttendanceSource`. If `Source == Manual`, the job skips overwriting the record, treating human input as final priority. |

## Open Questions for Reviewer
1. Do you have a specific vendor (eSSL, ZKTeco, Matrix) you want as the *first* concrete implementation of `IBiometricProvider`, or should I start with a generic Cloud Webhook provider?
2. Should the system auto-create unmapped User IDs in the `BiometricUserMappings` table when an unknown punch arrives, to allow Admins to easily link them later via UI?
