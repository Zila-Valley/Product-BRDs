# Institute-Level Subscription System Implementation Plan

This document outlines the proposed design and implementation plan for the Institute-Level Subscription System for the School ERP SaaS platform.

## 1. Existing System Analysis

*   **Multi-tenancy:** The system uses a multi-tenant architecture based on `ClientId` (`IMultiTenant`) and `InstituteId` (`IInstituteFilter`). Global query filters in `ApplicationDbContext` ensure data isolation.
*   **User/Institute Mapping:** `ApplicationUser` contains both `ClientId` and `InstituteId`. If a user is assigned to a specific institute, their `InstituteId` is populated. Otherwise, they may be a client-level admin with access to multiple institutes.
*   **JWT & Context:** `AuthService` generates JWT tokens including `clientId` and `instituteId` claims. `ClientContext` retrieves `ClientId` from either `X-Client-Id` header or JWT claims.
*   **ORM & Migrations:** EF Core is used for data access, but **EF migrations are disabled**. All schema changes must be applied via manual SQL scripts in the `Scripts` directory.
*   **Architecture:** The project follows a modular monolith structure with clean separation of concerns (`Core`, `Modules`, `Persistence`). Repositories and UnitOfWork pattern are utilized.
*   **RBAC:** Role-based access control is implemented using Identity and custom permissions.

## 2. Proposed Design

*   **Module Creation:** A new `Subscriptions` module will be created under `api/Modules/Subscriptions` containing Entities, DTOs, Repositories, Services, and Controllers.
*   **Institute-Level Granularity:** Subscriptions are tied strictly to an `Institute`. If a client has 5 institutes, there will be 5 independent `InstituteSubscription` records.
*   **Pricing Strategy:** Supports both `FixedYearly` and `PerStudentYearly`. For `PerStudentYearly`, the student count is snapshotted at billing/renewal time.
*   **Historical Preservation:** The `InstituteSubscriptionRenewal` table will store immutable snapshots of each billing cycle to ensure historical financial data remains intact even if pricing changes later.
*   **Access Control:** A custom action filter `[RequireActiveInstituteSubscription]` will intercept requests, validate the institute subscription status, and return a HTTP 402 if access is frozen/expired.

### Pricing & Capacity (PerStudentYearly)

*   **Student Capacity Limits:** When an institute subscribes under `PerStudentYearly` for 200 students, their `student_capacity` is set to 200.
*   **Overage Handling:** If the institute admits student #201, the system will not block the admission. Instead, an automated nightly job will detect the overage (`actual_students > student_capacity`).
*   **True-up Billing:** The system will generate a "Pending Overage Payment" record for the additional students (prorated or flat, depending on business rules) and update the `student_capacity` to the new number.
*   **Visibility:** Both Client-Admin and Institute-Admin will see a "Pending Dues" alert on their dashboard, and can view the pending invoice via the Subscription APIs.

## 3. Database Design (Manual SQL)

We will create a manual SQL script (e.g., `api/Scripts/002_Subscription_Tables.sql`) to add the following tables:

*   **`subscription_plans`**: Base templates for subscriptions.
*   **`institute_subscriptions`**: Main table holding the current state of an institute's subscription.
    *   *New fields to support overage:* `student_capacity` (int), `pending_overage_amount` (decimal).
*   **`institute_subscription_renewals`**: Historical snapshots of yearly renewals/billing.
*   **`subscription_payments`**: Payment transaction logs.
    *   *New field:* `payment_type` (Enum: Initial, Renewal, Overage).
*   **`subscription_events`**: Audit logs for status changes and important events.

All tables will use `uuid` for primary keys, and include `created_at`, `created_by`, `updated_at`, `updated_by` fields.

## 4. Proposed APIs

