# Developer Onboarding Guide

Welcome to the Sales Booster CRM development team! This guide will help you set up your local environment and understand the codebase.

## 1. Project Overview
Sales Booster CRM is a .NET 8 Web API backend with a React 18 (Vite) frontend. It features real-time chat via SignalR, background jobs via Quartz/Hangfire, and uses SQL Server.

## 2. Local Setup Prerequisites
Ensure you have the following installed on your machine:
- [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- [Node.js](https://nodejs.org/en/download/) (v18 or higher recommended)
- [SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) (Developer/Express) or Docker for a SQL Server container
- SQL Server Management Studio (SSMS) or Azure Data Studio
- [Visual Studio 2022](https://visualstudio.microsoft.com/) or JetBrains Rider or VS Code

## 3. Backend Setup Steps
1. Navigate to the `/api` folder.
2. Open `SalesBooster.sln` in Visual Studio or your preferred IDE.
3. Open `appsettings.Development.json` (or `appsettings.json`) and configure your `DefaultConnection` string to point to your local SQL Server.
4. Run `dotnet restore` to install NuGet packages.

## 4. Frontend Setup Steps
1. Navigate to the `/web` folder.
2. Run `npm install` to install node modules.
3. Open `.env.development` and ensure `VITE_API_BASE_URL` points to your local .NET API URL (usually `http://localhost:5000` or `https://localhost:5001`).

## 5. Database Setup
The project uses Entity Framework Core Code-First migrations.
1. Open Package Manager Console in Visual Studio, or use the .NET CLI in the `/api` directory.
2. Run `dotnet ef database update` (or `Update-Database` in VS).
3. The `Program.cs` includes a `DataSeeder.SeedData` call which will populate essential initial roles, admin user, and settings upon first run.

## 6. How to Run the Application
**Backend:**
- Press `F5` in Visual Studio, or run `dotnet run` in the `/api` directory.
- Swagger UI will be available at `http://localhost:<port>/swagger`.
- Hangfire Dashboard available at `http://localhost:<port>/hangfire`.

**Frontend:**
- Run `npm run dev` in the `/web` directory.
- The app will usually open at `http://localhost:5173`.

## 7. How Authentication Works
- We use JWT (JSON Web Tokens). 
- The frontend sends a POST request to `/api/auth/login`.
- The token must be attached to the `Authorization` header as `Bearer <token>`.
- The frontend `axiosInstance.js` automatically intercepts requests and adds this token.

## 8. Coding Standards
- **Backend**: Follow standard C# naming conventions. Keep Controllers thin; place business logic in `Services`. Always use asynchronous programming (`async/await`). Ensure all DB calls use `CancellationToken`.
- **Frontend**: Functional components with Hooks. Use Tailwind utility classes for styling. Do not mutate state directly.

## 9. Folder Structure (Backend)
- `/Controllers`: Route entry points.
- `/Data/Entities`: Database models. If you add a model, add it to `ApplicationDbContext.cs`.
- `/Infrastructure/Services`: Where the actual business logic lives.
- `/Infrastructure/Repository`: Data access layer.
- `/Jobs`: Background tasks managed by Quartz.

## 10. First 5 Tasks for a New Developer
1. **Setup Environment**: Successfully run the API and Frontend locally.
2. **Review DB Schema**: Look at the ERD or the `ApplicationDbContext` to understand relationships.
3. **Trace a Request**: Trace the flow of creating a new Lead from `LeadsController` -> `LeadService` -> `LeadRepository` -> Database.
4. **Fix a Minor Bug**: Pick a UI glitch or a small validation rule to fix.
5. **Read the API Docs**: Familiarize yourself with the Swagger endpoints.
