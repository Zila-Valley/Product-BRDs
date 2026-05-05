# API Documentation

## Overview
The School ERP API is built on .NET Core 10 using RESTful principles. All endpoints (except public ones like Login/Webhook) require a valid JWT Bearer token in the `Authorization` header.

Base URL (Local): `http://localhost:5000/api`
Base URL (Production): `https://api.yourdomain.com/api`

## Authentication
Pass the JWT token in the header:
`Authorization: Bearer <your_token>`

Many endpoints also require a specific permission policy, which is validated by the `PermissionRequirement` handler based on the user's role.

---

## Module: Identity & Authentication

### Login
- **Endpoint:** `POST /api/Auth/login`
- **Purpose:** Authenticate user and issue JWT.
- **Body:** `{ "username": "admin@school.com", "password": "password123" }`
- **Auth Required:** No

### Get Current User Context
- **Endpoint:** `GET /api/Auth/me`
- **Purpose:** Returns the current logged-in user's details, branch context, and assigned permissions.
- **Auth Required:** Yes

---

## Module: Master / Academic Structure

### Get All Classes
- **Endpoint:** `GET /api/Classes`
- **Purpose:** Retrieve all classes for the current branch.
- **Query Params:** `?page=1&pageSize=10`
- **Auth Required:** Yes
- **Permissions:** `ViewClasses`

### Create Class
- **Endpoint:** `POST /api/Classes`
- **Purpose:** Create a new class.
- **Body:** `{ "name": "Class 10", "numericValue": 10 }`
- **Auth Required:** Yes
- **Permissions:** `CreateClasses`

---

## Module: Student Management

### Get Students
- **Endpoint:** `GET /api/Students`
- **Purpose:** List students with filtering.
- **Query Params:** `classId`, `sectionId`, `searchQuery`
- **Auth Required:** Yes

### Get Student Profile
- **Endpoint:** `GET /api/Students/{id}`
- **Purpose:** Get full profile including parents, fees summary, and documents.
- **Auth Required:** Yes

---

## Module: Fees & Finance

### Collect Fee
- **Endpoint:** `POST /api/FeeTransactions/collect`
- **Purpose:** Process a fee payment for a student.
- **Body:** `{ "studentId": "guid", "amountPaid": 5000, "paymentMode": "BankTransfer", "remarks": "Q1 Fees" }`
- **Auth Required:** Yes
- **Permissions:** `CollectFees`

### Get Fee Defaulters
- **Endpoint:** `GET /api/Fees/defaulters`
- **Purpose:** Get list of students with pending dues.
- **Auth Required:** Yes

---

## General API Standards

### Pagination Response Format
```json
{
  "data": [ ... array of items ... ],
  "totalRecords": 150,
  "pageNumber": 1,
  "pageSize": 20,
  "totalPages": 8
}
```

### Standard Error Response Format
```json
{
  "success": false,
  "message": "Validation Failed",
  "errors": {
    "Email": ["Email is already registered."]
  }
}
```

## Developer Notes
- Ensure `BranchId` is never passed from the UI for creation endpoints. The backend `UserContextService` should inject the `BranchId` based on the authenticated user's session to prevent unauthorized cross-branch data manipulation.
- Validate input using DataAnnotations on DTOs.
- Use Asynchronous methods (`Async`) all the way down to the database calls.
