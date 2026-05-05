# Enterprise Readiness Audit

Date: 2026-05-03  
Scope: `api/`, `web/`, `school-mobile/`, database scripts, seeders, tests, and repository documentation.

## A. Executive Summary

This product is not enterprise-ready and should not be sold to a real education Trust yet. The current implementation is best classified as an MVP/demo-grade modular monolith with several promising foundations, but also several critical gaps that can cause tenant leakage, institute confusion, billing abuse, schema drift, and incorrect academic modeling for Indian education groups.

The implementation has useful foundations:

- A `Client` and `Institute` model exists, with `Institute` acting as an institution/campus under a client.
- EF Core global query filters exist for `ClientId`, `InstituteId`, and academic year.
- Manual SQL script folders and a `SchemaVersions` runner exist.
- Academic structures, global boards/universities, syllabus templates, subscription entities, RBAC entities, and dynamic frontend menus have been started.
- Some isolation tests and database script tests exist.

The implementation is only partially complete:

- `Client` is still partly modeled as a school, not a Trust or education group.
- `Institute` is now the preferred product term for a physical institution/campus, while `AcademicBranchId` must be used for college specializations such as CSE, Mechanical, and Civil.
- School class/section assumptions still exist in exams, fees, timetable, syllabus, docs, routes, and frontend labels.
- Subscription business logic exists, but RBAC and billing-route protection are not enterprise-safe.
- Global masters and syllabus templates are seeded with demo-scale data, not production-grade Indian coverage.
- Frontend pages exist for several concepts but many are partial, school-labeled, mock-backed, or not aligned with backend APIs.
- The Flutter mobile app exists as an active channel for parent, student, and teacher workflows, but it is not yet enterprise-hardened for secure storage, subscription lockout, tenant/institution context, or college/coaching terminology.

The highest remaining risks are:

- Critical security exposure through hardcoded secrets and credentials in environment files.
- Medium tenant and institute isolation risk from nullable institute filters. Direct `X-Institute-Id` header tampering is now mitigated by backend middleware, but per-entity nullable-scope policy still needs refinement.
- Subscription-administration risk was reduced by adding explicit subscription permission gates to billing endpoints.
- Schema discipline risk was reduced by removing startup DDL and disabling EF migration execution from seeding/reset paths.
- High academic-model risk because school, college, and coaching structures are mixed using overloaded IDs and MVP mappings.
- Mobile security risk was reduced by secure-storage-only token reads, sensitive log suppression, and cache-scope clearing; analyzer verification is still blocked until Flutter/Dart is available.

Verdict: Phase 1 backend institute-isolation stabilization is complete, but the product is still not enterprise-ready. Freeze production/pilot sales until Phase 0 security/schema/subscription stop-risk items are closed and Phase 2 academic model work is underway.

## Demo-Only Implementation Signals

The following parts look impressive in reports or UI but are not production-grade:

- `api/Persistence/Seeders/CollegeAcademicSeeder.cs` explicitly states: "Treat Semesters as 'Classes' for student enrollment in this MVP mapping" and "Treat batches as sections". That is a demo workaround, not an enterprise academic model.
- `api/Persistence/Seeders/GlobalMasterSeeder.cs` seeds only a very small subset of Indian boards, universities, classes, and subjects.
- `web/src/pages/school/admin/SubscriptionDashboard.tsx` shows hardcoded billing history instead of real renewal/payment data.
- `web/src/pages/superadmin/Subscriptions.tsx` calls `/api/subscriptions/all`, but the backend exposes no matching endpoint in `SubscriptionsController`.
- `web/src/pages/superadmin/SyllabusTemplates.tsx` appears to assume missing master APIs and has no clear production-ready backend template-management surface.
- `api/Persistence/DatabaseSeeder.cs` contains demo reset behavior, preserved demo emails, and startup DDL patching.
- `school-mobile/README.md` is still the default Flutter README, while `school-mobile/PROJECT_DNA.md` and the code show a real Flutter app. Documentation maturity does not match implementation.
- `school-mobile/api-parent.tsv`, `api-student.tsv`, and `api-teacher.tsv` contain mojibake and partial endpoint status markers, so they are working notes rather than reliable mobile API documentation.

## B. Evidence-Based Findings

