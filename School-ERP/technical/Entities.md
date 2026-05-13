# Entities

This document lists all entities and their properties.

## Identity Module

### ApplicationRole
| Property | Type |
| :--- | :--- |
| CreatedAt | DateTime |
| CreatedBy | string? |
| UpdatedAt | DateTime? |
| UpdatedBy | string? |
| IsActive | bool |
| Description | string? |
| IsSystemRole | bool |
| Weight | int |
| ClientId | Guid? |
| Client | Client? |
| RoleModules | ICollection<RoleModule> |

### ApplicationUser
| Property | Type |
| :--- | :--- |
| CreatedAt | DateTime |
| CreatedBy | string? |
| UpdatedAt | DateTime? |
| UpdatedBy | string? |
| IsActive | bool |
| EmployeeCode | string? |
| BranchName | string? |
| IsDeleted | bool |
| MustChangePassword | bool |
| LastGeneratedPassword | string? |
| PasswordResetToken | string? |
| PasswordResetTokenExpires | DateTime? |
| EmailOTP | string? |
| EmailConfirmed | bool |
| EmailOTPGeneratedAt | DateTime? |
| EmailOTPExpiry | DateTime? |
| MobileOTP | string? |
| MobileOTPExpiry | DateTime? |
| MobileConfirmed | bool |
| FullName | string |
| ManagerId | Guid? |
| Manager | ApplicationUser? |
| Subordinates | ICollection<ApplicationUser>? |
| ClientId | Guid? |
| Client | Client? |
| BranchId | Guid? |
| Branch | Branch? |
| FcmToken | string? |
| StudentId | Guid? |

### ApplicationUserRefreshToken
| Property | Type |
| :--- | :--- |
| Id | Guid |
| UserId | Guid |
| User | ApplicationUser |
| Token | string |
| ExpiresAt | DateTime |
| Revoked | bool |
| CreatedAt | DateTime |
| CreatedBy | string? |
| UpdatedAt | DateTime? |
| UpdatedBy | string? |
| IsActive | bool |
| DeviceInfo | string? |
| ReplacedByToken | string? |
| LoginSource | string? |

### Module
| Property | Type |
| :--- | :--- |
| Key | string |
| Name | string |
| Path | string? |
| Icon | string? |
| DisplayOrder | int |
| IsSuperAdminOnly | bool |
| ParentModuleId | Guid? |
| ParentModule | Module? |
| SubModules | ICollection<Module> |
| RoleModules | ICollection<RoleModule> |

### Permission
| Property | Type |
| :--- | :--- |
| Key | string |
| Name | string |
| Description | string? |
| ModuleId | Guid? |
| Module | Module? |
| IsSuperAdminOnly | bool |

### RoleModule
| Property | Type |
| :--- | :--- |
| RoleId | Guid |
| Role | ApplicationRole |
| ModuleId | Guid |
| Module | Module |

### RolePermission
| Property | Type |
| :--- | :--- |
| RoleId | Guid |
| Role | ApplicationRole |
| PermissionId | Guid |
| Permission | Permission |

## Utilities Module

### Counter
| Property | Type |
| :--- | :--- |
| Name | string |
| Code | string? |
| Description | string? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client |

### Country
| Property | Type |
| :--- | :--- |
| Name | string |
| Code | string |
| States | ICollection<State> |

### District
| Property | Type |
| :--- | :--- |
| Name | string |
| StateId | Guid |
| State | State |

### Holiday
| Property | Type |
| :--- | :--- |
| Title | string |
| FromDate | DateTime |
| ToDate | DateTime |
| Description | string? |
| ClientId | Guid |
| Client | Client? |

### Homework
| Property | Type |
| :--- | :--- |
| Title | string |
| ClassId | Guid? |
| Class | Class? |
| SectionId | Guid? |
| Section | Section? |
| CourseId | Guid? |
| Course | AcademicStructure? |
| AcademicBranchId | Guid? |
| AcademicBranch | AcademicStructure? |
| BranchId | Guid? |
| Branch | Branch? |
| SemesterId | Guid? |
| Semester | AcademicStructure? |
| BatchId | Guid? |
| Batch | AcademicStructure? |
| SubjectId | Guid |
| Subject | Subject? |
| TeacherId | Guid |
| Teacher | Teacher? |
| SyllabusTopicId | Guid? |
| SyllabusTopic | SyllabusTopic? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| HomeworkDate | DateTime |
| SubmissionDate | DateTime |
| Description | string? |
| Attachment | string? |
| ClientId | Guid |
| Client | Client? |

### HomeworkSubmission
| Property | Type |
| :--- | :--- |
| HomeworkId | Guid |
| Homework | Homework? |
| StudentId | Guid |
| Student | Student? |
| Status | HomeworkSubmissionStatus |
| SubmissionDate | DateTime? |
| Remarks | string? |
| SubmissionFile | string? |
| ClientId | Guid |
| Client | Client? |

### NoticeBoard
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| Title | string |
| Content | string |
| PostingDate | DateTime |
| ExpiryDate | DateTime? |
| TargetAudience | NoticeAudience |
| Type | NoticeType |
| IsCritical | bool |
| ClientId | Guid |
| Client | Client? |

### RecentActivity
| Property | Type |
| :--- | :--- |
| UserId | Guid |
| User | ApplicationUser |
| Title | string |
| Subtitle | string? |
| EntityType | EntityType |
| EntityId | Guid? |
| ActivityTime | DateTime |

### Sport
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| SportName | string |
| CoachName | string? |
| StartedYear | int |
| ClientId | Guid |
| Client | Client? |

