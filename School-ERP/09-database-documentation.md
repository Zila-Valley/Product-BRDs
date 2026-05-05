# Database Design & Architecture

## 1. Database Overview
The School ERP utilizes **PostgreSQL** as its primary relational database. Entity Framework Core (EF Core 10) is used as the Object-Relational Mapper (ORM). The database follows a multi-tenant schema design using a logical discriminator (e.g., `BranchId`) rather than separate databases per tenant.

## 2. Core Entities & Tables

### Security & Identity
- **AspNetUsers:** Stores user credentials, emails, phone numbers, and status.
- **AspNetRoles:** Defines custom roles (Super Admin, Teacher, Parent, etc.).
- **AspNetUserRoles:** Mapping between users and roles.
- **Permissions / RolePermissions:** Stores system actions and maps them to roles.

### Multi-Tenancy
- **Clients:** The top-level entity (e.g., an Educational Trust).
- **Branches / Institutes:** Individual schools or colleges under a Client. Most transactional tables link to a `BranchId`.

### Academic Structure
- **AcademicYears:** Defines sessions (e.g., 2024-2025).
- **Classes & Sections:** Defines standard grades and their divisions.
- **Subjects:** Maps to classes/sections.

### User Entities
- **Students:** Contains admission details, demographics, and links to Parent, Class, and Section.
- **Parents:** Contains guardian details. Linked 1-to-N with Students.
- **Staff:** Contains employee details, designation, and department.

### Finance & Fees
- **FeeHeads:** Types of fees (Tuition, Transport, Library).
- **FeeStructures:** Grouping of Fee Heads mapped to classes.
- **FeeTransactions / Vouchers:** Financial ledgers tracking payments, receipts, and dues.

### Example ER Diagram (High-Level)
```mermaid
erDiagram
    CLIENT ||--o{ BRANCH : owns
    BRANCH ||--o{ STUDENT : enrolls
    BRANCH ||--o{ STAFF : employs
    BRANCH ||--o{ CLASS : has
    CLASS ||--o{ SECTION : split_into
    STUDENT }|--|| SECTION : assigned_to
    PARENT ||--o{ STUDENT : guardian_of
    STUDENT ||--o{ FEE_TRANSACTION : pays
```

## 3. Important Design Patterns

### Multi-Tenant Isolation
Almost every table containing operational data (Students, Fees, Attendance) includes a `BranchId` column.
EF Core **Global Query Filters** are applied in `ApplicationDbContext` to automatically filter out records that do not belong to the currently authenticated user's branch.

```csharp
// Example conceptually
builder.Entity<Student>().HasQueryFilter(s => s.BranchId == _currentBranchId);
```

### Soft Deletes
To prevent accidental data loss and maintain referential integrity in historical reports, tables utilize soft deletes.
- Column: `IsDeleted` (boolean)
- Columns: `DeletedBy`, `DeletedAt`
EF Core interceptors/filters automatically handle setting the flag on delete and hiding them from queries.

### Auditing
Tables include standard audit fields:
- `CreatedBy` (string/Guid)
- `CreatedAt` (timestamp)
- `UpdatedBy` (string/Guid)
- `UpdatedAt` (timestamp)

## 4. Key Relationships
- **Student -> Class/Section:** Essential for all academic processing.
- **Staff -> Designation/Department:** Essential for HR and Payroll rules.
- **FeeStructure -> FeeHead -> Student:** The core matrix for calculating outstanding dues.

## 5. Potential Issues & Suggestions
- **Missing Indexes:** Ensure Foreign Keys (`BranchId`, `StudentId`, `ClassId`) have non-clustered indexes, as they are frequently used in `WHERE` and `JOIN` clauses.
- **JSON Columns:** For highly dynamic data (like custom form fields in Enquiries), consider using PostgreSQL's `JSONB` column type for flexibility without schema changes.
- **Archiving:** Large tables like `StaffAttendance` and `StudentAttendance` will grow rapidly. Consider a strategy to archive old academic year data to cold storage/history tables to maintain query performance on active data.
