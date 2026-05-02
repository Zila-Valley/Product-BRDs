# Academic Model Target Architecture

Date: 2026-05-03

## Design Principles

- Model the customer as a Trust/education group, not a school.
- Model each school, college, coaching center, or training center as an institution.
- Never overload physical institution branch with academic branch such as CSE or Mechanical.
- Use one academic context abstraction across attendance, exams, timetable, fees, students, reports, and syllabus.
- Keep school-specific concepts such as Class and Section, but do not force colleges and coaching institutes into them.
- Allow custom institution-defined structures without losing reporting consistency.
- Keep global syllabus templates immutable after publication; customize only through institution-level copies/overrides.

## Core Entities

### Trust

Current likely source: `api/Core/Entities/Client.cs`

Target meaning: legal customer organization or education group.

Recommended fields:

- `Id`
- `Name`
- `LegalName`
- `TrustName`
- `RegistrationNumber`
- `BillingContact`
- `PrimaryContact`
- `Address`
- `Status`
- `CreatedAt`

Move institution-specific school fields such as board and affiliation out of Trust.

### Institution

Current likely source: `api/Modules/Academics/Entities/Branch.cs`

Target meaning: one school, college, coaching institute, training center, medical college, pharmacy college, engineering college, or campus.

Recommended fields:

- `Id`
- `TrustId`
- `Name`
- `InstitutionCode`
- `InstitutionTypeId`
- `AffiliationBodyId`
- `BoardId`
- `UniversityId`
- `Address`
- `AcademicConfigurationMode`
- `SubscriptionStatus`
- `Status`

If the database keeps the name `BranchId` for compatibility, API and frontend should still expose it as institution where possible.

### InstitutionType

Recommended values:

- School
- DegreeCollege
- EngineeringCollege
- PharmacyCollege
- MedicalCollege
- NursingCollege
- Polytechnic
- ITI
- DiplomaInstitute
- CoachingInstitute
- TrainingCenter
- SkillDevelopmentCenter
- Custom

The current enum `School`, `College`, `CoachingClass` is too coarse for enterprise setup, reports, and permissions.

### AffiliationBody

Generic authority table.

Recommended fields:

- `Id`
- `Name`
- `Type`: Board, University, Council, SkillAuthority, Autonomous, Custom
- `Country`
- `State`
- `Website`
- `IsActive`

`Board` and `University` can either remain specialized tables linked to `AffiliationBody`, or be represented as typed affiliation bodies with specific metadata tables.

### Board

For schools.

Recommended fields:

- `Id`
- `AffiliationBodyId`
- `Name`
- `Code`
- `Country`
- `State`
- `CurriculumType`
- `Website`
- `IsActive`

Examples:

- Maharashtra State Board
- Karnataka State Board
- Telangana State Board
- Goa Board
- CBSE
- ICSE/CISCE
- IB
- Cambridge/IGCSE
- Custom board

### University

For colleges.

Recommended fields:

- `Id`
- `AffiliationBodyId`
- `Name`
- `Code`
- `State`
- `UniversityType`
- `Website`
- `IsActive`

### AcademicProgram

Represents a major offering by an institution.

Examples:

- SSC School
- HSC Science
- B.Sc
- B.Com
- B.A
- BBA
- BCA
- B.E.
- B.Pharm
- MBBS
- Nursing
- Diploma Mechanical
- ITI Electrician
- JEE Two Year
- NEET Repeater
- Spoken English

Recommended fields:

- `Id`
- `TrustId`
- `InstitutionId`
- `Name`
- `ProgramType`
- `DurationValue`
- `DurationUnit`
- `AffiliationBodyId`
- `BoardId`
- `UniversityId`
- `AcademicSystem`: ClassSection, YearBased, SemesterBased, CBCS, BatchBased, ModuleBased, Custom
- `IsActive`

### Course

Specific degree/course/package under a program.

Examples:

- B.Sc Computer Science
- B.Com General
- B.E. Computer Engineering
- B.Pharm
- JEE Advanced Package