### State
| Property | Type |
| :--- | :--- |
| Name | string |
| CountryId | Guid |
| Country | Country |
| Districts | ICollection<District> |

### SystemSetting
| Property | Type |
| :--- | :--- |
| Key | string |
| Value | string |
| ClientId | Guid |
| Client | Client? |

## Communication Module

### CommunicationLog
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| Recipient | string |
| RecipientId | Guid? |
| Subject | string? |
| MessageBody | string |
| Channel | CommunicationChannel |
| Status | CommunicationStatus |
| ErrorMessage | string? |
| SentAt | DateTime |
| ClientId | Guid |
| Client | Client? |

### FeeNotification
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| StudentId | Guid |
| Student | Student? |
| ParentId | Guid? |
| Parent | Parent? |
| Type | CommunicationChannel |
| Category | NotificationCategory |
| Message | string |
| Status | CommunicationStatus |
| ExternalMessageId | string? |
| WhatsAppConfigurationId | Guid? |
| WhatsAppConfiguration | WhatsAppConfiguration? |
| SentAt | DateTime? |
| ClientId | Guid |
| Client | Client? |
| IsRead | bool |
| ReadAt | DateTime? |

### FeeReminderConfig
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| DaysBeforeDue | int |
| DaysAfterDue | int |
| IsActive | bool |
| ClientId | Guid |
| Client | Client? |

### NotificationTemplate
| Property | Type |
| :--- | :--- |
| Name | string |
| Subject | string? |
| Body | string |
| Channel | CommunicationChannel |
| Placeholders | string? |
| ClientId | Guid |
| Client | Client? |

### WhatsAppConfiguration
| Property | Type |
| :--- | :--- |
| ClientId | Guid |
| Client | Client? |
| SchoolId | Guid? |
| AccountName | string |
| PhoneNumberId | string |
| BusinessAccountId | string |
| AccessToken | string |
| WebhookVerifyToken | string? |
| IsDefault | bool |
| IsActive | bool |

## Finances Module

### LedgerAccount
| Property | Type |
| :--- | :--- |
| AccountName | string |
| AccountCode | string? |
| GroupName | string |
| OpeningBalance | decimal |
| IsSystemAccount | bool |
| ClientId | Guid |
| Client | Client? |

### Tax
| Property | Type |
| :--- | :--- |
| Name | string |
| Code | string? |
| Rate | decimal |
| IsPercentage | bool |
| ClientId | Guid |
| Client | Client |

### Voucher
| Property | Type |
| :--- | :--- |
| VoucherNumber | string |
| VoucherDate | DateTime |
| Narration | string? |
| VoucherType | VoucherType |
| ClientId | Guid |
| Client | Client? |
| Entries | ICollection<VoucherEntry> |

### VoucherEntry
| Property | Type |
| :--- | :--- |
| VoucherId | Guid |
| Voucher | Voucher? |
| LedgerAccountId | Guid |
| LedgerAccount | LedgerAccount? |
| DebitAmount | decimal |
| CreditAmount | decimal |
| ClientId | Guid |
| Client | Client? |

## FrontOffice Module

### PhoneCallLog
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| Name | string |
| Phone | string |
| CallType | InteractionType |
| CallDate | DateTime |
| CallDurationSeconds | int? |
| Description | string? |
| FollowUpNeeded | bool |
| FollowUpDate | DateTime? |
| Remarks | string? |
| ClientId | Guid |
| Client | Client |

### PostalLog
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| Type | PostalType |
| FromTitle | string |
| ToTitle | string |
| ReferenceNo | string? |
| PostalDate | DateTime |
| Address | string? |
| Attachment | string? |
| Remarks | string? |
| ClientId | Guid |
| Client | Client |

### Visitor
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| VisitorName | string |
| Phone | string |
| Purpose | VisitorPurpose |
| Description | string? |
| IdentificationId | string? |
| ToMeetStaffId | Guid? |
| ToMeetStaff | Staff? |
| InTime | DateTime |
| OutTime | DateTime? |
| Remarks | string? |
| ClientId | Guid |
| Client | Client |

## Inventory Module

### InventoryItem
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| Name | string |
| ItemCategoryId | Guid |
| Category | ItemCategory? |
| ItemCode | string? |
| Description | string? |
| UnitPrice | decimal |
| TotalStock | int |
| MinimumRequiredQuantity | int |
| ClientId | Guid |
| Client | Client |
| IsDeleted | bool |

### ItemCategory
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| Name | string |
| Description | string? |
| ClientId | Guid |
| Client | Client |
| IsDeleted | bool |

### ItemIssue
| Property | Type |
| :--- | :--- |
| InventoryItemId | Guid |
| Item | InventoryItem? |
| IssuedToStaffId | Guid? |
| Staff | Staff? |
| IssuedToDepartmentId | Guid? |
| Department | Department? |
| IssuedToDepartment | string? |
| Quantity | int |
| IssueDate | DateTime |
| ReturnDate | DateTime? |
| Status | IssueStatus |
| Remarks | string? |
| ClientId | Guid |
| Client | Client |

### ItemStockLog
| Property | Type |
| :--- | :--- |
| InventoryItemId | Guid |
| Item | InventoryItem? |
| SupplierId | Guid? |
| Supplier | Supplier? |
| Quantity | int |
| Type | StockLogType |
| PricePerUnit | decimal |
| TransactionDate | DateTime |
| InvoiceNumber | string? |
| Remarks | string? |
| ClientId | Guid |
| Client | Client |

### Supplier
| Property | Type |
| :--- | :--- |
| Name | string |
| ContactPerson | string? |
| Mobile | string? |
| Email | string? |
| GstNumber | string? |
| Addresses | ICollection<Address> |
| ClientId | Guid |
| Client | Client |