**Admin Subscription Management:**
*   `POST /api/subscriptions/plans` - Create a subscription plan template.
*   `GET /api/subscriptions/plans` - List plans.
*   `POST /api/subscriptions/institute/{instituteId}/trial` - Start a trial for an institute.
*   `POST /api/subscriptions/institute/{instituteId}/activate` - Activate subscription (sets pricing).
*   `POST /api/subscriptions/institute/{instituteId}/renew` - Manually renew a subscription.
*   `POST /api/subscriptions/institute/{instituteId}/freeze` - Freeze access to an institute.
*   `POST /api/subscriptions/institute/{instituteId}/unfreeze` - Unfreeze access.
*   `GET /api/subscriptions/summary` - Get global subscription summary (Client Admins).

**Institute Subscription APIs:**
*   `GET /api/subscriptions/my-institute` - Get subscription details for the current institute.
*   `GET /api/subscriptions/my-institute/status` - Quick status check (Active, Expired, Frozen).
*   `GET /api/subscriptions/my-institute/pending-dues` - Returns any unpaid overage/renewal invoices.

**Payment & Webhook:**
*   `POST /api/subscriptions/payments/record` - Record manual payment.
*   `POST /api/subscriptions/payments/pay-overage` - Process payment for extra students added.

## 5. Middleware / Access Control Approach

1.  **Filter Implementation:** Create `RequireActiveInstituteSubscriptionFilter` implementing `IAsyncActionFilter`.
2.  **Logic:** 
    *   Extract `instituteId` from the context (via `IUserContextService` or route/header).
    *   If no institute is targeted (e.g., Client-level operation), allow.
    *   If an institute is targeted, check `institute_subscriptions` using a cached or optimized query.
    *   If status is `Frozen` or `Expired`, short-circuit the pipeline and return:
        `{ "statusCode": 402, "errorCode": "BRANCH_SUBSCRIPTION_EXPIRED", "message": "Subscription expired for this institute. Please renew." }`
3.  **Allowed Endpoints:** Use an attribute `[AllowExpiredSubscription]` on endpoints that should remain accessible (e.g., Login, Support, Subscription Payment APIs).

## 6. Risks & Mitigations

> [!WARNING]
> **Risk:** Performance overhead of checking subscription status on every request.
> **Mitigation:** Implement caching (e.g., `IMemoryCache` or Redis) for the subscription status of active institutes. Cache invalidates upon renewal or freeze events.

> [!CAUTION]
> **Risk:** Manual SQL errors since EF migrations are disabled.
> **Mitigation:** Write careful, tested Postgres-compatible SQL scripts. Ensure idempotent scripts if possible, or clear instructions for running them.

> [!IMPORTANT]
> **Risk:** Miscalculating student counts for `PerStudentYearly` pricing.
> **Mitigation:** The snapshotting logic will query the active `Students` table at the precise moment of renewal creation and store the hardcoded count in `institute_subscription_renewals`.

## 7. Step-by-Step Plan

1.  **Database Scripting:** Write the manual PostgreSQL script for all 5 new tables.
2.  **Entity Creation:** Create the corresponding EF Core Entities in a new `Modules/Subscriptions/Entities` folder.
3.  **DbContext Configuration:** Add the `DbSet`s to `ApplicationDbContext.cs` and configure relationships in `OnModelCreating`.
4.  **Repositories & Interfaces:** Implement Repositories and Unit of Work registration.
5.  **Services:** Create `ISubscriptionService` implementing trial, activation, renewal, and freezing logic.
6.  **Background Job:** Implement `.NET BackgroundService` to:
    *   Transition `Active -> GracePeriod -> Frozen` based on dates.
    *   Run a nightly "True-up Check" for `PerStudentYearly` plans to detect if `actual_students > student_capacity` and generate `Overage` payment records.
7.  **Controllers:** Create API endpoints for Admin and Institute levels.
8.  **Access Control Filter:** Implement and register the `RequireActiveInstituteSubscriptionFilter`. Apply `[AllowExpiredSubscription]` to necessary endpoints.
9.  **Login Update:** Modify `AuthService.LoginAsync` to return institute status (Active, Expired, Frozen) for users with multiple institutes, optionally blocking login if their *only* institute is frozen.

## 8. Frontend UI Changes Plan

