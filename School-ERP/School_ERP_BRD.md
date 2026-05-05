# Business Requirements Document (BRD) - Enterprise Education ERP SaaS

## 1. Executive Summary

This document defines the business requirements for an Enterprise Education ERP SaaS platform for Indian education organizations.

The product must support a **Trust / Education Group / Organization** that manages one or many institutions. An institution may be a school, junior college, degree college, engineering college, pharmacy college, medical college, nursing college, polytechnic, ITI, coaching institute, training center, or any custom education unit.

The ERP should help the organization manage students, admissions, academics, attendance, fees, staff, payroll, exams, reports, communication, subscriptions, and compliance from one centralized system.

The product has three user channels:

- **Web Admin Portal:** Main operational portal for SuperAdmin, Trust Admin, Institution Admin, accountants, HR, academic coordinators, and office staff.
- **Hybrid Mobile App:** Flutter-based Kaksha+ mobile app for parents, students, and teachers, with Android and iOS support.
- **Backend API:** Shared API layer used by web and mobile apps.

The product must be simple enough for a single school to use, but strong enough for a large Trust with 50+ institutions and thousands or lakhs of students.

### Simple Example

ABC Education Trust may run:

- ABC High School under Maharashtra State Board.
- ABC Junior College with Science and Commerce streams.
- ABC Engineering College affiliated to Mumbai University.
- ABC Pharmacy College affiliated to a pharmacy council/university.
- ABC NEET Coaching Center with morning and evening batches.

The ERP must allow the Trust owner to see all institutions, while each institution administrator sees only their assigned institution.

## 2. Product Vision

The goal is to build a sellable SaaS ERP for Indian education groups that can handle different academic systems without forcing every institution into a school-only model.

The system must support:

- Schools with board, class, section, subject, timetable, exams, and syllabus.
- Colleges with course, stream, department, year, semester, credits, electives, and university syllabus.
- Coaching institutes with courses, packages, batches, modules, and flexible fee plans.
- Training institutes with short-term programs, skill courses, and custom structures.
- Trust-level management across multiple institutions.
- Institution-level isolation for daily operations.

## 3. Key Business Terms

| Term | Meaning | Example |
|---|---|---|
| Trust / Client / Organization | The paying customer or education group. | ABC Education Trust |
| Institute / Institution | One school, college, coaching center, or campus under a Trust. | ABC Engineering College |
| Institution Type | Category of institution. | School, Engineering College, Coaching Institute |
| Board | School education authority. | CBSE, Maharashtra State Board, ICSE |
| University | College affiliation authority. | Mumbai University, VTU |
| Affiliation Body | Any board, university, council, skill authority, or custom authority. | Pharmacy Council, Nursing Council |
| Academic Program | Major offering by an institution. | B.Com, B.E., NEET Two Year |
| Course | Specific course or package. | B.E. Computer Engineering, JEE Advanced Package |
| Stream | Academic stream. | Science, Commerce, Arts |
| Department | Academic department. | Computer Engineering, Commerce, Anatomy |
| Academic Branch | College specialization. | CSE, Mechanical, Civil |
| Class | School class or standard. | Class 10 |
| Section | Division within a class. | Class 10-A |
| Batch | Group of students for a course/session. | JEE 2027 Morning Batch |
| Semester / Year / Term | Academic period. | Semester 5, First Year, Module 2 |
| Syllabus Template | Global syllabus from board/university/source. | CBSE Class 10 Mathematics 2026 |
| Institution Syllabus Plan | Institution copy/customization of a syllabus. | ABC High School Class 10 Math Plan |
| Subscription Plan | SaaS billing plan for using the ERP. | Per-student yearly plan |
| Web Admin Portal | Browser-based application for administrative and operational work. | Trust Admin dashboard |
| Hybrid Mobile App | Android/iOS mobile app built from one shared codebase. | Kaksha+ parent/teacher app |

## 4. Product Scope

### 4.1 In Scope

The ERP must support these business areas:

- Trust and institution management.
- User, role, and permission management.
- Academic year and academic setup.
- Admissions and student onboarding.
- Student information system.
- Staff and HR management.
- Attendance for students and staff.
- Fee management and finance.
- Exams and grading.
- Syllabus and teaching progress.
- Timetable.
- Communication and notices.
- Front office.
- Library, hostel, transport, inventory, and assets.
- Institute/institution-level SaaS subscription management.
- Reports and dashboards.
- Audit logs and compliance records.
- Web admin portal for full ERP operations.
- Hybrid mobile app for parent, student, and teacher usage.