Recommended fields:

- `Id`
- `ProgramId`
- `Name`
- `Code`
- `CourseType`
- `Duration`
- `CreditRequirement`
- `IsActive`

### Stream

Useful for school and degree colleges.

Examples:

- Science
- Commerce
- Arts
- Vocational

Recommended fields:

- `Id`
- `ProgramId`
- `Name`
- `Code`

### Department

Academic department.

Examples:

- Computer Science
- Mechanical Engineering
- Commerce
- English
- Anatomy
- Pharmacology

Recommended fields:

- `Id`
- `InstitutionId`
- `Name`
- `Code`
- `HeadStaffId`

### AcademicBranch

College branch/specialization. This must not be confused with physical institution branch.

Examples:

- Computer Engineering
- Mechanical Engineering
- Civil Engineering
- Pharmaceutics

Recommended fields:

- `Id`
- `CourseId`
- `DepartmentId`
- `Name`
- `Code`

### Term

Generic academic period.

Examples:

- Class 10
- FY B.Com
- Semester 1
- Year 2
- Module 3

Recommended fields:

- `Id`
- `ProgramId`
- `CourseId`
- `AcademicBranchId`
- `TermType`: Class, Year, Semester, Trimester, Quarter, Module, Custom
- `Name`
- `SequenceNumber`
- `CreditRequirement`

### Class and Section

Keep these for schools and coaching divisions where they are natural.

Recommended fields:

- `Class.Id`
- `Class.InstitutionId`
- `Class.BoardId`
- `Class.StreamId`
- `Class.Name`
- `Class.SequenceNumber`
- `Section.Id`
- `Section.ClassId`
- `Section.Name`
- `Section.Capacity`

Colleges should not be forced into `Class` and `Section`.

### Batch

Operational cohort.

Examples:

- 2026-2030
- FY B.Com 2026
- JEE 2027 Morning
- Spoken English Weekend

Recommended fields:

- `Id`
- `InstitutionId`
- `ProgramId`
- `CourseId`
- `AcademicBranchId`
- `TermId`
- `Name`
- `StartDate`
- `EndDate`
- `Capacity`
- `Status`

### Subject

Subject catalog.

Recommended fields:

- `Id`
- `TrustId` nullable
- `InstitutionId` nullable
- `GlobalSubjectId` nullable
- `Name`
- `Code`
- `SubjectType`
- `Credits`
- `IsElective`
- `IsPractical`
- `IsActive`

### SubjectOffering

Concrete subject offered in an academic context.

Recommended fields:

- `Id`
- `InstitutionId`
- `AcademicYearId`
- `ProgramId`
- `CourseId`
- `AcademicBranchId`
- `TermId`
- `ClassId`
- `SectionId`
- `BatchId`
- `SubjectId`
- `TeacherId`
- `Credits`
- `IsElective`

This is the right place to represent CBCS and university semester subjects.

## Syllabus Architecture

### SyllabusTemplate

Global or authority-level syllabus definition.

Recommended fields:

- `Id`
- `AffiliationBodyId`
- `BoardId`
- `UniversityId`
- `ProgramType`
- `CourseId`
- `TermType`
- `SubjectId`
- `Title`
- `Status`: Draft, Published, Deprecated
- `CreatedBy`

### SyllabusVersion

Published version of a template.

Recommended fields:

- `Id`
- `TemplateId`
- `Version`
- `EffectiveAcademicYearId`
- `EffectiveFrom`
- `EffectiveTo`
- `SourceUrl`
- `SourceDocumentHash`
- `IsVerified`
- `VerifiedBy`
- `VerifiedAt`
- `DeprecatedAt`
- `DeprecationReason`

### SyllabusNode

Tree under a syllabus version.

Recommended fields:

- `Id`
- `SyllabusVersionId`
- `ParentId`
- `NodeType`: Unit, Chapter, Topic, Subtopic, LearningOutcome, Assessment
- `Title`
- `Description`
- `Sequence`
- `EstimatedHours`
- `LearningOutcome`

