# Facilities Management: Transport, Hostel & Library

## 1. Module Overview
This document covers the auxiliary facility modules. While not core to academic grading, these modules are critical for the physical logistics and extra-curricular management of the institution.

## 2. Business Purpose
- **Transport:** Track vehicle maintenance, fuel usage, and route allocations to ensure student safety and optimize costs.
- **Hostel:** Manage room occupancy, mess fees, and student tracking.
- **Library:** Digitize book catalogs, manage issues/returns, and automate fine collection.

## 3. Users/Roles Involved
- **Transport Manager:** Manages buses, drivers, routes.
- **Warden:** Manages hostel rooms and student discipline.
- **Librarian:** Manages book issues and cataloging.
- **Accountant:** Collects specific facility fees based on allocations.

## 4. Features Implemented
### Transport
- Route creation and stop mapping.
- Vehicle registration and maintenance logging.
- Student allocation to routes (which automatically triggers transport fee heads).
### Hostel
- Room Type (AC/Non-AC) and Room creation.
- Bed allocation and vacancy tracking.
- Hostel attendance tracking.
### Library
- Book inventory cataloging.
- Issue and Return logging.
- Automated late fine calculation based on `LibrarySettings`.

## 5. Detected Screens (Web App)
- `Transport > Routes`: Define paths and assign pick-up costs.
- `Hostel > Room Allocations`: Visual grid showing occupied vs empty beds.
- `Library > Issue Book`: Search for student, scan book ID, and issue.

## 6. Backend APIs
- `GET /api/TransportRoutes`
- `POST /api/TransportAllocations`
- `POST /api/HostelAllocations`
- `POST /api/Books/issue`
- `POST /api/Books/return`

## 7. Database Entities
- **Transport:** `TransportRoutes`, `TransportVehicles`, `TransportStops`, `TransportAllocations`.
- **Hostel:** `HostelRooms`, `HostelRoomTypes`, `HostelAllocations`.
- **Library:** `Books`, `LibraryMembers`, `BookIssues` (Id, BookId, MemberId, IssueDate, DueDate, ReturnDate, FineAmount).

## 8. Business Rules & Validations
- **Capacity:** A student cannot be allocated to a Hostel Room or a Transport Vehicle if `CurrentOccupancy >= MaxCapacity`.
- **Library Limits:** A student cannot issue a new book if they have exceeded their `MaxBooksAllowed` quota or have pending library fines.

## 9. Gaps & Recommendations
- **Gap:** Transport module lacks live GPS integration.
- **Recommendation:** Integrate with third-party GPS hardware APIs to provide live bus tracking on the parent mobile app.
- **Gap:** Library issue/return relies on manual typing.
- **Recommendation:** Implement barcode scanner support directly into the web UI for rapid book processing.

## 10. Test Scenarios
- Try to allocate a student to a fully occupied hostel room and verify the UI throws a validation error.
- Return a book 5 days past its due date and verify the system auto-calculates the correct fine amount based on the library configuration.
- Allocate a student to a transport route and verify their fee ledger automatically updates with the transport fee.