### 4.2 Out of Scope For First Enterprise Release

These may be added later unless specifically approved:

- Full accounting replacement for Tally or enterprise finance suites.
- Government statutory filing automation.
- Advanced LMS content authoring.
- AI-based student performance prediction.
- Marketplace integrations.
- Full offline ERP operation without internet.
- Separate native Android and iOS apps with different codebases.

## 5. Product Channels

The ERP must be available through web and mobile channels. Both channels must use the same backend API and must follow the same Trust, institution, role, and permission rules.

### 5.1 Web Admin Portal

The web portal is the primary application for administration and management.

Expected web users:

- SuperAdmin.
- Trust Admin.
- Institution Admin.
- Principal / Director.
- Academic Coordinator.
- Accountant.
- HR.
- Receptionist.
- Librarian.
- Transport In-charge.
- Hostel Warden.
- Office staff.

Typical web tasks:

- Create Trusts and institutions.
- Configure academic structures.
- Manage admissions.
- Configure fees.
- Collect and reconcile fees.
- Manage staff and payroll.
- Create exams and publish results.
- Manage subscriptions.
- Generate reports.

### 5.2 Hybrid Mobile App

The mobile app is an active product channel. It is being developed as a Flutter hybrid app under the `school-mobile` repository and should support Android and iOS.

Expected mobile users:

- Parents.
- Students.
- Teachers.

The mobile app should focus on daily, high-frequency tasks that users naturally perform on phones.

Parent mobile examples:

- View child profile.
- View attendance.
- View homework.
- View exam results.
- View fee status.
- View notices.
- View transport details.
- View hostel details where applicable.

Student mobile examples:

- View timetable.
- View attendance.
- View homework and assignments.
- View syllabus and study progress.
- View exams and results.
- Download academic documents such as admit cards or PDFs.

Teacher mobile examples:

- View assigned classes or batches.
- Mark attendance.
- Add homework.
- View exam schedules.
- Enter marks where allowed.
- View result sheets.
- Access student lists.

### 5.3 Channel Responsibility

| Feature Area | Web Portal | Mobile App |
|---|---|---|
| Trust and institution setup | Primary | Not required initially |
| Subscription management | Primary | View-only or billing alert if needed |
| Admissions | Primary | Optional inquiry/follow-up later |
| Student profile | Full management | View own/child profile |
| Attendance | Full management and reports | Teacher marking, parent/student viewing |
| Fees | Full setup and collection | Parent/student fee status and payment later |
| Homework | Full oversight | Teacher creation, student/parent viewing |
| Exams | Full setup and publishing | Teacher marks entry, student/parent viewing |
| Notices | Full creation | Viewing and notifications |
| Transport | Full setup | Student/parent route/allocation view |
| Hostel | Full setup | Student/parent hostel details view |
| Reports | Full reports | Simple summaries only |

Mobile must not bypass backend permissions. If the backend does not allow an action for a role, the mobile app must also block it.

## 6. Stakeholders

| Stakeholder | What They Need |
|---|---|
| Trust Owner / Trustee | Overall view of all institutions, finances, admissions, staff, and performance. |
| SuperAdmin | SaaS-level control over all Trusts, subscriptions, plans, global masters, and system settings. |
| Trust Admin | Manage all institutions under one Trust. |
| Institution Admin | Manage one assigned school/college/coaching center. |
| Principal / Director | Academic oversight, reports, staff monitoring, student performance. |
| Academic Coordinator | Timetable, subjects, syllabus, exams, teacher allocation. |
| Teacher / Faculty | Attendance, homework, syllabus progress, marks entry, communication. |
| Student | Mobile-first access to timetable, attendance, fees, exams, results, homework, syllabus, notices, and documents. |
| Parent / Guardian | Mobile-first access to child's progress, attendance, fees, communication, transport, hostel, notices, and results. |
| Accountant | Fee setup, collection, concessions, receipts, dues, finance reports. |
| HR | Staff profile, attendance, leave, payroll. |
| Receptionist / Front Office | Inquiries, follow-ups, visitors, calls, postal records. |
| Librarian | Books, issue/return, fines, members. |
| Transport In-charge | Routes, vehicles, stops, allocation, maintenance. |
| Hostel Warden | Hostel rooms, allocation, attendance, complaints. |
| Support Staff | Assigned operational tasks based on permissions. |

