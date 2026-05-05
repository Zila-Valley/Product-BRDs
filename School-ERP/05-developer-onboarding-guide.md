# Developer Onboarding & Getting Started Guide

Welcome to the School ERP development team! This guide will help you set up your local environment and understand our development workflows.

## 1. Prerequisites
Ensure you have the following installed on your machine:
- **Git**
- **.NET SDK 10.0** (or the exact version specified in `global.json`)
- **Node.js** (v18 or higher) & npm
- **Flutter SDK** (v3.11.1 or higher)
- **PostgreSQL** (v14 or higher) - can be installed locally or via Docker
- **IDE:** Visual Studio 2022 / JetBrains Rider (Backend), VS Code (Frontend/Mobile).

## 2. Local Setup: Backend API (`/api`)
1. Open terminal and navigate to `d:/tmk-computers/products/school/api`.
2. Ensure PostgreSQL is running.
3. Open `appsettings.Development.json` and update the `DefaultConnection` string with your local PostgreSQL credentials.
4. Open terminal and apply migrations/scripts:
   ```bash
   dotnet ef database update
   ```
   *(Note: The project uses a custom DatabaseSeeder in `Program.cs`. Ensure it runs on first startup to seed SuperAdmin and basic roles).*
5. Run the project:
   ```bash
   dotnet run
   ```
6. Open browser: `http://localhost:5000/swagger` to view the API documentation.

## 3. Local Setup: Web Frontend (`/web`)
1. Open terminal and navigate to `d:/tmk-computers/products/school/web`.
2. Install dependencies:
   ```bash
   npm install
   ```
3. Copy `.env.development` to `.env.local` and ensure `VITE_API_URL` points to your local backend (e.g., `http://localhost:5000/api`).
4. Start the development server:
   ```bash
   npm run dev
   ```
5. Open browser: `http://localhost:5173`.
6. Login using the SuperAdmin credentials seeded in the database.

## 4. Local Setup: Mobile App (`/mobile`)
1. Open terminal and navigate to `d:/tmk-computers/products/school/mobile`.
2. Install dependencies:
   ```bash
   flutter pub get
   ```
3. Generate Riverpod and Isar models:
   ```bash
   dart run build_runner build --delete-conflicting-outputs
   ```
4. Create an `.env.dev` file based on provided assets and set the API base URL.
5. Run on simulator or physical device:
   ```bash
   flutter run --flavor dev
   ```

## 5. Coding Standards & Git Strategy
- **Git Flow:** We use feature branching. Branch names should be `feature/ERP-XXX-short-desc` or `bugfix/ERP-XXX-short-desc`.
- **Backend:** 
  - Follow clean architecture principles.
  - Do NOT put business logic in Controllers. Use Services.
  - Inject the `IUserContextService` to get the current `BranchId` and NEVER trust the client to send it for write operations.
- **Frontend:**
  - Use functional components and Hooks.
  - Prefer Tailwind classes over custom CSS files.
  - Place reusable logic in custom hooks under `src/hooks`.
- **Mobile:**
  - Use Riverpod for state management. Avoid `setState` for complex logic.
  - Extract UI widgets into smaller, reusable components.

## 6. Common Development Tasks

### How to add a new API endpoint
1. Create a DTO in `Modules/{ModuleName}/Entities/DTOs`.
2. Add the method contract to `I{ModuleName}Service`.
3. Implement the business logic in `{ModuleName}Service`.
4. Inject the service into `{ModuleName}Controller` and map the HTTP route.
5. Add `[Authorize(Policy = "perm:YourNewPermission")]` to secure it.

### How to add a new Page in React
1. Create the component in `src/pages/{ModuleName}/NewPage.tsx`.
2. Add the route in `src/App.tsx` (or your routing configuration).
3. If it requires API calls, add the fetch logic using Axios in `src/services/`.

## 7. Troubleshooting
- **CORS Error:** Ensure the API `CorsPolicy` in `Program.cs` includes your local frontend URL (e.g., `http://localhost:5173`).
- **Database Connection Error:** Verify PostgreSQL service is running and credentials in `appsettings.json` are correct.
- **Flutter Build Runner Fails:** Run `flutter clean` then `flutter pub get` and try the build runner command again.
