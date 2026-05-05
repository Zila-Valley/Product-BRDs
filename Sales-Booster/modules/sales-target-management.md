# Sales & Target Management Module

## Purpose
To define product-level sales goals for employees, track actual sales generated, and monitor revenue collections against those sales.

## Business Users
- Sales Managers
- Sales Executives
- Finance / Admin

## Features Implemented
- Define Monthly Sales Targets per employee/product.
- Log Sale Transactions linked to Clients and Leads.
- Log Collections (payments) received against Sales.
- Automated Job to generate/renew targets on the 1st of every month.

## Screens Found in React
- `/sales` -> `SalesListPage.jsx`
- `/target` -> `EmployeeTargetPage.js`
- `/categories` -> `CategoryPage.jsx`
- `/product` -> `ProductPage.jsx`

## APIs Found in .NET Core
- `SalesController.cs`
- `TargetsController.cs` (or `MonthlySalesTargetController.cs`)
- `SaleCollectionsController.cs`
- `ProductsController.cs`

## Database Entities Used
- `Sale`
- `MonthlySalesTarget`
- `MonthlyCollectionTarget`
- `Collection`
- `Product`
- `Category`

## Business Workflow
1. Admin configures Product Categories and Products.
2. Manager sets a `MonthlySalesTarget` for an Employee.
3. Employee closes a lead and records a `Sale` with a `TransactionAmount`.
4. Over time, the Employee logs `Collection` amounts against the `Sale`.
5. Dashboard compares the sum of `Sale.TransactionAmount` against the `MonthlySalesTarget`.

## Field List (Sale Entity)
- `SaleNumber` (string)
- `TransactionAmount` (decimal)
- `DateTime` (DateTime)
- `ProductId` (Guid, FK)
- `EmployeeId` (Guid, FK)
- `LeadId` (Guid, Nullable FK)
- `ClientId` (Guid, FK)

## Validation Rules
- Transaction Amount must be greater than zero.
- Total Collections against a Sale cannot exceed the Sale's Transaction Amount.

## Role/Permission Rules
- Sales Executives can log sales and view their own targets.
- Managers can define targets for their subordinates.

## Current Gaps
- Invoicing is not built-in; it assumes invoicing happens in a separate ERP (like Tally) and CRM just records the total value.

## Recommended Improvements
- Add Quotation Generation to standardize pricing before a Sale is recorded.
- Add payment gateway integrations for digital collections.

## Acceptance Criteria
- Recording a Sale correctly links to the Client and Employee.
- Logging a Collection updates the overall collected amount accurately.

## Test Scenarios
1. **Target Verification**: Set target of 100,000 -> Log Sale of 50,000 -> Verify Dashboard shows 50% achievement.
2. **Collection Overpay**: Attempt to log a Collection of 60,000 against a Sale of 50,000 -> System should reject.