## 7. Institution Types To Support

The ERP must not assume that every institution is a school.

### 7.1 Schools

Must support:

- Maharashtra State Board.
- Karnataka State Board.
- Telangana State Board.
- Goa Board.
- CBSE.
- ICSE/CISCE.
- IB.
- Cambridge/IGCSE.
- Custom boards.

School structure example:

ABC High School  
Board: Maharashtra State Board  
Academic Year: 2026-27  
Class: 10  
Section: A  
Subjects: Mathematics, Science, English, Marathi, History  

### 7.2 Colleges

Must support:

- B.Sc.
- B.Com.
- B.A.
- BBA.
- BCA.
- Engineering.
- Pharmacy.
- Medical.
- Nursing.
- Polytechnic.
- ITI.
- Diploma.
- Postgraduate courses.
- Year-based systems.
- Semester-based systems.
- Credit-based CBCS systems.
- University-affiliated syllabus.

College structure example:

ABC Engineering College  
University: Mumbai University  
Program: B.E.  
Course: Computer Engineering  
Batch: 2024-2028  
Term: Semester 5  
Subject: Database Management Systems  
Credits: 4  

### 7.3 Coaching And Training Institutes

Must support:

- JEE.
- NEET.
- MPSC/UPSC.
- Banking.
- Spoken English.
- IT courses.
- Skill development courses.
- Batch-based structure.
- Package-based fees.

Coaching structure example:

ABC NEET Academy  
Course: NEET Repeater Program  
Batch: Morning Batch  
Module: Physics Mechanics  
Fee Plan: One-year package with installments  

## 8. Multi-Tenant Business Model

The platform is a SaaS product.

### 8.1 Trust-Level Isolation

Data of one Trust must never be visible to another Trust.

Example:

ABC Education Trust and XYZ Education Society are separate customers. ABC users must never see XYZ students, staff, fees, reports, or settings.

### 8.2 Institution-Level Isolation

Within the same Trust, an institution user should see only their assigned institution unless their role allows more.

Example:

ABC Trust has three institutions:

- ABC High School.
- ABC Engineering College.
- ABC Coaching Center.

The Engineering College admin must not see High School fee records. A Trust Admin may see all three institutions.

### 8.3 Role-Based Scope

| Role | Scope |
|---|---|
| SuperAdmin | All Trusts and all institutions. |
| Trust Admin | All institutions under assigned Trust. |
| Institution Admin | Assigned institution only. |
| Teacher / Faculty | Assigned classes, batches, subjects, or students only. |
| Accountant | Assigned institution finance data only unless Trust-level finance role is given. |
| Parent | Own children only. |
| Student | Own profile only. |

## 9. User Roles And Permissions

The ERP must use role-based access control. A role decides what a user can view, create, edit, approve, export, or delete.

### 9.1 Standard Roles

- SuperAdmin.
- Trust Admin.
- Institution Admin.
- Principal / Director.
- Academic Coordinator.
- Teacher / Faculty.
- Accountant.
- HR.
- Receptionist / Front Office.
- Librarian.
- Transport In-charge.
- Hostel Warden.
- Student.
- Parent.

### 9.2 Permission Examples

| Permission | Who Usually Gets It |
|---|---|
| Manage Trusts | SuperAdmin |
| Manage Institutions | SuperAdmin, Trust Admin |
| Manage Academic Setup | Institution Admin, Academic Coordinator |
| Manage Admissions | Institution Admin, Receptionist |
| Approve Admissions | Institution Admin, Principal |
| Collect Fees | Accountant |
| Approve Fee Concession | Principal, Institution Admin |
| Mark Attendance | Teacher |
| Enter Exam Marks | Teacher |
| Publish Results | Principal, Academic Coordinator |
| Manage Subscription Plans | SuperAdmin |
| Renew Institution Subscription | SuperAdmin, Trust Admin, authorized billing user |

## 10. Core Functional Requirements

### 10.1 Trust And Institution Management

The system shall allow SuperAdmin to:

- Create and manage Trusts.
- Create one or more institutions under a Trust.
- Assign institution type.
- Configure board, university, or affiliation body.
- Activate, deactivate, or freeze institutions based on subscription status.