## Students Module

### Category
| Property | Type |
| :--- | :--- |
| Name | string |
| ShortName | string? |
| Description | string? |
| HsnCode | string? |
| Slug | string? |
| ParentCategoryId | Guid? |
| ParentCategory | Category? |
| ClientId | Guid |
| Client | Client |

### Parent
| Property | Type |
| :--- | :--- |
| FullName | string |
| ProfilePhoto | string? |
| Email | string? |
| Phone | string? |
| Relationship | ParentRelationship |
| UserId | Guid? |
| User | ApplicationUser? |
| LastLogin | DateTime? |
| Address | string? |
| Occupation | string? |
| Students | ICollection<Student> |
| ClientId | Guid |
| Client | Client? |

### Student
| Property | Type |
| :--- | :--- |
| FirstName | string |
| LastName | string |
| DOB | DateTime |
| Gender | Gender |
| AdmissionNo | string? |
| RollNo | string? |
| FatherName | string? |
| MotherName | string? |
| ParentPhone | string? |
| MotherMobileNo | string? |
| ParentEmail | string? |
| CategoryId | Guid? |
| Category | Category? |
| ClassId | Guid? |
| Class | Class? |
| SectionId | Guid? |
| Section | Section? |
| CourseId | Guid? |
| Course | AcademicStructure? |
| AcademicBranchId | Guid? |
| AcademicBranch | AcademicStructure? |
| BranchId | Guid? |
| Branch | Branch? |
| SemesterId | Guid? |
| Semester | AcademicStructure? |
| BatchId | Guid? |
| Batch | AcademicStructure? |
| ParentId | Guid? |
| Parent | Parent? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| ProfilePhoto | string? |
| BloodGroup | string? |
| AdmissionDate | DateTime |
| UserId | Guid? |
| User | ApplicationUser? |
| CurrentAddressId | Guid? |
| CurrentAddress | Address? |
| PermanentAddressId | Guid? |
| PermanentAddress | Address? |
| StateId | Guid? |
| State | State? |
| CountryId | Guid? |
| Country | Country? |
| ClientId | Guid |
| Client | Client? |
| StudentDocuments | ICollection<StudentDocument> |
| StudentAcademics | ICollection<StudentAcademic> |

### StudentAcademic
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| AcademicStructureId | Guid |
| AcademicStructure | AcademicStructure? |
| ClientId | Guid |
| Client | Client? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |

### StudentAttendance
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| ClassId | Guid |
| Class | Class? |
| SectionId | Guid |
| Section | Section? |
| CourseId | Guid? |
| Course | AcademicStructure? |
| AcademicBranchId | Guid? |
| AcademicBranch | AcademicStructure? |
| BranchId | Guid? |
| Branch | Branch? |
| SemesterId | Guid? |
| Semester | AcademicStructure? |
| BatchId | Guid? |
| Batch | AcademicStructure? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| AttendanceDate | DateTime |
| Status | StudentAttendanceStatus |
| AttendanceType | string? |
| Remarks | string? |
| Source | AttendanceSource |
| BiometricPunchInTime | DateTime? |
| BiometricPunchOutTime | DateTime? |
| BiometricDeviceId | Guid? |
| ClientId | Guid |
| Client | Client? |

### StudentDocument
| Property | Type |
| :--- | :--- |
| DocumentName | string |
| FileUrl | string |
| StudentId | Guid |
| Student | Student? |
| ClientId | Guid |
| Client | Client? |

## Academics Module

### AcademicStructure
| Property | Type |
| :--- | :--- |
| Name | string |
| Type | AcademicType |
| ParentId | Guid? |
| Parent | AcademicStructure? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### AcademicYear
| Property | Type |
| :--- | :--- |
| Name | string |
| StartDate | DateTime |
| EndDate | DateTime |
| IsCurrent | bool |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### Branch
| Property | Type |
| :--- | :--- |
| Name | string |
| Code | string? |
| Address | string? |
| Phone | string? |
| Email | string? |
| ManagerId | Guid? |
| Manager | ApplicationUser? |
| InstitutionType | SchoolErp.Core.Enums.InstitutionType |
| ClientId | Guid |
| Client | Client |

### Class
| Property | Type |
| :--- | :--- |
| Name | string |
| Code | string? |
| DepartmentId | Guid? |
| Department | Department? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |
| AcademicStructureId | Guid? |
| AcademicStructure | AcademicStructure? |

### ClassRoom
| Property | Type |
| :--- | :--- |
| RoomNo | string |
| Capacity | int |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### ClassRoutine
| Property | Type |
| :--- | :--- |
| ClassId | Guid? |
| Class | Class? |
| SectionId | Guid |
| Section | Section? |
| SubjectId | Guid |
| Subject | Subject? |
| TeacherId | Guid |
| Teacher | Teacher? |
| ClassRoomId | Guid? |
| ClassRoom | ClassRoom? |
| Day | DayOfWeek |
| StartTime | TimeSpan |
| EndTime | TimeSpan |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### Grade
| Property | Type |
| :--- | :--- |
| Name | string |
| PercentageFrom | decimal |
| PercentageTo | decimal |
| Point | decimal |
| ClientId | Guid |
| Client | Client? |

### Section
| Property | Type |
| :--- | :--- |
| Name | string |
| Code | string? |
| ClassId | Guid |
| Class | Class? |
| DepartmentId | Guid? |
| Department | Department? |
| ClassRoomId | Guid? |
| ClassRoom | ClassRoom? |
| TeacherId | Guid? |
| Teacher | Teacher? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |
| AcademicStructureId | Guid? |
| AcademicStructure | AcademicStructure? |

