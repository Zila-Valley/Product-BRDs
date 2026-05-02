# Testing Roadmap

Date: 2026-05-03

## Current Coverage Summary

The repository has a useful test foundation, especially under `api/tests`. Existing coverage appears to include some finance/institution isolation tests, global master seeder tests, database migration service tests, client/branch/auth/controller tests, and limited frontend Playwright smoke flows.

The test suite is not yet enterprise-ready because the highest-risk SaaS paths are not fully covered:

- Forged tenant/branch headers.
- Subscription authorization and branch freeze behavior.
- Syllabus import/version/customization.
- SQL script/entity drift.
- Frontend direct-route RBAC.
- Production config safety.
- Performance at Trust/institution scale.

## Unit Tests

Add or extend:

- `UserContextService` branch/client resolution.
- Branch assignment validation service.
- `PermissionAuthorizationHandler` for Trust Admin, Institution Admin, SuperAdmin, Teacher, Accountant, Receptionist.
- `SubscriptionService` activation, renewal, freeze, unfreeze, grace, overage, and audit transitions.
- `RequireActiveBranchSubscriptionFilter` allowed/blocked API matrix.
- Academic context validator for school/college/coaching/custom structures.
- Syllabus version selection by board/university/course/effective year.
- Syllabus override merge logic.
- Fee plan calculation for fixed, per-student, package, installment, concession, and overage cases.
- Cryptographic OTP generator after replacing `Random()`.

## Integration Tests

Add or extend:

- SuperAdmin can access all trusts only through authorized SuperAdmin APIs.
- Trust Admin can access all institutions in own Trust and none outside.
- Institution Admin can access only assigned institution.
- Teacher can access only assigned institution/classes/batches.
- Forged `X-Client-Id` is rejected.
- Forged `X-Branch-Id` is rejected.
- Null branch context does not leak branch data to normal users.
- Subscription create/activate/renew/freeze/unfreeze require correct permissions.
- Frozen branch cannot access operational APIs.
- Frozen/expired branch can access only allowed billing APIs.
- School academic setup: board/class/section/subject.
- College academic setup: program/course/department/semester/CBCS subject offering.
- Coaching setup: course/package/batch/module.
- Exam schedule for school, college, and coaching contexts.
- Timetable for school, college, and coaching contexts.
- Fee plan creation and invoice generation for school, college, and coaching.
- Syllabus import from global template into institution plan.
- Institution syllabus override does not mutate global template.

## E2E Frontend Tests

Add Playwright flows:

- SuperAdmin manages Trusts, institutions, plans, subscriptions, boards, universities.
- Trust Admin switches institutions and sees only own Trust institutions.
- Institution Admin cannot switch outside assigned institution.
- Direct URL access to unauthorized pages is blocked.
- Sidebar hides unauthorized modules and route guard blocks direct navigation.
- 402 subscription lock screen appears for frozen/expired branch.
- Billing-only pages remain accessible when operational pages are blocked.
- Academic setup wizard for school.
- Academic setup wizard for college.
- Academic setup wizard for coaching.
- Syllabus import/customization workflow.
- Fee plan setup by institution type.
- Student onboarding by institution type.
- Form validation and server error display.
- Empty states for no branches, no academic year, no subscription, no students.

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

Minimum performance gates for pilot:

- Common list endpoints must be paginated.
- Dashboard APIs must complete within agreed SLA on seeded large data.
- No unbounded export should run in request thread for very large datasets.
- Query plans for high-volume tables must use tenant/branch/year indexes.

## Test Roadmap By Phase

| Phase | Required tests before completion |
|---|---|
| Phase 0 | Config safety, subscription authorization, schema drift, SQL idempotency, forged header tests. |
| Phase 1 | Trust isolation, institution isolation, branch assignment, direct API IDOR tests. |
| Phase 2 | Academic context unit/integration tests for school, college, coaching, custom. |
| Phase 3 | Syllabus version/import/override tests and seed idempotency tests. |
| Phase 4 | Subscription lifecycle, freeze/402, overage, payment/renewal history tests. |
| Phase 5 | Frontend RBAC, institution selector, setup wizard, lock screen E2E tests. |
| Phase 6 | Security suite and performance/load tests. |
| Phase 7 | Deployment smoke, restore rehearsal, production config validation, pilot E2E scenario. |

## Pilot Exit Criteria

Do not start a paid pilot until:

- All P0 tests pass in CI.
- Tenant/branch isolation tests pass for SuperAdmin, Trust Admin, Institution Admin, staff, and student roles.
- Subscription freeze/payment flows pass.
- School, college, and coaching academic setup flows pass.
- Manual SQL scripts can rebuild a fresh database.
- Seeders are repeat-safe.
- No production secrets are committed.