The system shall allow Trust Admin to:

- View all institutions under their Trust.
- Compare admissions, fees, staff, attendance, and results across institutions.
- Manage users across institutions, based on permission.

### 10.2 Academic Setup

The system shall support academic setup based on institution type.

For schools:

- Board.
- Academic year.
- Class.
- Section.
- Subject.
- Teacher assignment.

For colleges:

- University.
- Program.
- Course.
- Stream.
- Department.
- Academic institute.
- Year or semester.
- Credits.
- Elective subjects.
- Batch.

For coaching/training:

- Course.
- Package.
- Batch.
- Module.
- Session schedule.
- Subject/faculty mapping.

### 10.3 Admissions

The system shall support:

- Inquiry capture.
- Follow-up tracking.
- Application form.
- Document upload and verification.
- Admission approval.
- Student enrollment.
- Fee plan allocation.
- Student and parent login creation.

Example:

A parent visits ABC High School. The receptionist records an inquiry for Class 8. After follow-up and document verification, the admin approves the admission, assigns Class 8-A, and the system creates fee dues.

### 10.4 Student Information System

The system shall maintain:

- Student personal details.
- Parent/guardian details.
- Address and contact information.
- Academic enrollment.
- Documents.
- Attendance history.
- Fee ledger.
- Exam results.
- Transfer/leaving records.

### 10.5 Staff And HR

The system shall manage:

- Staff profiles.
- Departments and designations.
- Teacher/faculty allocation.
- Staff attendance.
- Leave requests and approvals.
- Payroll inputs.
- Role and permission assignment.

### 10.6 Attendance

The system shall support:

- Student attendance.
- Staff attendance.
- Subject-wise attendance.
- Batch-wise attendance.
- Hostel attendance.
- Manual attendance.
- Optional biometric attendance integration.
- Attendance correction with audit trail.

Example:

A teacher marks Class 10-A attendance. A college faculty marks attendance for Semester 5 Database Management Systems. A coaching faculty marks attendance for the NEET Morning Batch.

### 10.7 Timetable

The system shall support timetable creation for:

- School class/section.
- College course/semester/batch.
- Coaching course/batch.
- Teacher availability.
- Rooms or classrooms.

The system should prevent obvious conflicts, such as assigning the same teacher to two sessions at the same time.

### 10.8 Syllabus And Teaching Progress

The system shall support:

- Board-wise syllabus.
- University-wise syllabus.
- Course-wise syllabus.
- Semester/year-wise syllabus.
- Subject-wise syllabus.
- Chapter/topic/unit/learning outcome level syllabus.
- Syllabus versioning.
- Effective academic year.
- Global syllabus templates.
- Institution-level syllabus copy/import.
- Institution-level customization without changing global template.
- Topic completion tracking by teachers.

Example:

CBSE Class 10 Mathematics 2026 syllabus is stored as a global template. ABC High School imports it for Academic Year 2026-27. A teacher tracks completed chapters during the year.

### 10.9 Fee Management And Finance

The system shall support:

- Fee heads.
- Fee groups.
- School class-based fees.
- College course/semester/year fees.
- Coaching package/batch fees.
- Installments.
- Late fines.
- Concessions.
- Refunds.
- Receipts.
- Dues reports.
- Payment history.
- Basic accounting vouchers and ledgers.

Example:

A school may charge annual tuition plus term fees. A college may charge semester fees. A coaching center may charge a JEE package fee in three installments.

### 10.10 Exams And Results

The system shall support:

- Exam groups.
- Exam schedules.
- Offline marks entry.
- Online exams.
- Question bank.
- Admit cards.
- Results.
- Tabulation sheets.
- Grade rules.
- Subject-wise and class/batch-wise reports.

For colleges, exams must support semester/year and credit-based results where required.

### 10.11 Communication

The system shall support:

- Notices.
- Email.
- SMS.
- WhatsApp integration where approved.
- Parent communication.
- Staff communication.
- Fee reminders.
- Admission follow-ups.
- Attendance alerts.

### 10.12 Front Office

The system shall support:

- Admission inquiries.
- Visitor logs.
- Phone call logs.
- Postal records.
- Follow-up reminders.

### 10.13 Library

The system shall support:

