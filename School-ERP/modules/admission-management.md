# Admission Management Module

## 1. Module Overview
The Admission Management module digitizes the entire student enrollment process, starting from the initial parent inquiry through to application processing, document verification, and final student profile creation.

## 2. Business Purpose
To replace paper-based admission forms and disconnected Excel tracking sheets, ensuring a single source of truth for leads (inquiries) and a streamlined conversion process into active students, reducing administrative overhead and data entry errors.

## 3. Users/Roles Involved
- **Receptionist / Front Office:** Captures walk-in or phone inquiries.
- **Institute Admin:** Reviews applications, approves/rejects admissions.
- **Parent:** Fills out online inquiry/application forms via the web portal or mobile app.

## 4. Features Implemented
- **Inquiry Capture:** Record basic details (Parent Name, Phone, Target Class).
- **Follow-up Tracking:** Log communication history with the lead.
- **Application Processing:** Collect detailed demographics and academic history.
- **Document Upload:** Secure vault for Birth Certificates, Previous Marksheets, etc.
- **Conversion:** One-click conversion from "Inquiry" to "Active Student".

## 5. Detected Screens (Web App)
- `Front Office > Inquiries List`: Data grid of all leads with status tags (Pending, Converted, Lost).
- `Front Office > Add Inquiry Modal`: Form to capture lead details.
- `Admissions > Application Form`: Multi-step wizard for full enrollment details.
- `Admissions > Document Verification`: Screen to view uploaded PDFs/Images and approve.

## 6. Backend APIs
- `GET /api/Inquiries`
- `POST /api/Inquiries`
- `PUT /api/Inquiries/{id}/status`
- `POST /api/Admissions/convert` (Converts Inquiry to Student entity)
- `POST /api/Admissions/upload-document`

## 7. Database Entities
- `Inquiries`: (Id, ParentName, Phone, TargetClassId, Status, BranchId)
- `AdmissionApplications`: (Id, InquiryId, FormDataJson, Status)
- `StudentDocuments`: (Id, StudentId/ApplicationId, DocumentType, FileUrl)

## 8. Business Rules & Validations
- A phone number cannot be registered for a new inquiry if an active inquiry already exists for the same academic year and target class.
- Admission cannot be finalized until mandatory documents (e.g., Birth Certificate) are marked as 'Verified'.

## 9. Gaps & Recommendations
- **Gap:** No dynamic form builder; the application form has hardcoded fields.
- **Recommendation:** Implement a custom field generator so different branches can ask specific questions without code changes.
- **Gap:** No online application fee payment gateway integration.
- **Recommendation:** Integrate Razorpay/Stripe directly into the public inquiry form.

## 10. Test Scenarios
- Verify an inquiry is successfully created and appears in the Institute Admin's dashboard.
- Verify that attempting to convert an inquiry without mandatory documents throws an error.
- Verify that converting an inquiry successfully creates a record in the `Students` table and generates a unique Roll No/Student ID.
