# Testing Roadmap

Date: 2026-05-03

## Current Coverage Summary

The repository has a useful test foundation, especially under `api/tests`. Existing coverage appears to include some finance/institution isolation tests, global master seeder tests, database migration service tests, client/institute/auth/controller tests, and limited frontend Playwright smoke flows.

The test suite is not yet enterprise-ready because the highest-risk SaaS paths are not fully covered:

- Forged tenant/institute headers.
- Subscription authorization and institute freeze behavior.
- Syllabus import/version/customization.
- SQL script/entity drift.
- Frontend direct-route RBAC.
- Mobile secure token storage, role routing, offline cache isolation, and subscription lockout.
- Production config safety.
- Performance at Trust/institution scale.

## Unit Tests

Add or extend:

- `UserContextService` institute/client resolution.
- Institute assignment validation service.
- `PermissionAuthorizationHandler` for Trust Admin, Institution Admin, SuperAdmin, Teacher, Accountant, Receptionist.
- `SubscriptionService` activation, renewal, freeze, unfreeze, grace, overage, and audit transitions.
- `RequireActiveInstituteSubscriptionFilter` allowed/blocked API matrix.
- Academic context validator for school/college/coaching/custom structures.
- Syllabus version selection by board/university/course/effective year.
- Syllabus override merge logic.
- Fee plan calculation for fixed, per-student, package, installment, concession, and overage cases.
- Cryptographic OTP generator after replacing `Random()`.
- Mobile auth state restoration and token expiry handling.
- Mobile cache key/partition helpers.
- Mobile 402 interceptor behavior.

## Integration Tests

Add or extend:

- SuperAdmin can access all trusts only through authorized SuperAdmin APIs.
- Trust Admin can access all institutions in own Trust and none outside.
- Institution Admin can access only assigned institution.
- Teacher can access only assigned institution/classes/batches.
- Forged `X-Client-Id` is rejected.
- Forged `X-Institute-Id` is rejected.
- Null institute context does not leak institute data to normal users.
- Subscription create/activate/renew/freeze/unfreeze require correct permissions.
- Frozen institute cannot access operational APIs.
- Frozen/expired institute can access only allowed billing APIs.
- School academic setup: board/class/section/subject.
- College academic setup: program/course/department/semester/CBCS subject offering.
- Coaching setup: course/package/batch/module.
- Exam schedule for school, college, and coaching contexts.
- Timetable for school, college, and coaching contexts.
- Fee plan creation and invoice generation for school, college, and coaching.
- Syllabus import from global template into institution plan.
- Institution syllabus override does not mutate global template.
- Mobile parent/student/teacher API scope tests.
- Mobile 402 subscription lock integration tests.

## E2E Frontend Tests

Add Playwright flows:

- SuperAdmin manages Trusts, institutions, plans, subscriptions, boards, universities.
- Trust Admin switches institutions and sees only own Trust institutions.
- Institution Admin cannot switch outside assigned institution.
- Direct URL access to unauthorized pages is blocked.
- Sidebar hides unauthorized modules and route guard blocks direct navigation.
- 402 subscription lock screen appears for frozen/expired institute.
- Billing-only pages remain accessible when operational pages are blocked.
- Academic setup wizard for school.
- Academic setup wizard for college.
- Academic setup wizard for coaching.
- Syllabus import/customization workflow.
- Fee plan setup by institution type.
- Student onboarding by institution type.
- Form validation and server error display.
- Empty states for no institutes, no academic year, no subscription, no students.

Mobile Flutter flows:

- Parent login routes to parent dashboard.
- Student login routes to student-safe dashboard.
- Teacher login routes to teacher dashboard.
- Unsupported role shows a safe no-access screen.
- Parent with multiple children can switch child safely.
- Child switch updates attendance, fees, homework, transport, hostel, syllabus, and exam context.
- Teacher can mark attendance for assigned school class/section.
- Teacher cannot mark attendance for unassigned class/batch.
- Offline mode shows scoped cached data only for the current user.
- Logout clears auth state and restricted cached data.
- Frozen/expired institution shows mobile subscription lock state.

## Seeder Tests

Add:

- Global master seeder can run twice without duplicates.
- Production seed does not create demo users, demo schools, or hardcoded credentials.
- Demo seed is opt-in.
- Boards seed includes expected state, national, international, and custom entries.
- Universities seed includes source/version metadata.
- Academic structure seed does not use semester-as-class or batch-as-section workarounds.
- Role/permission seed is repeat-safe.
- Permission keys are unique and stable.
- Seed rollback or cleanup path works in test environment.