### Subject
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| Name | string |
| Code | string? |
| Type | SubjectType |
| ClientId | Guid |
| Client | Client? |

### Board
| Property | Type |
| :--- | :--- |
| Name | string |
| Code | string |
| Description | string? |
| IsActive | bool |

### Syllabus
| Property | Type |
| :--- | :--- |
| BoardId | Guid |
| Board | Board? |
| ClassId | Guid? |
| Class | Class? |
| AcademicStructureId | Guid? |
| AcademicStructure | AcademicStructure? |
| CourseId | Guid? |
| SemesterId | Guid? |
| SubjectId | Guid |
| Subject | Subject? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| Title | string |
| Description | string? |
| IsPublished | bool |
| Topics | ICollection<SyllabusTopic> |
| SchoolMappings | ICollection<SchoolSyllabusMapping> |

### SyllabusTopic
| Property | Type |
| :--- | :--- |
| SyllabusId | Guid |
| Syllabus | Syllabus? |
| ParentTopicId | Guid? |
| ParentTopic | SyllabusTopic? |
| Title | string |
| Description | string? |
| LearningObjectives | string? |
| ResourceUrl | string? |
| EstimatedPeriods | int |
| SequenceNumber | int |
| IsActive | bool |
| SubTopics | ICollection<SyllabusTopic> |

### SchoolSyllabusMapping
| Property | Type |
| :--- | :--- |
| SchoolId | Guid |
| BranchId | Guid? |
| Branch | Branch? |
| SyllabusId | Guid |
| Syllabus | Syllabus? |
| AssignedAt | DateTime |
| IsEditableBySchool | bool |
| ClientId | Guid |
| Client | Client? |

### TimeTable
| Property | Type |
| :--- | :--- |
| ClassId | Guid? |
| Class | Class? |
| SectionId | Guid? |
| Section | Section? |
| CourseId | Guid? |
| Course | AcademicStructure? |
| AcademicBranchId | Guid? |
| AcademicBranch | AcademicStructure? |
| BranchId | Guid? |
| Branch | Branch? |
| SemesterId | Guid? |
| Semester | AcademicStructure? |
| BatchId | Guid? |
| Batch | AcademicStructure? |
| SessionType | SessionType |
| SubjectId | Guid? |
| Subject | Subject? |
| TeacherId | Guid? |
| Teacher | Teacher? |
| Note | string? |
| Day | DayOfWeek |
| StartTime | TimeSpan |
| EndTime | TimeSpan |
| Date | DateTime? |
| ClientId | Guid |
| Client | Client? |

### Topic
| Property | Type |
| :--- | :--- |
| ClassId | Guid? |
| Class | Class? |
| SectionId | Guid? |
| Section | Section? |
| SubjectId | Guid? |
| Subject | Subject? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| CourseId | Guid? |
| Course | AcademicStructure? |
| BranchId | Guid? |
| Branch | Branch? |
| AcademicBranchId | Guid? |
| AcademicBranch | AcademicStructure? |
| SemesterId | Guid? |
| Semester | AcademicStructure? |
| BatchId | Guid? |
| Batch | AcademicStructure? |
| Title | string |
| Description | string? |
| EstimatedHours | int |
| IsActive | bool |
| ClientId | Guid |
| Client | Client? |

## Libraries Module

### Book
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| ISBN | string |
| Title | string |
| Author | string |
| Publisher | string? |
| RackNumber | string? |
| Quantity | int |
| AvailableQuantity | int |
| Price | decimal |
| ClientId | Guid |
| Client | Client? |

### BookIssue
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| BookId | Guid |
| Book | Book? |
| MemberId | Guid |
| Member | LibraryMember? |
| IssueDate | DateTime |
| DueDate | DateTime |
| ReturnDate | DateTime? |
| FineAmount | decimal |
| IsFinePaid | bool |
| Status | BookIssueStatus |
| ClientId | Guid |
| Client | Client? |
| Remarks | string? |

### BookRenewal
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| BookIssueId | Guid |
| RenewalDate | DateTime |
| PreviousDueDate | DateTime |
| NewDueDate | DateTime |
| Remarks | string? |
| ClientId | Guid |

### BookReservation
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| BookId | Guid |
| Book | Book? |
| MemberId | Guid |
| Member | LibraryMember? |
| ReservationDate | DateTime |
| Status | ReservationStatus |
| ClientId | Guid |
| Client | Client? |
| Note | string? |

### LibraryMember
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| MemberName | string |
| CardNumber | string |
| Email | string? |
| Mobile | string? |
| DateOfJoin | DateTime |
| StudentId | Guid? |
| Student | Student? |
| StaffId | Guid? |
| Staff | Staff? |
| ClientId | Guid |
| Client | Client? |

### LibrarySetting
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| FinePerDay | decimal |
| MaxBooksPerStudent | int |
| MaxBooksPerStaff | int |
| MaxDaysForIssue | int |
| ClientId | Guid |
| Client | Client? |

## HR Module

### Department
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| Name | string |
| ClientId | Guid |
| Client | Client? |

### Designation
| Property | Type |
| :--- | :--- |
| Name | string |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### LeaveType
| Property | Type |
| :--- | :--- |
| Name | string |
| ClientId | Guid |
| Client | Client? |

### Payroll
| Property | Type |
| :--- | :--- |
| StaffId | Guid |
| Staff | ApplicationUser? |
| DepartmentId | Guid |
| Department | Department? |
| DesignationId | Guid |
| Designation | Designation? |
| Phone | string? |
| Month | int |
| Year | int |
| SalaryAmount | decimal |
| Status | PaymentStatus |
| PayslipUrl | string? |
| ClientId | Guid |
| Client | Client? |