| Risk | Area | Evidence | What was found | Why it matters |
|---|---|---|---|---|
| Critical | Security | `api/appsettings.json`, `api/appsettings.Development.json`, `api/appsettings.Production.json`, `api/appsettings.Uat.json` | JWT secret keys, database password, superadmin password, SendGrid key, and MailGun key are present in config files. | Production secrets in source/config are a direct compromise risk. |
| Medium | Academic model | `api/Modules/Academics/DTOs/*`; `api/Modules/Exams/DTOs/*`; `web/src/pages/school/admin/*`; `mobile/lib/features/**` | Initial audit found DTO/service logic using `InstituteId` as a college academic branch. Phase 1 separated physical `InstituteId` from academic `AcademicBranchId` in high-risk API paths and web payloads. Some compatibility aliases still accept legacy query names. | Remaining risk is contract confusion during Phase 2 unless old academic `instituteId` aliases are retired after client updates. |
| Done | Subscription RBAC | `api/Modules/Subscriptions/Controllers/SubscriptionsController.cs`; `api/Core/Enums/MultiTenantRBAC/PermissionEnum.cs` | Subscription admin/read/payment endpoints now require explicit subscription permissions. | Keep permission seeding and regression tests mandatory. |
| Done | Institute isolation | `api/Persistence/ApplicationDbContext.cs`; `api/Core/Middlewares/InstituteContextValidationMiddleware.cs`; `api/Persistence/AppRoles.cs` | Institute filter is role-aware and ordinary users without institute context are rejected; TrustAdmin is explicitly recognized for all-institute Trust scope. | Future multi-institute staff assignment still needs explicit assignment modeling. |
| Medium | Tenant/institute headers | `web/src/services/api.ts`; `api/Core/Middlewares/InstituteContextValidationMiddleware.cs`; `api/Core/Services/UserContextService.cs` | Frontend still sends `X-Institute-Id`, but backend now validates the header against the authenticated user's client/institute context before controllers run. | This closes the direct header-forgery gap for current role model; future multi-institute staff assignments need an explicit assignment table. |
| Done | Database discipline | `api/Persistence/DatabaseSeeder.cs`; `api/Core/Services/MigrationService.cs`; `api/database/scripts/changes/010_phase0_stop_risk_schema_guards.sql` | Runtime `ALTER TABLE` and reset-time `MigrateAsync()` were removed; EF migration apply path now throws; manual SQL guard script added. | Manual SQL runner remains the only approved schema-change path. |
| Done | Schema/entity drift | `api/database/scripts/schema/000_initial_tables.sql`; `api/tests/SchoolErp.UnitTests/Database/DatabaseDisciplineTests.cs` | Current schema does not add `Syllabuses.TemplateId`; test and SQL guard enforce that decision until Phase 3 syllabus imports. | Add template linkage deliberately in Phase 3 if required. |
| High | Exams | `api/Modules/Exams/Entities/ExamSchedule.cs` | `ClassRoomId` is required even though college/coaching fields exist. | College, coaching, semester, and batch exam schedules remain school-classroom constrained. |
| High | Subscription freeze | `api/Core/Filters/RequireActiveInstituteSubscriptionFilter.cs`; `api/Modules/Subscriptions/Controllers/SubscriptionsController.cs` | Global subscription filter blocks frozen/expired institutes, but billing endpoints are not clearly marked with `AllowExpiredSubscriptionAttribute`. | A frozen institute may be blocked from payment/renewal APIs, while privileged admin APIs remain insufficiently permission-gated. |
| High | Subscription correctness | `api/Modules/Subscriptions/Services/SubscriptionService.cs`, `ActivateSubscriptionAsync` | The method sets subscription status to `Active` before recording the event old status. | Audit log records wrong state transitions, weakening billing/legal traceability. |
| High | Frontend subscription | `web/src/pages/superadmin/Subscriptions.tsx`; `api/Modules/Subscriptions/Controllers/SubscriptionsController.cs` | Frontend calls `/api/subscriptions/all`; backend controller has no matching `all` endpoint. | SuperAdmin subscription dashboard is not fully integrated. |
| High | RBAC frontend | `web/src/App.tsx`; `web/src/components/layout/Sidebar.tsx` | Routes are mostly split by SuperAdmin vs non-SuperAdmin; menu visibility is dynamic, but direct route access is not obviously permission-guarded per feature. | UI hiding is not enough. Unauthorized users may reach pages by URL unless page/API permissions block them. |
| Medium | Client model | `api/Core/Entities/Client.cs` | `Client` has `TrustName`, but also school-specific `SchoolCode`, `Board`, and `AffiliationNumber`. | Trust-level and institution-level data are mixed, limiting multi-institution realism. |
| Medium | Institution types | `api/Core/Enums/InstitutionType.cs` | Only `School`, `College`, and `CoachingClass` exist. | Medical, pharmacy, engineering, ITI, nursing, diploma, training center, and custom institution types need richer classification. |
| Medium | Academic structures | `api/Modules/Academics/Entities/AcademicStructure.cs`; `api/Modules/Academics/Enums/AcademicType.cs` | Generic hierarchy supports only `Course`, `Institute`, `Semester`, `Batch`. | Year-based, department-based, stream-based, term-based, CBCS, and custom structures are not explicitly modeled. |
| Medium | Subjects | `api/Modules/Academics/Entities/Subject.cs` | Subjects have credits/elective fields, but no global catalog, academic level, department/program mapping, versioning, or syllabus-source metadata. | CBCS and university-affiliated subject management will become brittle. |
| Medium | Syllabus architecture | `api/Core/Entities/SyllabusTemplates.cs` | Templates include Board/University/Class/Subject/Version/SourceUrl, but no effective academic year, verification flag, deprecation state, institute import copy, or override model. | Production syllabus management needs traceability, versioning, and institution customization. |
| Medium | Seed quality | `api/Persistence/Seeders/GlobalMasterSeeder.cs` | Seeds 8 boards, 2 universities, very few classes and subjects. | Demo seed data does not support real Indian education operations. |
| Medium | Coaching model | `api/Persistence/Seeders/InstituteAcademicSeeder.cs` | Seeds JEE/NEET and a few subjects/batches. | Package-based fees, multiple course bundles, admissions cycles, and training course variants are missing. |
| Medium | Fee model | `api/Modules/Fees/Entities/FeeStructure.cs`; `web/src/pages/school/admin/FeeStructures.tsx` | Fee structure supports class or academic structure, but coaching packages, course plans, overage, concessions, and multi-cycle plans are incomplete. Frontend uses `Guid.Empty` sentinel values. | Fees are central to ERP; sentinel IDs and school-centric models are risky. |
| Medium | Timetable | `api/Modules/Academics/Services/TimeTableService.cs`; `web/src/pages/school/admin/TimeTable.tsx` | Some college fields exist, but school labels and institute naming ambiguity remain. | Timetable correctness depends on clean academic hierarchy and institute isolation. |
| Medium | API docs | `api/API_DOCUMENTATION.md` | Documentation describes client as school and APIs around classes, sections, classrooms, and fee structures by class. | Docs do not describe Trust/institution enterprise model. |
| Medium | Manual SQL runner | `api/Core/Services/DatabaseMigrationService.cs` | `SchemaVersions` runner exists but skips by script name; already-applied checksum drift is not clearly rejected. | Edited historical scripts may go undetected. |
| Medium | CORS/JWT/password | `api/Program.cs` | CORS allows any origin, JWT HTTPS metadata is false, access token expiry is 43200 minutes, password policy minimum length is 6. | Defaults are unsuitable for production SaaS. |
| Medium | OTP | `api/Modules/Identity/Services/AuthService.cs` | OTP generation uses `Random()`. | Security-sensitive OTPs should use cryptographic randomness. |
| Medium | Performance | `api/Modules/Academics/Services/SyllabusService.cs` | Progress enrichment performs per-group queries and counts. | Syllabus trees and progress reporting can become slow at scale. |
| Medium | Performance | `api/Core/Services/ExportService.cs`; multiple services | Large unpaged reads and export page size of 10000 appear. | Large Trusts with lakhs of records need strict paging, streaming, and reporting strategy. |
| Medium | Frontend terminology | `web/src/context/AuthContext.tsx`; `web/src/components/layout/Sidebar.tsx`; routes under `web/src/pages/school` | UI still stores `schoolName`, routes under `school`, and labels use School Admin. | Enterprise buyers will see a school-only product, not a Trust/institution ERP. |
| Low | Global masters reads | `api/Modules/Academics/Controllers/GlobalMastersController.cs` | GET endpoints are not permission-specific, relying on fallback auth. | Usually acceptable for global masters, but should be explicitly designed and documented. |
| High | Mobile token security | `school-mobile/lib/features/auth/presentation/providers/auth_provider.dart` | Access token is persisted in `SharedPreferences` as `access_token`; platform secure storage is not used. | Mobile tokens should be stored in OS secure storage/keychain/keystore, not plain app preferences. |
| High | Mobile sensitive logging | `school-mobile/lib/core/network/dio_provider.dart` | Dio `LogInterceptor` logs request bodies and response bodies. | Login responses, student data, fees, and personal data may leak into device/debug logs. |
| High | Mobile tenant/institution context | `school-mobile/lib/core/network/dio_provider.dart`; `school-mobile/lib/features/auth/data/models/user_profile.dart` | Authenticated requests add only `Authorization`; no explicit validated institution/academic-year context is sent. `UserProfile` has `instituteId`, but no Trust/institution selector or assignment model. | Parent/teacher workflows across institutions may be incorrectly scoped or rely entirely on backend defaults. |
| High | Mobile cache isolation | `school-mobile/lib/core/storage/isar_service.dart`; `school-mobile/lib/features/dashboard/presentation/providers/student_provider.dart` | Isar cache stores students, attendance, homework, transport, hostel, syllabus, exams, and notices globally; cache helpers do not include userId/clientId/institutionId partitioning. | A logout/switch-account failure or shared device can expose another user's student data. |
| Medium | Mobile role coverage | `school-mobile/lib/features/auth/data/models/user_profile.dart`; `school-mobile/lib/features/dashboard/presentation/screens/main_navigation_controller.dart` | Mobile routes only recognize `teacher`, `parent`, and `student`. | Acceptable for current mobile scope, but Trust Admin/Institution Admin/Billing roles must be explicitly web-only or future mobile roles. |
| Medium | Mobile academic flexibility | `school-mobile/lib/features/teacher/data/attendance_api.dart`; `school-mobile/lib/features/teacher/presentation/screens/tabs/teacher_attendance_tab.dart` | Teacher attendance is class/section based through `/api/Classes` and `/api/Sections/filter`. | Teacher mobile workflows are school-first and do not yet support college semester/batch or coaching batch attendance. |
| Medium | Mobile API alignment | `school-mobile/lib/features/finance/data/finance_api.dart`; `school-mobile/api-parent.tsv` | Fee API calls `/api/FeeCollections` without studentId in code, while notes reference `?studentId={studentId}`. | Parent with multiple children may see wrong or unfiltered fee data unless backend scopes perfectly. |
| Medium | Mobile subscription handling | `school-mobile/lib/core/network/dio_provider.dart` | No 402 interceptor or frozen/expired institution lock screen exists. | Mobile users may receive confusing failures during subscription freeze; billing-safe behavior is undefined. |
| Medium | Mobile terminology | `school-mobile/lib/features/dashboard/presentation/widgets/service_grid.dart`; `school-mobile/lib/features/teacher/presentation/screens/tabs/teacher_attendance_tab.dart` | Text such as "School Services", "CLASS", "SECTION", and class/section APIs remain school-centric. | College/coaching users will see incorrect language and workflows. |
| Medium | Mobile config/release | `school-mobile/BUILD_INSTRUCTIONS.md`; `school-mobile/pubspec.yaml`; `.env.dev`, `.env.prod` | Dev/prod flavors exist, but README is generic and production signing is a to-do; env URLs are packaged as assets. | Release governance, environment handling, and store readiness need hardening. |
| Medium | Mobile tests | `school-mobile/integration_test/auth_flow_test.dart`; `school-mobile/test/features/**` | Tests cover some widgets/models/repositories; E2E login is only a UI presence check. | Critical mobile auth, cache clearing, role routing, offline, and permission flows are not proven. |

