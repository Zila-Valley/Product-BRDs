# Customer (Client) Management Module

## Purpose
To manage onboarded clients/customers who have purchased products or services, and to act as the primary anchor for multi-tenancy.

## Business Users
- Admins
- Sales Managers
- Account Managers

## Features Implemented
- Create and edit Clients.
- Link users to clients (Primary User).
- Link Business Units to a Client.
- Provide a structured view of the organizational setup per client.

## Screens Found in React
- `/clients` -> `ClientsPage.jsx`
- `ClientOnboarding.jsx`

## APIs Found in .NET Core
- `ClientsController.cs`

## Database Entities Used
- `Client`
- `BusinessUnit`

## Business Workflow
1. A Lead is successfully closed.
2. An Admin or Manager onboards the customer via `ClientOnboarding`.
3. Client record is created along with its initial `BusinessUnit` and `PrimaryUserId`.
4. Subsequent Sales and Leads can be tagged to this `ClientId`.

## Field List (Client Entity)
- `Name` (string)
- `Description` (string, nullable)
- `Address` (string)
- `City` (string)
- `Pincode` (string)
- `ContactPerson` (string)
- `ContactEmail` (string)
- `ContactPhone` (string)
- `OnboardedAt` (DateTime)
- `PrimaryUserId` (Guid, Nullable FK)

## Validation Rules
- `Name`, `ContactEmail`, and `ContactPhone` must be present.
- Email formats must be valid.

## Role/Permission Rules
- Only Admins or designated Account Managers can edit Client core details.
- `ClientId` acts as a data boundary for many queries.

## Current Gaps
- No automated invoice/billing lifecycle attached to the Client.
- No visual "360-degree view" showing all Leads, Sales, and Support Tickets on one screen.

## Recommended Improvements
- Implement a 360-degree Customer View tab.
- Add document management (Contract uploads) to the Client profile.

## Acceptance Criteria
- Client can be created with valid contact details.
- Multiple Business Units can be attached to one Client.

## Test Scenarios
1. **Onboarding**: Verify a new client is successfully created with a Primary User.
2. **Editing**: Verify that editing an address reflects across related views.