### RoleWiseCompensation
| Property | Type |
| :--- | :--- |
| RoleId | Guid? |
| UserId | Guid? |
| User | ApplicationUser? |
| DesignationId | Guid? |
| Designation | Designation? |
| BasicSalary | decimal |
| HRA | decimal |
| OtherAllowance | decimal |
| ClientId | Guid |
| Client | Client? |

### Staff
| Property | Type |
| :--- | :--- |
| FirstName | string |
| LastName | string |
| Email | string |
| PhoneNumber | string |
| ProfilePhoto | string? |
| BloodGroup | string? |
| EmployeeId | string? |
| UserId | Guid? |
| User | ApplicationUser? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |
| BasicSalary | decimal? |
| DesignationId | Guid? |
| Designation | Designation? |
| DepartmentId | Guid? |
| Department | Department? |

### StaffAttendance
| Property | Type |
| :--- | :--- |
| StaffId | Guid |
| Staff | Staff? |
| AttendanceDate | DateTime |
| Status | AttendanceStatus |
| AttendanceType | string? |
| Notes | string? |
| Source | AttendanceSource |
| BiometricPunchInTime | DateTime? |
| BiometricPunchOutTime | DateTime? |
| BiometricDeviceId | Guid? |
| ClientId | Guid |
| Client | Client? |

### Teacher
| Property | Type |
| :--- | :--- |
| FullName | string |
| EmployeeId | string? |
| ProfilePhoto | string? |
| JoiningDate | DateTime |
| Email | string? |
| Phone | string? |
| DesignationId | Guid? |
| Designation | Designation? |
| DepartmentId | Guid? |
| Department | Department? |
| BasicSalary | decimal? |
| SubjectSpecialization | string? |
| UserId | Guid? |
| User | ApplicationUser? |
| LastLogin | DateTime? |
| Qualification | string? |
| ExperienceYears | int? |
| Address | string? |
| AssignedSections | ICollection<Section> |
| AssignedAcademicStructures | ICollection<AcademicStructure> |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |
| IsDeleted | bool |

## Exams Module

### AdmitCard
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| ExamId | Guid |
| Exam | Exam? |
| AdmitCardNo | string? |
| ClientId | Guid |
| Client | Client? |

### Exam
| Property | Type |
| :--- | :--- |
| ExamName | string |
| ExamDate | DateTime |
| StartTime | TimeSpan |
| EndTime | TimeSpan |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| Status | string |
| ExamGroupId | Guid? |
| ExamGroup | ExamGroup? |
| ClientId | Guid |
| Client | Client? |

### ExamCategory
| Property | Type |
| :--- | :--- |
| Name | string |
| Description | string? |
| ClientId | Guid |
| Client | Client? |

### ExamGroup
| Property | Type |
| :--- | :--- |
| Name | string |
| ExamCategoryId | Guid |
| ExamCategory | ExamCategory? |
| ExamType | string? |
| Description | string? |
| ClientId | Guid |
| Client | Client? |

### ExamMark
| Property | Type |
| :--- | :--- |
| ExamId | Guid |
| Exam | Exam? |
| StudentId | Guid |
| Student | Student? |
| SubjectId | Guid |
| Subject | Subject? |
| MarksObtained | decimal |
| TotalMarks | decimal |
| GradeId | Guid? |
| Grade | Grade? |
| Remarks | string? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| ClientId | Guid |
| Client | Client? |

### ExamSchedule
| Property | Type |
| :--- | :--- |
| ExamId | Guid |
| Exam | Exam? |
| SubjectId | Guid |
| Subject | Subject? |
| Type | ExamType |
| ExamDate | DateTime |
| StartTime | TimeSpan |
| EndTime | TimeSpan |
| Duration | TimeSpan |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| ClassRoomId | Guid |
| ClassRoom | ClassRoom? |
| ClassId | Guid? |
| Class | Class? |
| SectionId | Guid? |
| Section | Section? |
| CourseId | Guid? |
| Course | AcademicStructure? |
| AcademicBranchId | Guid? |
| AcademicBranch | AcademicStructure? |
| BranchId | Guid? |
| Branch | Branch? |
| BatchId | Guid? |
| Batch | AcademicStructure? |
| SemesterId | Guid? |
| Semester | AcademicStructure? |
| MaxMarks | decimal |
| MinMarks | decimal |
| OnlineExamConfigId | Guid? |
| OnlineExamConfig | OnlineExamConfig? |
| ClientId | Guid |
| Client | Client? |

### ExamSeatingArrangement
| Property | Type |
| :--- | :--- |
| ExamScheduleId | Guid |
| StudentId | Guid |
| ClassRoomId | Guid? |
| SeatNumber | string |
| Remarks | string? |
| BranchId | Guid? |
| AcademicYearId | Guid? |
| ClientId | Guid |

### OnlineExamConfig
| Property | Type |
| :--- | :--- |
| Title | string |
| Instructions | string? |
| DurationMinutes | int |
| TotalMarks | decimal |
| PassingMarks | decimal |
| NegativeMarksPerQuestion | decimal |
| RandomizeQuestions | bool |
| ShuffleOptions | bool |
| QuestionSelectionType | string |
| ClientId | Guid |
| Client | Client? |

### OnlineExamResult
| Property | Type |
| :--- | :--- |
| AttemptId | Guid |
| Attempt | StudentExamAttempt? |
| ObtainedMarks | decimal |
| TotalMarks | decimal |
| Percentage | decimal |
| ResultStatus | string |
| IsPublished | bool |
| PublishedAt | DateTime? |
| ClientId | Guid |
| Client | Client? |