## C. Enterprise Readiness Scorecard

| Area | Score | Assessment |
|---|---:|---|
| Multi-tenancy | 5/10 | Client filters exist, but client model and header/context validation need hardening. |
| Institute/institution isolation | 4/10 | Institute entity exists, but nullable institute filters and overloaded institute terms are risky. |
| Academic flexibility | 4/10 | Generic structures started, but school assumptions and MVP college mappings remain. |
| Syllabus architecture | 2.5/10 | Templates started, but versioning/import/override/verification model is incomplete. |
| Seeding quality | 3/10 | Seeds are useful demos, not production Indian coverage. |
| Subscription system | 4/10 | Core entities/services exist, but RBAC, freeze behavior, audit accuracy, and UI integration are incomplete. |
| RBAC | 5/10 | Permissions exist, but enforcement is inconsistent across controllers and frontend routes. |
| Frontend completeness | 4/10 | Many pages exist, but terminology, route guards, API alignment, and mock data remain. |
| Mobile completeness | 4/10 | Flutter app has real parent/student/teacher screens, but security, cache isolation, college/coaching support, and subscription handling are incomplete. |
| Database script discipline | 4/10 | Script runner exists, but startup DDL mutation and schema/entity drift are serious. |
| Performance readiness | 3/10 | Pagination exists in places, but reporting/export/progress paths are not scale-ready. |
| Security readiness | 2/10 | Hardcoded secrets, weak production defaults, long tokens, broad CORS, and route authorization gaps. |
| Testing readiness | 3/10 | Some useful tests exist, but high-risk flows lack coverage. |
| Documentation readiness | 2/10 | Docs are school-centric and do not describe enterprise Trust/institution operations. |

