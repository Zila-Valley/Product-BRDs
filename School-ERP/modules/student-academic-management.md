# Student & Academic Management Module

## 1. Module Overview
This module represents the core operational center of the school. It manages student profiles, daily classroom attendance, timetables, syllabus tracking, and homework assignments.

## 2. Business Purpose
To provide a unified view of student progression and to organize the logistical nightmare of daily school operations (which teacher is in which classroom at what time) into a clean, digital format.

## 3. Users/Roles Involved
- **Institute Admin:** Sets up Academic Years, Classes, Sections, and Subjects.
- **Teacher:** Takes attendance, assigns homework, and logs syllabus completion.
- **Student/Parent:** Views timetable, attendance reports, and downloads homework.

## 4. Features Implemented
- **Master Data:** Setup of the school's structural tree (Classes -> Sections -> Subjects).
- **Student Directory:** Comprehensive profiles including demographics, guardian details, and document vaults.
- **Timetable:** Matrix mapping Teachers, Subjects, Classes, and Time slots.
- **Attendance:** Daily roll-call interface.
- **Homework & Assignments:** Interface for teachers to post tasks with file attachments and deadlines.
- **Syllabus Tracking:** Teachers can mark specific curriculum topics as 'Completed'.

## 5. Detected Screens (Web App)
- `Academics > Timetable Builder`: Drag and drop (or select) UI to assign teachers to time slots.
- `Students > Directory`: Advanced data table with bulk actions (e.g., Promote Students to next year).
- `Academics > Homework`: Form to create a new assignment, rich text editor for instructions, and file upload.
- `Academics > Daily Attendance`: Fast grid interface for marking students absent.

## 6. Backend APIs
- `GET /api/Students`
- `POST /api/TimeTables`
- `POST /api/Homeworks`
- `POST /api/StudentAttendance/bulk`
- `GET /api/Syllabus/progress/{classId}`

## 7. Database Entities
- `Classes`, `Sections`, `Subjects`
- `Students`: (Id, FirstName, LastName, RollNo, ClassId, SectionId, BranchId)
- `TimeTables`: (Id, ClassId, SectionId, SubjectId, TeacherId, DayOfWeek, StartTime, EndTime)
- `StudentAttendance`: (Id, StudentId, Date, Status)
- `Homeworks`: (Id, ClassId, SectionId, SubjectId, Title, Description, Deadline, AttachmentUrl)

## 8. Business Rules & Validations
- **Promotion:** Students cannot be promoted to the next academic year if they have pending fee dues (configurable rule).
- **Timetable Conflicts:** The API must reject a timetable entry if the `TeacherId` is already scheduled in a different class at the same `StartTime` and `EndTime`.

## 9. Gaps & Recommendations
- **Gap:** Timetable conflict resolution is manual.
- **Recommendation:** Implement a heuristic algorithm or AI-driven auto-timetable generator to suggest conflict-free schedules.
- **Gap:** Homework submissions by students are not explicitly handled in the backend (it operates mostly as a one-way notice board).
- **Recommendation:** Allow students to upload completed assignments via the portal/mobile app for teachers to grade.

## 10. Test Scenarios
- Attempt to assign a teacher to two overlapping time slots and verify the system blocks the action.
- Promote a class of students to the next academic year and verify their `ClassId` updates, and a historical record is maintained.
- Assign homework via the web app and verify the push notification is received on the mobile app.
