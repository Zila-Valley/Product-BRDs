# Missing Implementation Plan

Date: 2026-05-03

This plan converts the audit findings into a staged implementation path. It intentionally avoids a "rebuild everything" recommendation. The product can continue from the current codebase, but only after the stop-risk items are fixed.

## Priority Definitions

- P0: Must fix before any real customer data or paid pilot.
- P1: Must fix before multi-institution Trust pilot.
- P2: Needed before broad enterprise sales.
- P3: Optimization or maturity work after core model is stable.

## Critical Missing Items

| Priority | Area | Missing implementation | Evidence | Recommended action |
|---|---|---|---|---|
| P0 | Security | Secret management and credential rotation | `api/appsettings*.json` include secrets | Move secrets to env/secret manager and rotate exposed credentials. |
| P0 | Tenant isolation | Verified server-side client/branch membership | `web/src/services/api.ts` sends headers from localStorage | Reject any client/branch context not assigned to authenticated user. |
| P0 | Academic isolation | Separate physical branch/institution from academic branch | `SyllabusService`, `TimeTableService`, `Syllabus.cs` | Introduce clear `InstitutionId`/`AcademicBranchId` mapping and migration. |
| P0 | Subscription security | Permission-gated billing administration | `SubscriptionsController` has `[Authorize]` only | Add `HasPermission` to create plan, activate, renew, freeze, unfreeze, view all. |
| P0 | Database discipline | Remove startup DDL mutation | `DatabaseSeeder.cs` dynamic `ALTER TABLE` | Move all schema changes to numbered SQL scripts. |
| P0 | Schema drift | Fix `Syllabuses.TemplateId` mismatch | SQL 007 adds column; entity missing property | Decide model and align entity, SQL, DTOs, tests. |
| P0 | Tests | Isolation and billing authorization tests | Test suite has gaps around forged headers and subscriptions | Add integration tests before refactors. |
| P1 | Academic model | Explicit program/course/term/department model | `AcademicType` only Course/Branch/Semester/Batch | Add model that supports schools, colleges, coaching, and custom structures. |
| P1 | Syllabus | Version/import/override workflow | `SyllabusTemplates.cs` is partial | Add versioned global templates and institution-level plans. |
| P1 | Frontend | Institution selector and route-level permission guards | Sidebar selector commented; routes broadly protected | Add role-aware selector and route permission metadata. |
| P1 | Exams | Non-school exam targets | `ExamSchedule.ClassRoomId` required | Replace class-room requirement with academic context. |
| P1 | Subscription UI | Real dashboards and billing history | Mock history and missing `/all` endpoint | Align APIs and pages for SuperAdmin/TrustAdmin/InstitutionAdmin. |
| P2 | Seeds | Indian master-data packs | `GlobalMasterSeeder` is demo-sized | Add curated, idempotent packs with source metadata. |
| P2 | Reporting | Trust/institution reports | School-centric docs/routes | Add report dimensions for trust, institution, program, term. |
| P2 | Performance | Large-volume query and reporting strategy | Unpaged/N+1 paths | Add indexes, projections, pagination, caching, background aggregation. |

## Phase 0: Stop-Risk Audit And Stabilization

Goal: stop the most dangerous security, billing, and schema behaviors before more features are added.

Tasks:

- Remove secrets from appsettings files and rotate exposed credentials.
- Add startup validation that production cannot run with placeholder/default secrets.
- Disable or remove runtime schema mutation from `DatabaseSeeder`.
- Block `Database.MigrateAsync()` in normal/manual-SQL mode.
- Fix or explicitly map `Syllabuses.TemplateId`.
- Add permission attributes to subscription administration endpoints.
- Fix `ActivateSubscriptionAsync` audit old-status bug.
- Add tests for forged `X-Client-Id` and `X-Branch-Id`.

Files likely affected:

- `api/appsettings*.json`
- `api/Program.cs`
- `api/Persistence/DatabaseSeeder.cs`
- `api/Core/Services/DatabaseMigrationService.cs`
- `api/Modules/Subscriptions/Controllers/SubscriptionsController.cs`
- `api/Modules/Subscriptions/Services/SubscriptionService.cs`
- `api/Modules/Academics/Entities/Syllabus.cs`
- `api/database/scripts/schema/*.sql`
- `api/tests/**`

Acceptance criteria:

- No production secret values are committed.
- Application refuses unsafe production config.
- No runtime DDL mutation occurs outside manual SQL runner.
- Subscription admin APIs require correct permissions.
- Header tampering tests fail before fix and pass after fix.

Tests required:

