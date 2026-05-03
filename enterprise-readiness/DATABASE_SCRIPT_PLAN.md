# Database Script Plan

Date: 2026-05-03

## Current Assessment

The repository has a manual SQL structure under `api/database/scripts/` and a `SchemaVersions` runner in `api/Core/Services/DatabaseMigrationService.cs`. This is a good start.

However, the current database discipline is not enterprise-safe because:

- `api/Persistence/DatabaseSeeder.cs` performs dynamic runtime `ALTER TABLE` changes.
- `DatabaseSeeder.ResetAndSeedAsync` calls `db.Database.MigrateAsync()`.
- SQL script `007_branch_academic_configuration.sql` adds `Syllabuses.TemplateId`, but `api/Modules/Academics/Entities/Syllabus.cs` does not define it.
- Some SQL scripts rely on later startup patching for columns such as `IsDeleted`.
- Rollback strategy is optional and not consistently present.
- Applied script checksum drift is not clearly rejected.

## Required Rules Going Forward

- Every schema change must be a numbered SQL script.
- No production startup code may create, alter, or drop tables/columns.
- EF migrations must remain disabled for schema changes.
- Every script must be idempotent.
- Every script must have a verification query.
- High-risk scripts must have a rollback or forward-fix note.
- `SchemaVersions` must store and verify script checksum.
- CI must compare EF model expectations with database schema.

## New Tables Required

| Table | Purpose |
|---|---|
| `AffiliationBodies` | Generic board/university/council/authority master. |
| `InstitutionTypes` | Rich institution classifications beyond School/College/Coaching. |
| `AcademicPrograms` | Institution offerings such as B.Sc, B.E., JEE, Secondary School. |
| `Courses` | Specific courses/degrees/packages under programs. |
| `Streams` | Science, Commerce, Arts, vocational streams. |
| `Departments` | Academic departments. |
| `AcademicBranches` | College branches/specializations such as CSE/Mechanical. |
| `AcademicTerms` | Year/Semester/Class/Module/Custom period abstraction. |
| `SubjectOfferings` | Subject in concrete institution/year/program/term context. |
| `SyllabusVersions` | Versioned published syllabus data. |
| `SyllabusNodes` | Unit/chapter/topic/outcome tree for a syllabus version. |
| `InstitutionSyllabusPlans` | Institution imported syllabus plan for an academic context. |
| `SyllabusOverrides` | Institution-level customizations. |
| `FeePlans` | Unified fee plan for school/college/coaching contexts. |
| `SubscriptionStudentSnapshots` | Student-count snapshots for billing. |
| `SubscriptionOverageEvents` | Overage tracking. |
| `UserInstitutionAssignments` | Explicit user-to-institution access where not already represented. |

## Tables To Modify

| Table | Modification |
|---|---|
| `Clients` | Clarify Trust-level fields; move school-specific fields out over time. |
| `Institutes` | Clarify as Institution; add institution type/affiliation metadata if not already present. |
| `Syllabuses` | Resolve `TemplateId`; add `InstitutionSyllabusPlanId` or migrate to new plan model. |
| `TimeTables` | Separate physical InstituteId from academic AcademicBranchId. |
| `ExamSchedules` | Remove required school-only `ClassRoomId` dependency for non-school contexts. |
| `FeeStructures` | Migrate toward `FeePlans`; avoid sentinel `Guid.Empty` semantics. |
| `Subjects` | Link to global subject/subject offering model. |
| `RolePermissions` | Add missing billing/academic/syllabus permissions. |
| `InstituteSubscriptions` or equivalent | Add grace/freeze/payment/renewal audit completeness if missing. |
| `SchemaVersions` | Add checksum verification metadata and applied environment/user if absent. |

## Constraints Required

Add or verify:

- Unique `Clients.Name` or legal registration constraints as business allows.
- Unique institution code per Trust.
- Unique board/university codes.
- Unique academic program name/code per institution.
- Unique course code per program.
- Unique academic branch code per course.
- Unique term sequence per course/academic branch.
- Unique subject offering per institution/year/context/subject.
- Foreign keys from operational records to institution and academic year.
- Foreign keys from subscription records to Trust/institution/plan.
- Foreign keys from syllabus plans to syllabus versions.
- Prevent duplicate active subscription per institution unless business explicitly supports it.
- Prevent duplicate role-permission rows.