### QuestionBank
| Property | Type |
| :--- | :--- |
| QuestionText | string |
| SubjectId | Guid |
| Subject | Subject? |
| Topic | string |
| Difficulty | QuestionDifficulty |
| QuestionType | QuestionType |
| Options | string? |
| CorrectAnswer | string? |
| Explanation | string? |
| Marks | decimal |
| QuestionImage | string? |
| QuestionHtml | string? |
| OptionsJson | string? |
| ExplanationHtml | string? |
| IsRichText | bool |
| AnswerTolerance | decimal? |
| ClientId | Guid |
| Client | Client? |

### Result
| Property | Type |
| :--- | :--- |
| ExamScheduleId | Guid |
| ExamSchedule | ExamSchedule? |
| StudentId | Guid |
| Student | Student? |
| MarksObtained | decimal |
| Grade | string? |
| Remarks | string? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| ClientId | Guid |
| Client | Client? |

### StudentExamAttempt
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| ExamScheduleId | Guid |
| ExamSchedule | ExamSchedule? |
| StartTime | DateTime |
| SubmissionTime | DateTime? |
| TotalScore | decimal |
| Status | string |
| ClientId | Guid |
| Client | Client? |
| Answers | ICollection<StudentExamAnswer> |

### StudentExamAnswer
| Property | Type |
| :--- | :--- |
| AttemptId | Guid |
| Attempt | StudentExamAttempt? |
| QuestionId | Guid |
| Question | QuestionBank? |
| SelectedOption | string? |
| AnswerText | string? |
| IsCorrect | bool |
| MarksObtained | decimal |
| ClientId | Guid |
| Client | Client? |

### TabulationSheet
| Property | Type |
| :--- | :--- |
| ExamId | Guid |
| Exam | Exam? |
| ClassId | Guid? |
| Class | Class? |
| SectionId | Guid? |
| Section | Section? |
| CourseId | Guid? |
| Course | AcademicStructure? |
| BranchId | Guid? |
| Branch | AcademicStructure? |
| BatchId | Guid? |
| Batch | AcademicStructure? |
| SemesterId | Guid? |
| Semester | AcademicStructure? |
| Remarks | string? |
| ClientId | Guid |
| Client | Client? |

## Transports Module

### TransportAllocation
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| StudentId | Guid |
| Student | Student? |
| RouteId | Guid |
| Route | TransportRoute? |
| StopId | Guid |
| Stop | TransportStop? |
| StartDate | DateTime |
| EndDate | DateTime? |
| ClientId | Guid |
| Client | Client? |

### TransportFuel
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| VehicleId | Guid |
| Vehicle | TransportVehicle? |
| FuelDate | DateTime |
| Quantity | decimal |
| Rate | decimal |
| TotalAmount | decimal |
| OdometerReading | string |
| ReceiptPath | string |
| ClientId | Guid |
| Client | Client? |

### TransportMaintenance
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| VehicleId | Guid |
| Vehicle | TransportVehicle? |
| MaintenanceDate | DateTime |
| Description | string |
| Cost | decimal |
| BillPath | string |
| GarageName | string |
| ClientId | Guid |
| Client | Client? |

### TransportRoute
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| RouteName | string |
| AddedOn | DateTime |
| VehicleId | Guid? |
| Vehicle | TransportVehicle? |
| Stops | ICollection<TransportStop> |
| ClientId | Guid |
| Client | Client? |

### TransportStop
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| RouteId | Guid |
| Route | TransportRoute? |
| StopName | string |
| PickUpTime | TimeSpan |
| DropTime | TimeSpan |
| MonthlyFee | decimal |
| SequenceOrder | int |
| Latitude | decimal? |
| Longitude | decimal? |
| ClientId | Guid |
| Client | Client? |

### TransportVehicle
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| VehicleNo | string |
| Model | string |
| Capacity | int |
| FuelType | string |
| DriverName | string |
| DriverLicenseNo | string |
| DriverContact | string |
| DriverWhatsAppNumber | string |
| RegistrationExpiry | DateTime? |
| RCBookPath | string |
| InsurancePath | string |
| PUCPath | string |
| ClientId | Guid |
| Client | Client? |

## Hostels Module

### Hostel
| Property | Type |
| :--- | :--- |
| HostelName | string |
| HostelType | HostelType |
| Address | string? |
| IntakeCapacity | int |
| Description | string? |
| WardenName | string? |
| WardenContact | string? |
| SupportEmail | string? |
| HasMessFacility | bool |
| ClientId | Guid |
| Client | Client? |

### HostelAllocation
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| HostelRoomId | Guid |
| HostelRoom | HostelRoom? |
| AllocationDate | DateTime |
| ReleaseDate | DateTime? |
| Status | AllocationStatus |
| ClientId | Guid |
| Client | Client? |

### HostelAttendance
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| HostelRoomId | Guid |
| HostelRoom | HostelRoom? |
| AttendanceDate | DateTime |
| Status | HostelAttendanceStatus |
| Remarks | string? |
| ClientId | Guid |
| Client | Client? |

### HostelRoom
| Property | Type |
| :--- | :--- |
| HostelId | Guid |
| Hostel | Hostel? |
| RoomTypeId | Guid |
| RoomType | HostelRoomType? |
| RoomNo | string |
| Floor | string? |
| NoOfBeds | int |
| ClientId | Guid |
| Client | Client? |