- Book catalog.
- Member management.
- Issue and return.
- Reservations.
- Fines.
- Reports.

### 10.14 Transport

The system shall support:

- Routes.
- Stops.
- Vehicles.
- Drivers.
- Student/staff transport allocation.
- Maintenance records.
- Fuel logs.

### 10.15 Hostel

The system shall support:

- Hostel buildings.
- Room types.
- Room allocation.
- Hostel attendance.
- Complaints.
- Swap/change requests.

### 10.16 Inventory And Assets

The system shall support:

- Suppliers.
- Items.
- Stock.
- Issues to staff/students.
- Asset tracking.
- Purchase and usage history.

### 10.17 Subscription And Billing

The system shall support institution-level SaaS subscription.

Required subscription capabilities:

- Trial.
- Activation.
- Renewal.
- Freeze and unfreeze.
- Grace period.
- Fixed yearly pricing.
- Per-student yearly pricing.
- Student count snapshot.
- Overage detection.
- Payment history.
- Renewal history.
- Event audit log.
- Access restriction for frozen/expired institutions.
- Billing access during freeze so payment can be completed.

Example:

ABC Trust has five institutions. Each institution may have a separate subscription. If ABC Coaching Center is frozen due to non-payment, ABC High School should continue working if its subscription is active.

### 10.18 Mobile App

The system shall provide a hybrid mobile app for parents, students, and teachers.

The mobile app shall support:

- Login and secure session handling.
- Role-based home screen.
- Parent/student dashboard.
- Teacher dashboard.
- Student selection for parents with multiple children.
- Attendance viewing.
- Teacher attendance marking where permitted.
- Homework viewing and teacher homework creation where permitted.
- Exam schedule and results viewing.
- Teacher marks entry where permitted.
- Syllabus viewing.
- Notice board.
- Fee status viewing.
- Transport allocation viewing.
- Hostel information viewing.
- Profile and settings.
- Document/PDF viewing where applicable.
- Basic offline cache for read-heavy data, where safe.
- Clear handling of no internet, loading, empty, and error states.

The mobile app shall not initially be the primary tool for:

- Trust creation.
- Institution setup.
- Full fee configuration.
- Full subscription administration.
- Deep reports.
- Role and permission management.

Those remain web-first workflows.

### 10.19 Mobile Privacy And Device Permissions

The mobile app may request device permissions only when needed for a user-facing feature.

Examples:

- Storage permission for downloading documents.
- Camera/photos permission for profile images or assignment submission.
- Notification permission for alerts.
- Location permission only for approved transport-related features.

The app must explain why a permission is needed and must continue to work in a limited way if a non-essential permission is denied.

## 11. Key Workflows

### 11.1 Trust Onboarding Workflow

1. SuperAdmin creates a Trust.
2. SuperAdmin creates institutions under the Trust.
3. SuperAdmin assigns subscription plan or trial.
4. Trust Admin user is created.
5. Trust Admin completes institution setup.

### 11.2 Institution Setup Workflow

1. Institution Admin selects institution type.
2. System shows setup options based on institution type.
3. Admin configures board/university/affiliation body.
4. Admin configures academic year.
5. Admin creates academic structure.
6. Admin maps subjects and staff.
7. Institution becomes ready for admissions and operations.

### 11.3 Student Admission Workflow

1. Inquiry is recorded.
2. Follow-up is completed.
3. Application is submitted.
4. Documents are verified.
5. Admission is approved.
6. Student is enrolled into correct academic context.
7. Fee plan is allocated.
8. Student/parent login is created.

### 11.4 Fee Collection Workflow

1. Fee plan is configured.
2. System generates dues.
3. Student/parent/accountant pays or records payment.
4. Receipt is generated.
5. Ledger is updated.
6. Dues report reflects updated balance.

### 11.5 Syllabus Workflow

1. SuperAdmin or authorized academic admin creates global syllabus template.
2. Template is versioned with source and effective year.
3. Institution imports syllabus for its academic year.
4. Institution customizes if required.
5. Teacher tracks progress by topic.
6. Principal/academic coordinator reviews completion.

### 11.6 Subscription Freeze Workflow

1. Institution subscription expires.
2. Grace period starts, if configured.
3. Users see warning.
4. If unpaid after grace period, operational access is frozen.
5. Billing page remains accessible.
6. Payment is recorded.
7. Institution access is restored.