Overall score: 3.6/10.

## D. Missing Implementation List

| Area | Missing item | Current evidence | Business impact | Technical risk | Recommended fix | Priority |
|---|---|---|---|---|---|---|
| Security | Remove committed secrets and rotate credentials | `api/appsettings*.json` contain secrets | Cannot pass enterprise security review | Critical compromise risk | Move secrets to environment/secret manager; rotate all exposed values | P0 skipped by request |
| Tenant isolation | Server-side institute assignment validation | `InstituteContextValidationMiddleware` and integration tests now exist | Future multi-institute staff assignment model is not explicit yet | Medium IDOR risk if new assignment modes bypass middleware assumptions | Add `UserInstitutionAssignments` when multi-institute staff roles are introduced | Done/P1 follow-up |
| Institute model | Separate physical InstituteId from academic AcademicBranchId | Phase 1 corrected high-risk academic DTO/service paths | Legacy query alias remains | Contract confusion | Remove academic `instituteId` aliases after Phase 2 client contract cleanup | Done/P2 cleanup |
| Subscription | Permission-gate billing admin endpoints | Subscription endpoints now have explicit permission attributes | Billing operations now have backend permission gates | Regression risk if permissions are not seeded | Keep tests and seed new permission keys | Done |
| Database | Stop runtime schema mutation | Seeder DDL removed; manual SQL guard script added | Production schema mutation removed from startup | Script runner must be used consistently | Keep database discipline tests in CI | Done |
| Database | Resolve SQL/entity mismatch | Current schema has no `Syllabuses.TemplateId`; guard test added | Drift claim resolved for current schema | Phase 3 may need a deliberate import model | Reopen as Phase 3 design decision | Done |
| Academic model | Trust/institution/program/course model | Client/Institute plus `AcademicStructure` only | Cannot reliably sell to mixed Trusts | Model debt | Add explicit `Institution`, `AcademicProgram`, `Course`, `Department/Stream`, `Term` model | P1 |
| Exams | Non-school exam schedules | `ExamSchedule.ClassRoomId` required | Colleges/coaching cannot schedule correctly | Schema constraint bug | Make exam target polymorphic/academic-context based | P1 |
| Syllabus | Syllabus version/import/override | Template lacks effective year/import copy/override | Cannot manage board/university changes safely | Data lineage loss | Add `SyllabusVersion`, `InstitutionSyllabusPlan`, `SyllabusOverride` | P1 |
| Seeds | Production-grade global masters | `GlobalMasterSeeder` has tiny demo set | Customers need Indian coverage | Incorrect setup defaults | Add idempotent master packs with source/version metadata | P2 |
| Frontend | Trust/institution selector | Sidebar institute selector commented out | Trust Admin cannot operate across institutions cleanly | Mis-scoped UI | Build validated institution switcher with backend membership | P1 |
| Frontend | Subscription dashboard integration | Mock history, missing `/all` endpoint | Billing operations not trustworthy | Incomplete workflows | Align backend endpoints and UI by role | P1 |
| Mobile | Secure token storage | `SharedPreferences` stores `access_token` | Lost/stolen/shared device risk | Token compromise | Move tokens to secure storage and add refresh/expiry handling | P0 |
| Mobile | User-scoped cache partitions | `IsarService` cache helpers are global | Wrong student data can appear after account switch | Privacy leak | Add user/client/institution/student scoping and cache invalidation tests | P0 |
| Mobile | 402 subscription flow | No Dio 402 interceptor or lock screen | Frozen users get broken UX | Undefined access | Add mobile subscription lock behavior aligned with web/API | P1 |
| Mobile | College/coaching teacher workflows | Attendance APIs are class/section only | Teachers in colleges/coaching cannot use attendance properly | School-only mobile model | Add academic-context-aware teacher attendance/homework/exam flows | P1 |
| Mobile | API contract documentation | TSV files are partial/mojibake | Mobile/backend drift | Integration risk | Replace TSVs with clear mobile API contract docs | P1 |
| RBAC | Per-route frontend guards | `App.tsx` broad protected route surface | Unauthorized screens visible by URL | UX/security gap | Add route permission metadata and AccessGuard enforcement | P1 |
| Testing | Tenant/institute IDOR tests | Some isolation tests exist, but institute header tampering tests are missing | High risk before deployment | Regression risk | Add integration tests for forged client/institute headers | P0 |
| Docs | Enterprise architecture docs | Existing docs are school-centric | Onboarding and audits fail | Misimplementation risk | Publish Trust/institution academic model and DB process docs | P1 |