### HostelRoomSwapRequest
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| CurrentRoomId | Guid |
| CurrentRoom | HostelRoom? |
| TargetRoomId | Guid |
| TargetRoom | HostelRoom? |
| Reason | string |
| RequestDate | DateTime |
| Status | HostelRoomSwapStatus |
| ClientId | Guid |
| Client | Client? |

### HostelRoomType
| Property | Type |
| :--- | :--- |
| Name | string |
| Description | string? |
| FeeAmount | decimal |
| ClientId | Guid |
| Client | Client? |

## Fees Module

### Fee
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| TotalAmount | decimal |
| PaidAmount | decimal |
| DueAmount | decimal |
| DueDate | DateTime? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### FeeConcessionRequest
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| FeeStructureId | Guid? |
| FeeStructure | FeeStructure? |
| RequestedAmount | decimal |
| RequestedPercentage | decimal |
| Reason | string |
| Status | VerificationStatus |
| AdminRemarks | string? |
| ApprovedBy | string? |
| ApprovalDate | DateTime? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### FeeFine
| Property | Type |
| :--- | :--- |
| ClientId | Guid |
| Client | Client? |
| StudentFeeId | Guid? |
| StudentFee | StudentFee? |
| Amount | decimal |
| AppliedDate | DateTime |
| Reason | string? |

### FeeHead
| Property | Type |
| :--- | :--- |
| Name | string |
| Frequency | FeeFrequency |
| Description | string? |
| FeesGroupId | Guid |
| FeesGroup | FeesGroup? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |
| IsRefundable | bool |
| IsOneTime | bool |

### FeeInstallment
| Property | Type |
| :--- | :--- |
| ClientId | Guid |
| Client | Client? |
| FeeStructureId | Guid? |
| FeeStructure | FeeStructure? |
| InstallmentNo | int |
| Amount | decimal |
| DueDate | DateTime |

### FeeStructure
| Property | Type |
| :--- | :--- |
| ClassId | Guid? |
| Class | Class? |
| FeeHeadId | Guid |
| FeeHead | FeeHead? |
| AcademicStructureId | Guid? |
| AcademicStructure | AcademicStructure? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| Amount | decimal |
| DueDate | DateTime? |
| CategoryId | Guid? |
| Category | Category? |
| IsOptional | bool |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |
| Installments | ICollection<FeeInstallment>? |

### FeeTransaction
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| FeeStructureId | Guid? |
| FeeStructure | FeeStructure? |
| StudentFeeId | Guid? |
| StudentFee | StudentFee? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| Amount | decimal |
| PaidAmount | decimal |
| WaivedAmount | decimal |
| PaymentDate | DateTime |
| PaymentMode | PaymentMode |
| TransactionReference | string? |
| ReceiptNo | string? |
| Remarks | string? |
| DueDate | DateTime? |
| PaymentStatus | FeePaymentStatus |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### FeesGroup
| Property | Type |
| :--- | :--- |
| FeesGroupName | string |
| Description | string? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### Payment
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| ParentId | Guid? |
| Amount | decimal |
| Status | OnlinePaymentStatus |
| RazorpayOrderId | string? |
| RazorpayPaymentId | string? |
| RazorpaySignature | string? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### StudentFee
| Property | Type |
| :--- | :--- |
| BranchId | Guid? |
| Branch | Branch? |
| StudentId | Guid |
| Student | Student? |
| FeeStructureId | Guid |
| FeeStructure | FeeStructure? |
| Amount | decimal |
| Discount | decimal |
| FinalAmount | decimal |
| Status | FeePaymentStatus |
| ClientId | Guid |
| Client | Client? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |

### StudentFeePaymentPlan
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |
| PaymentPlanType | PaymentPlanType |
| TotalAnnualFee | decimal |
| OverallConcession | decimal |
| TotalPayable | decimal |

### StudentOverallInstallment
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |
| InstallmentNo | int |
| Amount | decimal |
| DueDate | DateTime |
| Status | FeePaymentStatus |

## Admission Module

### AdmissionApplication
| Property | Type |
| :--- | :--- |
| ApplicationNo | string |
| StudentName | string |
| ParentName | string |
| ParentPhone | string? |
| ParentEmail | string? |
| AcademicYearId | Guid? |
| AcademicYear | AcademicYear? |
| ClassId | Guid? |
| Class | Class? |
| AcademicStructureId | Guid? |
| AcademicStructure | AcademicStructure? |
| AdmissionType | AdmissionApplicationType |
| ApplicationDate | DateTime |
| DocumentsStatus | AdmissionDocumentsStatus |
| ApplicationStatus | AdmissionApplicationStatus |
| ClientId | Guid |
| Client | Client? |
| Documents | ICollection<AdmissionDocument> |

### AdmissionDocument
| Property | Type |
| :--- | :--- |
| AdmissionApplicationId | Guid |
| AdmissionApplication | AdmissionApplication? |
| DocumentName | string |
| FileUrl | string |
| Status | AdmissionDocumentStatus |
| ClientId | Guid |
| Client | Client? |

### AdmissionInquiry
| Property | Type |
| :--- | :--- |
| StudentName | string |
| Mobile | string |
| Email | string? |
| ClassId | Guid? |
| Class | Class? |
| Status | AdmissionInquiryStatus |
| Source | InquirySource |
| InquiryDate | DateTime |
| FollowUpDate | DateTime? |
| AssignedToStaffId | Guid? |
| AssignedTo | Staff? |
| Remarks | string? |
| BranchId | Guid? |
| Branch | Branch? |
| ClientId | Guid |
| Client | Client? |

### CapAdmission
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| CollegeId | Guid |
| College | College? |
| CourseId | Guid |
| Course | Course? |
| ConfirmedDate | DateTime |
| ClientId | Guid |
| Client | Client? |

