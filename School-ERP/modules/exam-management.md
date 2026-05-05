# Exam & Result Management Module

## 1. Module Overview
The Exam module digitizes the academic assessment process. It handles the scheduling of exams, the generation of admit cards, fast marks entry by teachers, and the automated compilation of final report cards and tabulation sheets.

## 2. Business Purpose
To eliminate calculation errors in grading, provide parents with instant digital access to results, and save teachers hundreds of hours previously spent on manual data entry and report card printing.

## 3. Users/Roles Involved
- **Institute Admin:** Creates Exam Groups, configures grading scales.
- **Teacher:** Enters marks for their assigned subjects.
- **Student/Parent:** Downloads Admit Cards, views published results.

## 4. Features Implemented
- **Exam Configuration:** Setup Exam Categories, Groups, and grading structures (e.g., A+, A, B).
- **Scheduling:** Map subjects to dates and times; generate printable Admit Cards.
- **Marks Entry:** Grid-based data entry for teachers, supporting maximum marks and passing criteria validation.
- **Result Processing:** Calculates total marks, percentages, grades, and ranks.
- **Online Exams (Objective):** Feature for creating Multiple Choice Questions (MCQs) for students to take directly via the web/mobile portal.

## 5. Detected Screens (Web App)
- `Exams > Exam Schedule`: UI to define the timetable for an exam group.
- `Exams > Marks Entry`: Teacher dashboard selecting Class -> Section -> Subject, displaying a grid of students.
- `Exams > Tabulation Sheet`: A master matrix showing all students and their marks across all subjects for admin review.
- `Exams > Generate Results`: Process button to compile and lock the results, triggering PDF generation.

## 6. Backend APIs
- `POST /api/ExamSchedules`
- `POST /api/ExamMarks/bulk-update`
- `GET /api/Results/tabulation/{examGroupId}`
- `POST /api/OnlineExams/submit-attempt`
- `GET /api/AdmitCards/{studentId}`

## 7. Database Entities
- `ExamGroups`: (Id, Name, AcademicYearId, BranchId)
- `ExamSchedules`: (Id, ExamGroupId, SubjectId, ExamDate, MaxMarks, MinMarks)
- `ExamMarks`: (Id, ExamScheduleId, StudentId, MarksObtained, IsAbsent, Remarks)
- `OnlineExamConfigs`: (Id, Title, DurationMinutes, TotalQuestions)
- `StudentExamAttempts`: (Id, OnlineExamId, StudentId, Score, SubmittedAt)

## 8. Business Rules & Validations
- **Validation:** `MarksObtained` cannot exceed `MaxMarks` defined in the `ExamSchedule`.
- **Absenteeism:** If `IsAbsent` is true, the student automatically receives 0 marks and a specific "ABS" tag on the report card.
- **Locking:** Once results are "Published", marks can no longer be edited by Teachers; they require an Admin override.

## 9. Gaps & Recommendations
- **Gap:** Report cards use hardcoded PDF templates. Different schools have vastly different aesthetic requirements for report cards.
- **Recommendation:** Implement a drag-and-drop HTML/PDF template builder allowing schools to place logos, signatures, and data tags dynamically.
- **Gap:** Subjective online exams are not supported (only MCQs).
- **Recommendation:** Add support for file-upload answers in the online exam portal where teachers can manually grade the uploaded files.

## 10. Test Scenarios
- Enter marks exceeding the maximum threshold and verify the UI/API rejects the input.
- Process results for a class and verify that the calculated Percentage and assigned Grade exactly match the configured Grading Scale logic.
- As a student, attempt an online exam, let the timer run out, and verify the exam auto-submits and calculates the score correctly.