- Config safety tests.
- Subscription authorization integration tests.
- SQL idempotency tests.
- Schema/entity drift tests.
- Tenant and branch header tampering tests.

Definition of done:

- P0 findings in the audit are resolved or have explicit risk acceptance.
- CI blocks unsafe config and schema drift.

## Phase 1: Fix Tenant/Branch Isolation Gaps

Goal: make Trust and institution isolation reliable under hostile direct API calls.

Tasks:

- Define canonical language: `Client` as Trust and `Branch` as Institution, or migrate to explicit names.
- Validate selected branch against authenticated user's branch assignments.
- Replace broad nullable branch filter semantics with per-entity scope policies.
- Add Trust Admin all-institution access and Institution Admin single-institution access tests.
- Review all controllers accepting `clientId` or `branchId`.
- Ensure system/global master tables are not accidentally tenant-filtered.

Files likely affected:

- `api/Persistence/ApplicationDbContext.cs`
- `api/Core/Services/UserContextService.cs`
- `api/Core/Middleware/*`
- `api/Core/Helpers/Auth/*`
- `api/Modules/Identity/**`
- `api/Modules/Academics/Entities/Branch.cs`
- `web/src/services/api.ts`
- `web/src/context/AuthContext.tsx`

Acceptance criteria:

- SuperAdmin can access all trusts only through SuperAdmin APIs.
- Trust Admin can access only institutions in the same trust.
- Institution Admin can access only assigned institution.
- Forged headers cannot expand scope.

Tests required:

- Trust isolation integration tests.
- Branch isolation integration tests.
- Header forgery tests.
- RBAC plus tenant-scope tests.

Definition of done:

- Every scoped query path is backed by tests.
- All direct API calls enforce scope even if UI is bypassed.

## Phase 2: Fix Academic Structure Model

Goal: support Indian school, college, coaching, and custom structures without school-only workarounds.

Tasks:

- Add explicit models for `AcademicProgram`, `Course`, `Stream`, `Department`, `AcademicBranch`, `Batch`, and `Term`.
- Keep `Class` and `Section` for school/coaching where valid.
- Create `AcademicContext` or equivalent DTO for APIs that target academic scope.
- Replace semester-as-class and batch-as-section workarounds.
- Update student enrollment, exams, timetable, fees, attendance, and reports to use academic context.

Files likely affected:

- `api/Modules/Academics/Entities/**`
- `api/Modules/Students/**`
- `api/Modules/Exams/**`
- `api/Modules/Fees/**`
- `api/Modules/Attendance/**`
- `web/src/pages/school/admin/AcademicConfig.tsx`
- `web/src/pages/school/admin/CollegeHierarchySetup.tsx`
- `web/src/pages/school/admin/TimeTable.tsx`
- `web/src/pages/school/admin/FeeStructures.tsx`

Acceptance criteria:

- School: board/class/section works.
- College: course/department/semester or year works.
- CBCS: credits/electives/subject offerings work.
- Coaching: course/package/batch works.
- Custom: institution-defined levels can be configured.

Tests required:

- Academic context unit tests.
- Enrollment integration tests for school/college/coaching.
- Exam/timetable/fee tests per institution type.

Definition of done:

- No module needs to pretend semester is class or batch is section.

## Phase 3: Fix Board/University/Syllabus Architecture

Goal: make syllabus management trustworthy and customizable without mutating global data.

Tasks:

- Add `AffiliationBody`.
- Add `SyllabusVersion` with effective year, source URL, verification, and deprecation.
- Add `InstitutionSyllabusPlan` for imported/assigned syllabus.
- Add `SyllabusOverride` for institution-level customization.
- Add import APIs and audit trail.
- Seed boards/universities as master packs with source metadata.

Files likely affected:

- `api/Core/Entities/AcademicMasters.cs`
- `api/Core/Entities/SyllabusTemplates.cs`
- `api/Modules/Academics/Services/SyllabusService.cs`
- `api/Modules/Academics/Controllers/*Syllabus*`
- `api/Persistence/Seeders/GlobalMasterSeeder.cs`
- `web/src/pages/superadmin/SyllabusTemplates.tsx`
- new syllabus import/customization UI components

Acceptance criteria:

- Global templates remain immutable after publication.
- Institution can import and customize without altering global template.
- Deprecated versions remain readable.
- Effective academic year is enforced.

Tests required:

- Syllabus version tests.
- Import idempotency tests.
- Override merge tests.
- Permission tests for global vs institution syllabus operations.

Definition of done:

- Real board/university syllabus lifecycle is documented, tested, and usable in UI.

## Phase 4: Fix Subscription And Billing

