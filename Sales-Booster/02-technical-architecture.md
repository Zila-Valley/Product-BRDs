# Technical Architecture Document

## 1. Solution Overview
Sales Booster CRM is architected as a modular monolith. It utilizes a layered approach within a single .NET Core API project for backend operations, a React SPA (Single Page Application) for the web frontend, and a React Native (Expo) Mobile App for field agents. Real-time features are powered by SignalR, and background tasks are orchestrated using Quartz.NET and Hangfire.

## 2. Technology Stack
**Backend:**
- Framework: .NET 8.0 Web API
- ORM: Entity Framework Core
- Database: Microsoft SQL Server
- Authentication: JWT Bearer Tokens
- Logging: Serilog (to Console and File `Logs/app-.log`)
- Background Processing: Quartz.NET (scheduled), Hangfire (dashboard and queued tasks)
- Real-time Communication: SignalR (`/messageHub`)
- PDF Generation: QuestPDF
- Mapping: AutoMapper

**Frontend (Web):**
- Framework: React 18
- Build Tool: Vite
- Routing: React Router DOM v6
- UI Libraries: TailwindCSS (implied)
- API Client: Axios (configured via `axiosInstance.js`)
- State Management: React Context API (`PermissionsContext`) + Local State

**Frontend (Mobile):**
- Framework: React Native (Expo SDK 54)
- Routing: Expo Router (File-based routing)
- UI/Styling: NativeWind, React Native Paper
- API Client: Custom Fetch Wrapper (`ApiService.ts`)
- Storage & Features: AsyncStorage, Expo Location, Expo Notifications

## 3. Current Folder Structure

### Backend (`/api`)
- `/Config` - Configuration classes and settings binding.
- `/Controllers` - API endpoint definitions.
- `/Data/Entities` - Database models (e.g., Lead, Employee, Sale, Message).
- `/Extensions`, `/Filters`, `/Helpers`, `/Middlewares` - Cross-cutting concerns.
- `/Hubs` - SignalR hub definitions (`MessageHub.cs`).
- `/Infrastructure/IRepository` & `/Repository` - Data Access Layer interfaces and implementations.
- `/Infrastructure/IServices` & `/Services` - Business Logic Layer.
- `/Jobs` - Quartz/Hangfire job definitions (e.g., `SalaryGenerateJob.cs`).
- `/Migrations` - EF Core migration scripts.

### Frontend (`/web/src`)
- `/components` - Reusable UI components (Layout, Sidebar, Dashboard).
- `/context` - React contexts (e.g., `PermissionsContext`).
- `/pages` - Route-level components grouped by feature (Auth, Settings, RBAC, etc.).
- `/services` - Axios wrappers for backend API endpoints.
- `/utils` - Utility functions (`auth.js`, `utils.js`).

### Mobile (`/mobile`)
- `/app` - Expo Router configuration (tabs, stacks).
- `/features` - Core logic separated by domain (e.g., `auth`, `lead`, `home`).
- `/services` - API fetch wrappers and refresh token logic.
- `/utils` - Helpers for device features (location, selfie).

## 4. Backend Architecture
The backend follows a standard N-Tier architecture (Controller -> Service -> Repository -> Database).
- **Controllers**: Handle HTTP requests, basic validation, and return standard API responses.
- **Services**: Contain business logic. Most are scoped to handle individual entity logic (e.g., `LeadService`, `AttendanceService`).
- **Repositories**: Abstract the EF Core `ApplicationDbContext`. A generic `GenericRepository<T>` is used alongside specific repositories.
- **Dependency Injection**: Registered extensively in `Program.cs`.
- **Authentication/Authorization**: Standard JWT authorization. Custom `PermissionAuthorizationHandler` manages fine-grained RBAC based on the user's role and assigned permissions.

## 5. Frontend Architecture
The frontend is a classic Single Page Application.
- **Routing**: Client-side routing managed by `React Router`. Routes are wrapped in a `<Protected>` guard that verifies the `authToken` in local storage.
- **Layout**: A persistent `Layout` component wraps authenticated routes, providing a Sidebar and Header.
- **Services**: Each module has a dedicated service file in `/src/services` (e.g., `leadService.js`, `attendanceService.js`) which uses a customized `axiosInstance` for injecting the JWT token.

## 6. Database Architecture
The database is highly relational.
**Core Entities:**
- `ApplicationUser` (inherits IdentityUser)
- `Employee` (Links to ApplicationUser, tracks HR data)
- `BusinessUnit`, `Client`, `Department`, `Division` (Org structure)
- `Lead`, `Sale`, `Product`, `Category` (CRM core)
- `Attendance`, `EmployeeAttendance`, `LeaveRequest` (HR/Time Tracking)
- `Message`, `Channel`, `Workspace`, `Conversation` (Chat)

**Relationships:**
- 1 User -> 1 Employee
- 1 Employee -> Many Leads (assigned to)
- 1 Client -> Many Business Units

## 7. API Endpoint Inventory (Sample)

| Module | Method | Endpoint | Purpose | Auth Required |
|---|---|---|---|---|
| Auth | POST | `/api/auth/login` | Authenticate user | No |
| Leads | GET | `/api/leads` | Get all leads | Yes |
| Leads | POST | `/api/leads` | Create a lead | Yes |
| Attendance | POST | `/api/attendance/checkin` | Submit check-in coords | Yes |
| Sales | POST | `/api/sales` | Record a sale | Yes |

## 8. Authentication & Authorization
- **Login Flow**: Frontend sends credentials -> Backend validates against Identity store -> Returns JWT and Refresh Token.
- **RBAC**: Handled by custom models: `Role`, `Permission`, `RolePermission`, `Module`, `RoleModule`. This allows dynamic assignment of granular permissions (e.g., "Lead.Create", "Lead.Delete") to roles.

## 9. Deployment Architecture
- **Environment**: Controlled via `appsettings.json` and `.env` files (Development, UAT, Production).
- **Docker**: A `Dockerfile` and `docker-compose.yml` are present in both `/api` and `/web`, indicating containerization support.
- **Static Files**: Frontend build can be hosted separately (e.g., Nginx, S3) or served via .NET static files middleware. Currently, `Nginx` config is present in the `/web` folder.

## 10. Security Review
- **JWT**: Properly implemented with Secret Keys.
- **CORS**: Configured in `Program.cs` strictly for specific origins in production, with wildcard allowed in development.
- **SQL Injection**: Handled by EF Core LINQ abstraction.
- **Passwords**: Hashed automatically by ASP.NET Core Identity.

## 11. Observability
- **Logging**: Handled by Serilog. Writes to local files (`Logs/app-.log`).
- **Jobs**: Hangfire provides a visual dashboard (`/hangfire`) to monitor background jobs.
- **Exceptions**: `GlobalExceptionHandlerMiddleware` catches unhandled errors and saves them to the `ExceptionLogs` table.

## 12. Scalability Recommendations
- The chat system (`Message`, `MessageReaction`, `UserChannelRead`) generates high data volume. Consider partitioning these tables or moving historical chat data to CosmosDB/MongoDB.
- Add Redis for distributed caching instead of `MemoryCache` if horizontally scaling the API to multiple instances.