### 11.7 Mobile Login And Daily Use Workflow

1. Parent, student, or teacher opens the Kaksha+ mobile app.
2. User logs in with approved credentials.
3. App loads the user's role and institution context.
4. Parent sees linked children and selects a child if more than one exists.
5. Student sees personal academic information.
6. Teacher sees assigned classes, batches, attendance, homework, and exams.
7. App shows only the features allowed by backend permissions.
8. If internet is unavailable, app may show safe cached information where available.

Example:

A parent has two children in the same Trust: one in school and one in coaching. The mobile app should allow the parent to select the child and then view only that child's attendance, fees, homework, notices, and results.

## 12. Reports And Dashboards

### 12.1 SuperAdmin Dashboard

Must show:

- Total Trusts.
- Total institutions.
- Active/frozen/expired subscriptions.
- Revenue summary.
- Institution usage.
- Pending renewals.

### 12.2 Trust Admin Dashboard

Must show:

- All institutions under Trust.
- Admissions summary.
- Fee collection summary.
- Pending dues.
- Staff summary.
- Attendance summary.
- Academic performance.
- Subscription status per institution.

### 12.3 Institution Dashboard

Must show:

- Today's attendance.
- Fee collection.
- New inquiries/admissions.
- Staff attendance.
- Exam/result status.
- Syllabus completion.
- Alerts and notices.

### 12.4 Operational Reports

Required reports:

- Student list.
- Admission report.
- Fee dues report.
- Fee collection report.
- Attendance report.
- Exam result report.
- Staff report.
- Payroll report.
- Library report.
- Transport report.
- Hostel report.
- Subscription report.
- Audit report.

## 13. Non-Functional Requirements

### 13.1 Security

The system must:

- Use secure login and token handling.
- Enforce strong passwords.
- Protect refresh tokens.
- Prevent users from accessing another Trust or institution.
- Prevent unauthorized direct API calls.
- Protect sensitive student, parent, staff, and financial data.
- Avoid exposing secrets in source code or configuration files.
- Log security-relevant events.

### 13.2 Performance

The system should support:

- One Trust with 50+ institutions.
- One institution with 5,000 to 50,000 students.
- Large attendance and fee records.
- Fast dashboards.
- Paginated lists.
- Search and filters.
- Efficient reports.

### 13.3 Auditability

The system must track:

- Who created a record.
- Who updated a record.
- When changes happened.
- Subscription events.
- Fee transactions.
- Attendance corrections.
- Permission changes.
- Important student/staff changes.

### 13.4 Reliability

The system should:

- Avoid data loss.
- Use safe database scripts.
- Support backup and restore.
- Handle failures gracefully.
- Show clear error messages to users.

### 13.5 Usability

The product should be easy for non-technical education staff.

The UI should:

- Use simple language.
- Avoid confusing school-only labels for colleges/coaching institutes.
- Show only relevant options based on institution type.
- Provide clear empty states and validation messages.
- Help users complete common tasks quickly.

### 13.6 Mobile App Non-Functional Requirements

The mobile app must:

- Work on Android and iOS from a shared Flutter codebase.
- Support separate development and production environments.
- Use secure API communication.
- Store sensitive tokens securely.
- Avoid exposing secrets in app code or public assets.
- Handle slow networks gracefully.
- Show clear offline and retry messages.
- Cache only data that is safe for the user's role.
- Clear cached data on logout where required.
- Respect backend tenant, institution, and role permissions.
- Support production signing and release process for app stores.

## 14. Data And Isolation Rules

1. Every Trust's data must be isolated from other Trusts.
2. Every institution's operational data must be isolated unless user role allows Trust-level view.
3. Global masters such as boards and universities may be shared across Trusts.
4. Institution-specific customizations must not change global templates.
5. Deleted records should usually be soft-deleted to preserve history.
6. Fee and subscription history must never be physically deleted during normal operations.
7. Academic year context must be stored for year-dependent records.
8. Reports must respect user role and institution access.
9. Mobile cached data must also respect the same user, Trust, institution, and child/student access rules.
10. If a mobile user logs out, switches account, or loses access to an institution, restricted cached data must not remain accessible.

## 15. Manual Database Change Requirement

The product uses PostgreSQL with manual SQL scripts for schema changes.

Business requirement:

- Every database change must be traceable.
- Every script must be repeat-safe where possible.
- Schema changes must not happen silently during application startup.
- Seed data must be separated into production master data, demo data, and test data.
- Database script history must be recorded.

This is important because Trust customers will depend on historical academic and financial records.

## 16. Examples Of Correct Behavior

### Example 1: Trust Admin Access

ABC Trust Admin logs in and sees:

- ABC High School.
- ABC Engineering College.
- ABC Coaching Center.

The admin can switch between institutions and view Trust-level reports.

### Example 2: Institution Admin Access

ABC High School Admin logs in and sees only ABC High School. They cannot view ABC Engineering College data.

### Example 3: School Setup

Admin selects:

- Institution Type: School.
- Board: CBSE.
- Classes: 1 to 10.
- Sections: A, B.

The system shows school-style screens.

### Example 4: College Setup

Admin selects:

- Institution Type: Engineering College.
- University: Mumbai University.
- Program: B.E.
- Course: Computer Engineering.
- Semester: 1 to 8.

The system shows college-style screens and does not force class/section.

### Example 5: Coaching Setup

Admin selects:

- Institution Type: Coaching Institute.
- Course: NEET.
- Batch: Morning Batch.
- Package Fee: One-year package.

The system shows batch/package screens and does not force board/class.

### Example 6: Mobile Parent Access

Parent logs into the Kaksha+ mobile app and sees two children. After selecting one child, the parent can view only that child's attendance, homework, fee status, notices, transport, hostel, and results.

### Example 7: Mobile Teacher Access

A teacher logs into the mobile app and sees assigned classes or batches. The teacher can mark attendance and add homework only for assigned academic groups.

## 17. Business Risks

| Risk | Impact | Required Control |
|---|---|---|
| School-only model used for all institutions | Colleges/coaching institutes cannot use product properly | Institution-type-aware academic model |
| Weak tenant isolation | Data leakage between Trusts | Strict server-side access checks |
| Weak institution isolation | One institute sees another institute's data | Role and institution assignment checks |
| Incomplete subscription lock | Non-paying institutions keep using product or paying users get blocked from billing | Clear subscription rules and tests |
| Poor seed data | Setup looks complete but fails in real customer onboarding | Production-grade master data strategy |
| Hardcoded roles/secrets | Security and maintenance issues | Config and RBAC governance |
| Untracked database changes | Production data risk | Manual SQL process and script tracking |
| Missing audit logs | Disputes cannot be resolved | Audit trail for sensitive operations |
| Mobile app bypasses web permissions | Unauthorized access from phone | Backend permission enforcement for every API |
| Mobile cached data leaks after logout | Sensitive student data exposure | Secure storage, cache clearing, and user-scoped cache |
| Mobile app uses school-only labels | College/coaching users get confused | Institution-type-aware mobile language |

## 18. Success Criteria

The product can be considered ready for a controlled paid pilot only when:

- One Trust can manage multiple institutions safely.
- School, college, and coaching setups work without workarounds.
- Trust Admin and Institution Admin access rules are verified.
- Subscription trial, activation, renewal, freeze, and payment flows work.
- Fees, admissions, attendance, exams, and syllabus work for at least one school, one college, and one coaching institute.
- Global masters and seed data are realistic enough for Indian usage.
- Database scripts can build and update a database reliably.
- Security risks such as hardcoded secrets and tenant leakage are resolved.
- Critical workflows are covered by automated tests.
- The mobile app supports at least the approved parent, student, and teacher daily workflows for the pilot institution type.
- Mobile login, role routing, secure storage, offline/cache behavior, and API permission enforcement are tested.
- Dev and production mobile build processes are documented and working.

## 19. Future Enhancements

Future versions may include:

- Alumni management.
- Advanced LMS.
- AI-assisted reports.
- Advanced analytics.
- Government compliance integrations.
- Payment gateway reconciliation.
- WhatsApp chatbot.
- Advanced biometric and RFID integrations.
- Multi-language UI.
- Advanced mobile payments.
- Push notification automation.
- Mobile biometric attendance for staff where approved.

## 20. Final Business Direction

The product should no longer be treated as only a School ERP.

The correct product direction is:

**Enterprise Education ERP SaaS for Indian Trusts and multi-institution education groups.**

All future implementation, UI labels, database design, role design, testing, documentation, web screens, and mobile screens should follow this business direction.