Goal: make SaaS billing enforceable, auditable, and usable by role.

Tasks:

- Harden permissions for plan, activation, renewal, freeze, unfreeze, and history.
- Add payment history, renewal history, event audit, student-count snapshots, and overage detection.
- Add scheduled jobs for expiry, grace transition, freeze, and overage.
- Define allowed APIs during frozen/expired states.
- Add 402 frontend lock screen and billing-only navigation.
- Build SuperAdmin, Trust Admin, and Institution Admin dashboards.

Files likely affected:

- `api/Modules/Subscriptions/**`
- `api/Core/Filters/RequireActiveBranchSubscriptionFilter.cs`
- `api/Core/Attributes/AllowExpiredSubscriptionAttribute.cs`
- `web/src/pages/superadmin/Subscriptions.tsx`
- `web/src/pages/school/admin/SubscriptionDashboard.tsx`
- `web/src/services/subscriptionService.ts`
- `web/src/services/api.ts`

Acceptance criteria:

- Frozen branch cannot access operational APIs.
- Billing APIs remain accessible to authorized billing users.
- Overage and renewal history are auditable.
- UI reflects exact subscription state.

Tests required:

- Subscription lifecycle integration tests.
- Frozen/expired API matrix tests.
- Frontend 402 redirect tests.
- Payment/renewal audit tests.

Definition of done:

- Billing behavior can be explained and proven to a paying Trust.

## Phase 5: Enterprise Frontend Completion

Goal: make the frontend feel like a Trust/institution ERP, not a school-only admin panel.

Tasks:

- Rename routes and labels where user-facing terminology is school-only.
- Add Trust dashboard and institution dashboard variants.
- Add role-aware institution selector.
- Add academic setup wizard by institution type.
- Add board/university/affiliation setup pages.
- Add syllabus import/customization pages.
- Add permission-aware route guards.
- Improve loading, empty, error, and validation states.

Files likely affected:

- `web/src/App.tsx`
- `web/src/components/layout/Sidebar.tsx`
- `web/src/context/AuthContext.tsx`
- `web/src/pages/school/**`
- `web/src/pages/superadmin/**`
- `web/src/services/**`

Acceptance criteria:

- Trust Admin can switch institutions safely.
- Institution Admin sees only assigned institution.
- School, college, and coaching setup flows are distinct and realistic.
- Direct URL access respects permissions.

Tests required:

- Playwright E2E for role menus and direct routes.
- Form validation tests.
- 402 lock-screen tests.
- API error-state tests.

Definition of done:

- Frontend supports the backend capabilities without mock billing/history data.

## Phase 6: Performance/Security Hardening

Goal: prepare for large Trusts with 50+ institutions and lakhs of records.

Tasks:

- Add indexes for tenant, branch, academic year, student, fee, attendance, and reporting queries.
- Replace N+1 and unpaged queries.
- Add query projections for dashboards.
- Add caching for global masters and permissions.
- Add rate limiting, secure CORS, HTTPS metadata, password policy, cryptographic OTPs.
- Add file upload validation and content scanning policy.
- Add structured audit logs and safe error responses.

Files likely affected:

- `api/Program.cs`
- `api/Persistence/ApplicationDbContext.cs`
- service classes with reporting/export/list endpoints
- `api/database/scripts/schema/*`
- `web/src/services/api.ts`

Acceptance criteria:

- Large-list endpoints require paging.
- Dashboard queries are measured and indexed.
- Security baseline passes CI checks.

Tests required:

- Load tests for attendance, fees, dashboards, syllabus tree.
- Security tests for rate limiting, CORS, auth, and IDOR.
- SQL execution-plan checks for high-volume queries.

Definition of done:

- Performance and security risks are measured, not guessed.

## Phase 7: Production Deployment Readiness

Goal: make deployment, operations, support, and audit processes reliable.

Tasks:

- Document deployment process.
- Add database script promotion process.
- Add rollback scripts or forward-fix policy.
- Add production seed strategy.
- Add observability dashboards.
- Add backup/restore runbooks.
- Add admin/user documentation.
- Run pilot-readiness checklist.

Files likely affected:

- `docs/**`
- CI/CD configuration
- deployment manifests
- environment configuration templates

Acceptance criteria:

- A new environment can be built from scripts and documented steps.
- Rollback/restore has been rehearsed.
- Admins can operate subscriptions, institutions, roles, and academic setup.

Tests required:

- Deployment smoke tests.
- Database restore rehearsal.
- Production config validation.
- End-to-end pilot scenario.

Definition of done:

- Product is ready for controlled paid pilot with defined support process.