### InstitutionSyllabusPlan

Institution-level imported syllabus for an academic context.

Recommended fields:

- `Id`
- `InstitutionId`
- `AcademicYearId`
- `ProgramId`
- `CourseId`
- `AcademicBranchId`
- `TermId`
- `ClassId`
- `SectionId`
- `BatchId`
- `SubjectOfferingId`
- `SyllabusVersionId`
- `ImportedAt`
- `ImportedBy`
- `Status`

### SyllabusOverride

Institution customization without mutating the global template.

Recommended fields:

- `Id`
- `InstitutionSyllabusPlanId`
- `SyllabusNodeId`
- `OverrideType`: Add, Hide, Modify, Reorder
- `OverridePayload`
- `Reason`
- `ApprovedBy`
- `ApprovedAt`

## Fee Architecture

### FeePlan

Recommended fee plan targets:

- Institution-level annual fee
- School class/section fee
- College program/course/term fee
- Coaching package/batch fee
- Hostel, transport, exam, library, lab, and custom add-ons

Recommended fields:

- `Id`
- `TrustId`
- `InstitutionId`
- `AcademicYearId`
- `ProgramId`
- `CourseId`
- `TermId`
- `ClassId`
- `BatchId`
- `Name`
- `BillingCycle`
- `PricingMode`
- `Amount`
- `InstallmentPolicyId`
- `IsActive`

## Subscription Architecture

### SubscriptionPlan

Recommended fields:

- `Id`
- `Name`
- `PricingModel`: FixedYearly, PerStudentYearly, Hybrid
- `BaseAmount`
- `PerStudentAmount`
- `IncludedStudents`
- `GraceDays`
- `Features`
- `IsActive`

### InstitutionSubscription

Recommended fields:

- `Id`
- `TrustId`
- `InstitutionId`
- `PlanId`
- `Status`
- `TrialStartDate`
- `TrialEndDate`
- `StartDate`
- `EndDate`
- `GraceUntil`
- `FrozenAt`
- `FrozenBy`
- `FreezeReason`
- `CurrentStudentSnapshot`
- `OverageAmount`

## Example Academic Contexts

### Maharashtra State Board School

- Trust: ABC Education Trust
- Institution: ABC High School
- InstitutionType: School
- Board: Maharashtra State Board
- Program: Secondary School
- Class: Class 10
- Section: A
- SubjectOffering: Mathematics for Class 10 A, Academic Year 2026-27
- SyllabusPlan: Maharashtra Board Class 10 Mathematics 2026 version

### Engineering College

- Trust: ABC Education Trust
- Institution: ABC Engineering College
- InstitutionType: EngineeringCollege
- University: Mumbai University
- Program: B.E.
- Course: B.E. Computer Engineering
- AcademicBranch: Computer Engineering
- Term: Semester 5
- Batch: 2024-2028
- SubjectOffering: Database Management Systems, 4 credits
- SyllabusPlan: MU Computer Engineering Semester 5 DBMS 2026 version

### Coaching Institute

- Trust: ABC Education Trust
- Institution: ABC Coaching Center
- InstitutionType: CoachingInstitute
- Program: JEE
- Course: JEE Two Year Integrated
- Batch: JEE 2027 Morning
- Term: Module 1
- SubjectOffering: Physics Mechanics
- FeePlan: JEE Two Year Package with installments

## Backend API Rule

Every operational API should accept one clear academic context object rather than many optional overloaded IDs.

Recommended DTO shape:

```json
{
  "institutionId": "required",
  "academicYearId": "required",
  "programId": "optional",
  "courseId": "optional",
  "streamId": "optional",
  "departmentId": "optional",
  "academicBranchId": "optional",
  "termId": "optional",
  "classId": "optional",
  "sectionId": "optional",
  "batchId": "optional"
}
```

Validation must be institution-type aware.