## E. Ideal Target Architecture

The target model should preserve a simple modular monolith but remove overloaded concepts.

- `Trust`: legal/customer organization. Existing `Client` can evolve into this.
- `Institution`: one school, college, coaching center, training center, medical college, pharmacy college, engineering college, or campus. Existing `Institute` can evolve into this.
- `InstitutionType`: broad category such as School, DegreeCollege, EngineeringCollege, PharmacyCollege, MedicalCollege, NursingCollege, Polytechnic, ITI, CoachingInstitute, TrainingCenter, Custom.
- `AffiliationBody`: generic authority such as Board, University, Council, Skill body, or custom body.
- `Board`: school authority such as Maharashtra Board, CBSE, ICSE/CISCE, IB, Cambridge.
- `University`: university authority for affiliated colleges.
- `AcademicProgram`: institution offering such as B.Sc, B.Com, B.E., B.Pharm, MBBS, NEET Repeaters, JEE Foundation, Spoken English.
- `Course`: course/degree/package under a program.
- `Stream`: school or college stream such as Science, Commerce, Arts.
- `Department`: academic department such as Computer Engineering, Pharmacy, Anatomy.
- `AcademicBranch`: engineering/college branch such as CSE, Mechanical.
- `Batch`: operational cohort such as 2026-2030, FYJC 2026, JEE 2027 Morning.
- `Term`: generic period with type Year, Semester, Trimester, Quarter, Module, Custom.
- `Class`: school class/standard only.
- `Section`: school/coaching section/division only.
- `Subject`: global or institution-specific subject catalog entry.
- `SubjectOffering`: subject offered in a concrete academic context and academic year.
- `SyllabusTemplate`: global authoritative template.
- `SyllabusVersion`: board/university/course version with effective year, source, verification, and deprecation.
- `InstitutionSyllabusPlan`: institution-level imported copy/plan for a year/term/program.
- `SyllabusOverride`: institution customizations without mutating global templates.
- `FeePlan`: school class fee, college semester fee, coaching package fee, hostel/transport/add-on fee.
- `SubscriptionPlan`: SaaS billing plan at institution/client level.

