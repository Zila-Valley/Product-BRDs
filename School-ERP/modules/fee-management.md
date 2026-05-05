# Fee & Finance Management Module

## 1. Module Overview
The Fee Management module is the financial backbone of the ERP. It handles the creation of complex fee structures, recurring billing, concessions (discounts), fine calculations, and payment collection (both online and offline).

## 2. Business Purpose
To eliminate revenue leakage, automate manual receipt generation, provide transparent ledgers for parents, and ensure strict branch-level financial isolation for Trust administrators.

## 3. Users/Roles Involved
- **Accountant:** Configures fee structures, collects offline payments, manages vouchers.
- **Institute Admin:** Reviews daily collection reports and outstanding dues.
- **Parent:** Views fee ledgers and pays online via the Mobile App.

## 4. Features Implemented
- **Fee Heads:** Defines types of fees (Tuition, Transport, Lab).
- **Fee Structures:** Groups Fee Heads and assigns them to specific Classes/Sections.
- **Concessions:** Applies percentage or flat discounts (e.g., Sibling Discount, Staff Child).
- **Fines:** Configures late payment penalty rules.
- **Transactions:** Records payments, generates unique receipt numbers, and updates the student ledger.
- **Ledger & Vouchers:** Tracks general accounting (income/expense) outside of student fees.

## 5. Detected Screens (Web App)
- `Finance > Fee Structures`: Builder interface to map heads to classes.
- `Finance > Collect Fees`: Dual-pane interface (Search Student -> Pay Dues).
- `Finance > Fee Defaulters`: Report grid showing all students with negative balances.
- `Finance > Vouchers`: Interface to log operational expenses (e.g., Electricity Bill).

## 6. Backend APIs
- `GET /api/FeeStructures`
- `POST /api/FeeTransactions/collect`
- `GET /api/Fees/student/{studentId}/ledger`
- `GET /api/Fees/defaulters`
- `POST /api/Vouchers`

## 7. Database Entities
- `FeeHeads`: (Id, Name, Description, BranchId)
- `FeeStructures`: (Id, ClassId, AcademicYearId, TotalAmount)
- `FeeStructureDetails`: Mapping of Structure to Heads.
- `FeeTransactions`: (Id, StudentId, AmountPaid, PaymentMode, ReceiptNo, Date)
- `FeeConcessions`: (Id, StudentId, DiscountAmount, Reason)

## 8. Business Rules & Validations
- **Branch Isolation:** A transaction MUST be permanently tied to the `BranchId` of the user collecting the fee.
- **Overpayment:** The system must handle partial payments and advance payments, updating the `Balance` correctly.
- **Receipts:** Receipt numbers must be sequentially auto-generated per branch and cannot be duplicated or altered once generated.

## 9. Gaps & Recommendations
- **Gap:** Missing automated reconciliation for split-payments (e.g., when a parent pays via Cheque and it bounces).
- **Recommendation:** Implement a specific "Cheque Bounce" workflow that automatically applies a penalty fee and reverses the transaction.
- **Gap:** Complex conditional sibling discounts (e.g., 50% off the younger sibling only if the older sibling is fully paid).
- **Recommendation:** Build a robust rule-engine for concessions.

## 10. Test Scenarios
- Process a partial cash payment and verify the remaining balance matches the expected mathematical output.
- Log in as Branch A Accountant and verify Branch B's fee structures or transactions are completely invisible.
- Apply a 10% concession to a student and verify the generated invoice reflects the discounted total before payment collection.