We will need to build the frontend interfaces to support this system. The frontend plan is as follows:

### Super Admin Interface (`/superadmin/subscriptions`)
*   **Subscription Management Page:** A new page for Super Admins to view all clients and their institute subscriptions in a data table.
*   **Plan Management:** UI to create and edit base subscription plans (`FixedYearly`, `PerStudentYearly`).
*   **Institute Controls:** Action buttons to manually Activate, Renew, Freeze, or Unfreeze any institute's subscription.

### School/Institute Admin Interface (`/school/admin/subscription`)
*   **My Subscription Page:** A dedicated page where the institute admin can view their current plan, status (Active, GracePeriod, etc.), expiry date, pricing model, and `student_capacity`.
*   **Pending Dues Section:** A section highlighting any unpaid overage charges or renewal invoices, with a "Pay Now" action button.
*   **Historical Billing:** A tab/table to view past `institute_subscription_renewals` and `subscription_payments`.

### Global UI / Interceptors
*   **Global Dashboard Alert:** Update `SchoolAdminDashboard.tsx` to display a warning banner if there are "Pending Dues" or if the institute is in the "Grace Period".
*   **402 Expired Interceptor:** Update the global API client (Axios/Fetch) to catch HTTP `402 BRANCH_SUBSCRIPTION_EXPIRED`. When caught, redirect the user to a strict "Subscription Expired" lock screen that only allows navigating to the Billing/Payment page.
*   **Login Institute Selection:** If a user logs in and has access to multiple institutes, the institute selection modal must show tags indicating the status (e.g., `<Badge color="red">Frozen</Badge>`) and disable selection for frozen institutes.

## 9. QA Manual Test Cases

The following test scenarios must be verified by the manual testing team:

### Scenario 1: Trial Period Flow
*   **Steps:** Super Admin starts a 14-day trial for Institute A.
*   **Expected:** Institute A can log in and access all features. Status shows "Trial".
*   **Steps:** Fast-forward system date by 15 days (or manually expire the trial).
*   **Expected:** Institute A login is allowed, but API calls return `402`. UI redirects to "Subscription Expired" page.

### Scenario 2: Per-Student Pricing & Overage
*   **Steps:** Activate `PerStudentYearly` plan for Institute B with 100 students capacity.
*   **Steps:** Add student #101 via the Admissions module.
*   **Expected:** Admission succeeds.
*   **Steps:** Trigger the nightly background job.
*   **Expected:** System generates a "Pending Overage Payment" for 1 student. Dashboard displays a red "Pending Dues" banner. Capacity increases to 101.

### Scenario 3: Fixed Pricing Renewal
*   **Steps:** Institute C is on `FixedYearly` plan expiring today.
*   **Steps:** Pay the renewal amount via the Subscription page.
*   **Expected:** Expiry date extends by 1 year. A new immutable snapshot is created in `institute_subscription_renewals`. `subscription_payments` logs the transaction.

### Scenario 4: Institute Isolation (Crucial)
*   **Steps:** Client X has Institute D (Active) and Institute E (Frozen).
*   **Steps:** Log in as Client X Admin.
*   **Expected:** Institute Selection screen shows Institute D as Active and Institute E as Frozen (disabled).
*   **Steps:** Select Institute D.
*   **Expected:** Access granted. Operations work normally.
*   **Steps:** Force API call using Institute E's context (Postman).
*   **Expected:** API returns `402 BRANCH_SUBSCRIPTION_EXPIRED`.

### Scenario 5: Allowed APIs during Freeze
*   **Steps:** Log into a Frozen Institute.
*   **Expected:** Standard APIs (e.g., fetching students) return `402`.
*   **Steps:** Hit the `/api/subscriptions/my-institute` and `/api/subscriptions/payments/record` APIs.
*   **Expected:** Returns `200 OK`. The billing portal must remain functional so the client can pay.

## User Review Required

Does this plan accurately reflect your vision for the Institute-Level Subscription System? 
Once approved, I will proceed with creating the SQL scripts and implementing the system.