## Database Script Tests

Add CI jobs:

- Apply all scripts to empty PostgreSQL database.
- Apply all scripts twice.
- Verify `SchemaVersions` rows and checksums.
- Fail on checksum mismatch for previously applied script.
- Verify expected tables, columns, constraints, indexes, and foreign keys.
- Compare schema to EF mapped model.
- Verify rollback or forward-fix notes for high-risk scripts.
- Verify scripts do not depend on demo seed data.
- Verify no startup path executes `ALTER TABLE` or `Database.MigrateAsync()` in manual-SQL mode.

## Security Tests

Add:

- Hardcoded secret scanner in CI.
- JWT expiry and refresh-token rotation tests.
- Refresh-token replay test.
- Password policy tests.
- Login rate-limit tests.
- OTP randomness and expiry tests.
- CORS configuration tests by environment.
- IDOR tests for every controller accepting route/query GUIDs.
- SQL injection tests for search/filter endpoints.
- XSS tests for rich text, remarks, notes, and uploaded file names.
- File upload content-type, size, extension, and malware-scan policy tests.
- Sensitive data redaction tests for logs and API errors.
- Production config refuses unsafe defaults.
- Mobile sensitive-log tests or static checks to prevent request/response body logging in production.
- Mobile secure-storage tests for tokens.
- Mobile shared-device/account-switch privacy tests.

## Performance Tests

Create representative data:

- 1 Trust with 50 institutions.
- Institution with 50,000 students.
- Multiple academic years.
- Large attendance, fee, exam, timetable, and syllabus data.

Add tests for:

- Student search/list pagination.
- Attendance marking and monthly report.
- Fee collection and outstanding report.
- Dashboard aggregation by Trust and institution.
- Syllabus tree loading.
- Timetable weekly view.
- Subscription overage snapshot.
- Export endpoints.
- Global master cache behavior.
- Mobile offline cache read/write under parent with multiple children.
- Mobile dashboard startup with large notice/homework/attendance datasets.

Minimum performance gates for pilot:

- Common list endpoints must be paginated.
- Dashboard APIs must complete within agreed SLA on seeded large data.
- No unbounded export should run in request thread for very large datasets.
- Query plans for high-volume tables must use tenant/institute/year indexes.

## Test Roadmap By Phase

| Phase | Required tests before completion |
|---|---|
| Phase 0 | Config safety remains open by request. Subscription authorization, database discipline/schema guard, forged header, and institute-filter regression tests are now added/passing. Mobile analyzer tests are blocked until Flutter/Dart is available. |
| Phase 1 | Trust isolation, institution isolation, institute assignment, direct API IDOR tests. Status: backend direct-header tests added and passing; keep as regression suite. |
| Phase 2 | Academic context unit/integration tests for school, college, coaching, custom. |
| Phase 3 | Syllabus version/import/override tests and seed idempotency tests. |
| Phase 4 | Subscription lifecycle, freeze/402, overage, payment/renewal history tests. |
| Phase 5 | Frontend RBAC, institution selector, setup wizard, lock screen E2E tests. |
| Phase 6 | Security suite and performance/load tests. |
| Phase 7 | Deployment smoke, restore rehearsal, production config validation, pilot E2E scenario. |

Mobile-specific phase gates:

| Phase | Required mobile tests before completion |
|---|---|
| Phase 0 | Secure token storage, production logging disabled, logout/cache clearing. |
| Phase 1 | Mobile role data scoped to assigned Trust/institution/student. |
| Phase 2 | Teacher mobile academic-context tests for school, college, coaching. |
| Phase 3 | Mobile syllabus list/detail scoped by selected student/context. |
| Phase 4 | Mobile 402 lock screen and subscription-state handling. |
| Phase 5 | Parent/student/teacher E2E flows on Android emulator and iOS simulator. |
| Phase 6 | Mobile privacy, offline, network failure, and performance tests. |
| Phase 7 | Signed dev/prod mobile build smoke tests. |

## Pilot Exit Criteria

Do not start a paid pilot until:

- All P0 tests pass in CI.
- Tenant/institute isolation tests pass for SuperAdmin, Trust Admin, Institution Admin, staff, and student roles.
- Subscription freeze/payment flows pass.
- School, college, and coaching academic setup flows pass.
- Manual SQL scripts can rebuild a fresh database.
- Seeders are repeat-safe.
- No production secrets are committed.
- Mobile app has passed secure storage, cache isolation, role routing, and subscription lock tests.