Critical naming rule: physical institution/campus must not be called the same thing as college academic branch. Use `InstitutionId` or keep `InstituteId` only if the product language clearly defines Institute as physical institution. Use `AcademicBranchId` for CSE/Mechanical/etc.

## F. Database Plan Summary

Detailed database plan is in `DATABASE_SCRIPT_PLAN.md`.

Immediate database actions:

- Remove all runtime schema mutation from startup seeders.
- Add versioned manual SQL for every missing entity property and every removed/renamed column.
- Resolve `Syllabuses.TemplateId` schema/entity drift.
- Add unique and foreign-key constraints for global masters, academic structures, subscription histories, and role permissions.
- Add tenant/institute indexes for all high-volume tables.
- Add institute-membership validation tables if not already sufficient.
- Add script checksum enforcement in `SchemaVersions`.

## G. Backend Plan Summary

Backend modules to add or modify:

- Identity/Tenant Context: validate `ClientId` and `InstituteId` against authenticated assignments.
- Clients/Institutes: evolve terminology to Trust/Institution without breaking existing APIs.
- Academics: add explicit program/course/department/stream/term/subject-offering model.
- Syllabus: add version/import/override services and APIs.
- Exams/Timetable/Fees: replace class-only assumptions with academic context targeting.
- Subscriptions: add strict permissions, billing-safe freeze flows, renewal/payment history, overage jobs.
- RBAC: add permission keys for Trust Admin, Institution Admin, Academic Admin, Billing Admin, and SuperAdmin billing operations.
- Background jobs: subscription expiry/grace/overage snapshots, audit-log compaction, report pre-aggregation.
- Caching: global masters, permission maps, institution context, academic setup.