## Indexes Required

High-priority indexes:

- `(ClientId, InstituteId)` or `(TrustId, InstitutionId)` on every tenant/institute-scoped high-volume table.
- `(ClientId, InstituteId, AcademicYearId)` on students, attendance, fees, exams, timetable, syllabus.
- `(InstituteId, SubscriptionStatus, EndDate)` on subscription tables.
- `(AcademicYearId, ClassId, SectionId)` for school operational tables.
- `(AcademicYearId, ProgramId, CourseId, AcademicBranchId, TermId, BatchId)` for college/coaching operational tables.
- `(StudentId, AcademicYearId)` on fee, attendance, exam result tables.
- `(SubjectOfferingId)` on syllabus, timetable, exam, attendance mappings.
- GIN or trigram indexes for large searchable names if PostgreSQL extensions are approved.

## Proposed Manual SQL Scripts

Use the existing numeric order after `009_add_trust_name_to_clients.sql`.

| Script | Purpose |
|---|---|
| `010_fix_schema_drift_and_remove_runtime_patch_dependencies.sql` | Add missing base columns explicitly, resolve `Syllabuses.TemplateId`, remove dependency on seeder DDL. |
| `011_harden_schema_versions_checksum.sql` | Add checksum validation fields and uniqueness. |
| `012_institution_type_and_affiliation_bodies.sql` | Add rich institution types and affiliation bodies. |
| `013_academic_program_course_department_terms.sql` | Add program/course/stream/department/academic branch/term tables. |
| `014_subject_offerings.sql` | Add subject offering model and indexes. |
| `015_syllabus_versions_and_nodes.sql` | Add syllabus versions and normalized tree. |
| `016_institution_syllabus_plans_and_overrides.sql` | Add import/customization model. |
| `017_exam_timetable_academic_context.sql` | Add clear academic-context columns and migrate from overloaded institute/class assumptions. |
| `018_fee_plans_unified_academic_context.sql` | Add unified fee plans and migration bridge from fee structures. |
| `019_subscription_hardening.sql` | Add missing snapshots, overage, audit fields, and constraints. |
| `020_tenant_branch_scale_indexes.sql` | Add high-volume tenant/institute/year indexes. |
| `021_user_institution_assignments.sql` | Add explicit user-institution access table if current role/user model is insufficient. |
| `022_cleanup_legacy_school_only_columns.sql` | Carefully deprecate or move school-only fields after compatibility window. |

## Seed Script Strategy

Separate seed data into categories:

- System seed: roles, permissions, modules, default SuperAdmin setup.
- Global master seed: boards, universities, affiliation bodies, academic levels, global subjects.
- Demo seed: sample Trust, institutions, users, students, fees.
- Test seed: deterministic fixtures for automated tests.

Rules:

- Production startup must not run demo seed.
- Demo seed must be explicitly requested.
- Seed scripts must be repeat-safe.
- Seed scripts must not depend on local database state.
- Hardcoded passwords must be allowed only in local/test seed, never production.
- Global master seed must include source/version metadata where possible.

## Migration Safety Checklist

Before applying a script:

- Confirm script is numbered and named meaningfully.
- Confirm script is idempotent.
- Confirm script has a verification query.
- Confirm foreign keys and indexes are included.
- Confirm data migration is batched for large tables.
- Confirm locks and expected runtime are documented.
- Confirm rollback or forward-fix strategy is documented.
- Confirm EF entities, DTOs, services, and frontend API models are updated in the same phase.
- Confirm tests cover before/after behavior.

After applying a script:

- Verify `SchemaVersions` row exists with checksum.
- Verify expected columns, constraints, and indexes.
- Run schema/entity drift test.
- Run tenant/institute isolation tests.
- Run critical workflow smoke tests.

## CI Checks To Add

- Run all SQL scripts against an empty PostgreSQL database.
- Run all SQL scripts twice to prove idempotency.
- Run seed scripts twice to prove repeat safety.
- Compare applied schema to EF model metadata for mapped columns.
- Fail if appsettings contain known secret-like values.
- Fail if `Database.MigrateAsync()` or runtime `ALTER TABLE` appears in production startup path.