### CapRound
| Property | Type |
| :--- | :--- |
| Name | string |
| StartDate | DateTime |
| EndDate | DateTime |
| Status | CapRoundStatus |
| ClientId | Guid |
| Client | Client? |

### College
| Property | Type |
| :--- | :--- |
| Name | string |
| ClientId | Guid |
| Client | Client? |
| Courses | ICollection<Course> |

### Course
| Property | Type |
| :--- | :--- |
| CollegeId | Guid |
| College | College? |
| Name | string |
| TotalSeats | int |
| AvailableSeats | int |
| ClientId | Guid |
| Client | Client? |

### Inquiry
| Property | Type |
| :--- | :--- |
| Description | string? |
| CompanyName | string? |
| Address | string |
| City | string |
| Pincode | string |
| ContactPerson | string |
| ContactEmail | string |
| ContactPhone | string |
| AdditionalNotes | string? |
| Status | InquiryStatus |
| ClientId | Guid? |
| Client | Client? |

### MeritList
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| Rank | int |
| Score | decimal |
| ClientId | Guid |
| Client | Client? |

### SeatAllocation
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| CollegeId | Guid |
| College | College? |
| CourseId | Guid |
| Course | Course? |
| RoundId | Guid |
| Round | CapRound? |
| Status | SeatAllocationStatus |
| ClientId | Guid |
| Client | Client? |

### StudentPreference
| Property | Type |
| :--- | :--- |
| StudentId | Guid |
| Student | Student? |
| CollegeId | Guid |
| College | College? |
| CourseId | Guid |
| Course | Course? |
| PreferenceOrder | int |
| ClientId | Guid |
| Client | Client? |

## Biometric Module

### BiometricDevice
| Property | Type |
| :--- | :--- |
| Name | string |
| SerialNumber | string? |
| IpAddress | string? |
| ProviderType | ProviderType |
| Port | int |
| IsActive | bool |
| DisplayOrder | int |
| Location | string? |
| ClientId | Guid |
| Client | Client? |

### BiometricPunchLog
| Property | Type |
| :--- | :--- |
| ProviderUserId | string |
| DeviceId | Guid? |
| Device | BiometricDevice? |
| PunchTime | DateTime |
| VerificationMode | VerificationMode |
| IsProcessed | bool |
| ClientId | Guid |
| Client | Client? |

### BiometricUserMapping
| Property | Type |
| :--- | :--- |
| UserId | Guid |
| UserType | BiometricUserType |
| ProviderUserId | string |
| ClientId | Guid |
| Client | Client? |

## Subscriptions Module

### BranchSubscription
| Property | Type |
| :--- | :--- |
| BranchId | Guid |
| PlanId | Guid |
| Plan | SubscriptionPlan |
| StudentCount | int |
| Amount | decimal |
| StartDate | DateTime |
| EndDate | DateTime |
| Status | SubscriptionStatus |

### Invoice
| Property | Type |
| :--- | :--- |
| BranchId | Guid |
| InvoiceNumber | string |
| BillingStart | DateTime |
| BillingEnd | DateTime |
| StudentCount | int |
| Amount | decimal |
| Tax | decimal |
| TotalAmount | decimal |

### PricingSlab
| Property | Type |
| :--- | :--- |
| PlanId | Guid |
| Plan | SubscriptionPlan |
| MinStudents | int |
| MaxStudents | int |
| Price | decimal |

### SubscriptionPlan
| Property | Type |
| :--- | :--- |
| Name | string |
| Description | string? |
| PricingType | PricingType |
| BillingCycle | string |
| FixedPrice | decimal? |
| PricingSlabs | ICollection<PricingSlab> |

## Core Module

### Address
| Property | Type |
| :--- | :--- |
| Street | string |
| Area | string? |
| City | string |
| State | string |
| Pincode | string |
| SupplierId | Guid? |
| Supplier | Supplier? |
| ClientId | Guid |
| Client | Client |

### Attachment
| Property | Type |
| :--- | :--- |
| EntityId | Guid |
| EntityType | EntityType |
| FileName | string |
| FilePath | string |
| ContentType | string |
| FileSize | long |
| AttachmentType | AttachmentType |
| AttachmentCategory | AttachmentCategory |
| VerificationStatus | VerificationStatus |
| Remarks | string? |

### BulkImportTask
| Property | Type |
| :--- | :--- |
| Module | ExportModule |
| Status | BulkImportStatus |
| TotalRows | int |
| ProcessedRows | int |
| SuccessCount | int |
| ErrorCount | int |
| TempFilePath | string? |
| ResultsJson | string? |
| FileName | string? |
| ClientId | Guid |
| Client | Client? |

### Client
| Property | Type |
| :--- | :--- |
| Name | string |
| Description | string? |
| Address | string |
| City | string |
| Pincode | string |
| ContactPerson | string |
| ContactEmail | string |
| ContactPhone | string |
| OnboardedAt | DateTime |
| SchoolCode | string? |
| Board | string? |
| AffiliationNumber | string? |
| WebsiteUrl | string? |
| LogoUrl | string? |
| PrimaryUserId | Guid? |
| PrimaryUser | ApplicationUser? |
| Users | ICollection<ApplicationUser> |
| Roles | ICollection<ApplicationRole> |

### ExceptionLog
| Property | Type |
| :--- | :--- |
| Id | Guid |
| ExceptionType | string |
| Message | string |
| StackTrace | string |
| InnerException | string |
| Source | string |
| Path | string |
| QueryString | string |
| Method | string |
| RequestBody | string |
| StatusCode | int |
| CreatedAt | DateTime |
| CreatedBy | string? |