## H. Frontend Plan Summary

Frontend modules to add or modify:

- Rename user-facing "school" language to Trust/Institution where appropriate.
- Add validated Trust/Institution selector with role-aware scope.
- Add Academic Setup Wizard for school, college, coaching, and custom models.
- Build board/university/affiliation setup pages backed by real APIs.
- Build syllabus import, version compare, customization, and approval UI.
- Complete subscription screens for SuperAdmin, Trust Admin, and Institution Admin.
- Add permission-aware route guards, not just menu hiding.
- Add enterprise dashboard views for Trust-level and institution-level metrics.

Mobile plan:

- Store tokens in platform secure storage.
- Remove sensitive request/response logging from production builds.
- Add user/client/institution/student-scoped Isar caches.
- Add mobile 402 subscription interceptor and frozen/expired institution screen.
- Keep mobile role scope explicit: parent, student, and teacher for first release.
- Add institution-type-aware labels and workflows for school, college, and coaching.
- Align mobile API contracts with backend endpoints and generated clients.

## I. Testing Plan Summary

Detailed testing plan is in `TESTING_ROADMAP.md`.

Highest-priority tests:

- Forged `X-Client-Id` and `X-Institute-Id` integration tests.
- Subscription endpoint authorization tests.
- Frozen/expired institute billing-access tests.
- Syllabus institute/academic-institute mapping tests.
- Manual SQL script idempotency and schema/entity drift tests.
- Frontend direct-route RBAC tests.
- Mobile secure-token and cache-isolation tests.
- Mobile 402 subscription lock tests.
- Mobile role-routing tests for parent, student, teacher, and unsupported roles.
- Secret/config validation in CI.

## J. Phased Implementation Roadmap

Full roadmap is in `MISSING_IMPLEMENTATION_PLAN.md`.

Recommended phases:

1. Phase 0: Stop-risk audit and stabilization.
2. Phase 1: Fix tenant/institute isolation gaps.
3. Phase 2: Fix academic structure model.
4. Phase 3: Fix board/university/syllabus architecture.
5. Phase 4: Fix subscription and billing.
6. Phase 5: Enterprise frontend completion.
7. Phase 6: Performance/security hardening.
8. Phase 7: Production deployment readiness.

## K. Final Recommendation

Continue from the current implementation, but do not continue normal feature development yet.

The codebase has enough useful foundation to refactor forward instead of restarting. However, tenant/institute isolation, subscription authorization, schema discipline, academic modeling, and security configuration must be stabilized before any real Trust pilot.

Freeze feature development until at least these are done:

- Secrets removed and rotated.
- Subscription admin endpoints permission-gated.
- Runtime schema mutation removed.
- `InstituteId` ambiguity resolved in academics.
- SQL/entity drift fixed.
- Forged tenant/institute header tests passing.
- Frontend direct-route RBAC checks implemented for high-risk pages.
- Mobile token/cache handling reviewed before real parent/student data is used.

Before selling to a real Trust, complete Phases 0 through 4 at minimum, and run a controlled pilot only after tenant isolation, subscription lockout/payment, academic setup, RBAC, and database-script discipline are verified by tests.
