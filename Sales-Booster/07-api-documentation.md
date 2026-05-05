# API Documentation

## Overview
This document provides a summary of the core APIs exposed by the Sales Booster CRM backend. The API is built on .NET Core 8.0.

- **Base URL**: `http://localhost:5000/api` (Local) / `https://api.salesbooster.com/api` (Prod)
- **Authentication**: JWT Bearer Token. Must be passed in the header: `Authorization: Bearer <token>`
- **Response Format**: JSON.
- **Swagger UI**: Available at `/swagger/index.html` when running in development mode.

## Common Response Format
Most APIs return a standard wrapper (unless it's a direct array response):
```json
{
  "success": true,
  "message": "Operation successful",
  "data": { ... }
}
```

## Error Response Format
```json
{
  "success": false,
  "message": "Validation failed",
  "errors": [ "Email is required", "Mobile must be 10 digits" ]
}
```

## Core Modules

### 1. Authentication
**Method**: `POST`  
**Endpoint**: `/auth/login`  
**Auth**: None  
**Purpose**: Authenticate user and retrieve JWT.  
**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "Password123!"
}
```
**Response**: Token details.

### 2. Leads Management
**Method**: `GET`  
**Endpoint**: `/leads`  
**Auth**: Required  
**Purpose**: Retrieve a paginated list of leads.  

**Method**: `POST`  
**Endpoint**: `/leads`  
**Auth**: Required  
**Purpose**: Create a new lead.  
**Request Body**:
```json
{
  "name": "Acme Corp",
  "mobile": "9876543210",
  "email": "contact@acme.com",
  "employeeId": "guid-here",
  "businessUnitId": "guid-here",
  "leadCompanyCategoryId": "guid-here",
  "status": 0
}
```
**Possible Errors**: 400 Bad Request if EmployeeId or BU is missing.

### 3. Attendance
**Method**: `POST`  
**Endpoint**: `/attendance/checkin`  
**Auth**: Required  
**Purpose**: Log employee check-in with location.  
**Request Body**:
```json
{
  "latitude": 19.0760,
  "longitude": 72.8777
}
```

### 4. Sales
**Method**: `POST`  
**Endpoint**: `/sales`  
**Auth**: Required  
**Purpose**: Log a completed sale transaction.  
**Request Body**:
```json
{
  "saleNumber": "SL-001",
  "employeeId": "guid-here",
  "clientId": "guid-here",
  "productId": "guid-here",
  "transactionAmount": 50000
}
```

### 5. Communication (Chat)
**Method**: `POST`  
**Endpoint**: `/messaging/send`  
**Auth**: Required  
**Purpose**: Send a message to a channel/workspace. Note: Real-time delivery happens via SignalR Hub (`/messageHub`).

*Note: For the exhaustive list of endpoints, properties, and testing capabilities, refer to the Swagger UI available during runtime.*
