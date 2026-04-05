# Zorvyn API Documentation

## Overview
This document provides a professional reference for the Zorvyn Finance Dashboard API.

## Live Documentation
- Postman public docs: <add-public-docs-link>
- Postman collection: <add-collection-link>
- Swagger UI (secondary): http://localhost:8080/swagger-ui.html
- OpenAPI JSON (secondary): http://localhost:8080/v3/api-docs

## Base URL
- Local: http://localhost:8080
- Production: <add-production-base-url>

## Authentication
The API uses JWT Bearer tokens for protected routes.

### Login
- `POST /api/v1/auth/login`
- Returns `accessToken` and `refreshToken`.

### Using the Access Token
Include the header on protected routes:
```
Authorization: Bearer <accessToken>
```

### Refresh Token
- `POST /api/v1/auth/refresh`
- Header: `X-Refresh-Token: <refreshToken>`

### Logout
- `POST /api/v1/auth/logout`
- Header: `Authorization: Bearer <accessToken>`

## Roles
- `ROLE_ADMIN`: full access
- `ROLE_ANALYST`: read access to records and analytics
- `ROLE_VIEWER`: read-only access to summary and recent activity

## Standard Response Envelope
All responses are wrapped in a consistent envelope:
```json
{
  "success": true,
  "message": "Record created successfully",
  "data": { "...": "..." },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

## Pagination
Endpoints that return lists accept the following query parameters:
- `page` (default: 0)
- `size` (default: 20)
- `sort` (example: `createdAt,desc`)

## Endpoints

### Auth

#### Login
**POST** `/api/v1/auth/login`

Request:
```json
{
  "email": "admin@finance.com",
  "password": "Admin@1234"
}
```

Response (200):
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "accessToken": "<jwt>",
    "refreshToken": "<jwt>",
    "tokenType": "Bearer",
    "expiresIn": 900000
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Refresh Token
**POST** `/api/v1/auth/refresh`

Header:
```
X-Refresh-Token: <refreshToken>
```

Response (200):
```json
{
  "success": true,
  "message": "Token refreshed",
  "data": {
    "accessToken": "<jwt>",
    "refreshToken": "<jwt>",
    "tokenType": "Bearer",
    "expiresIn": 900000
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Logout
**POST** `/api/v1/auth/logout`

Header:
```
Authorization: Bearer <accessToken>
```

Response (200):
```json
{
  "success": true,
  "message": "Logged out successfully",
  "data": null,
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

### Users (Admin only)

#### Create User
**POST** `/api/v1/users`

Request:
```json
{
  "email": "analyst1@finance.com",
  "password": "Analyst@1234",
  "fullName": "Analyst One",
  "roleName": "ROLE_ANALYST"
}
```

Response (201):
```json
{
  "success": true,
  "message": "User created successfully",
  "data": {
    "id": 2,
    "email": "analyst1@finance.com",
    "fullName": "Analyst One",
    "status": "ACTIVE",
    "roles": ["ROLE_ANALYST"],
    "createdAt": "2026-04-05T10:20:30.000"
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Get Users (Paginated)
**GET** `/api/v1/users?page=0&size=20&sort=createdAt,desc`

Response (200):
```json
{
  "success": true,
  "message": "Users retrieved",
  "data": {
    "content": [
      {
        "id": 1,
        "email": "admin@finance.com",
        "fullName": "System Admin",
        "status": "ACTIVE",
        "roles": ["ROLE_ADMIN"],
        "createdAt": "2026-04-05T10:20:30.000"
      }
    ],
    "page": 0,
    "size": 20,
    "totalElements": 1,
    "totalPages": 1,
    "first": true,
    "last": true
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Get User by ID
**GET** `/api/v1/users/{id}`

Response (200):
```json
{
  "success": true,
  "message": "User retrieved",
  "data": {
    "id": 1,
    "email": "admin@finance.com",
    "fullName": "System Admin",
    "status": "ACTIVE",
    "roles": ["ROLE_ADMIN"],
    "createdAt": "2026-04-05T10:20:30.000"
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Update User
**PUT** `/api/v1/users/{id}`

Request:
```json
{
  "fullName": "Analyst One Updated",
  "status": "ACTIVE",
  "roleName": "ROLE_ANALYST"
}
```

Response (200):
```json
{
  "success": true,
  "message": "User updated",
  "data": {
    "id": 2,
    "email": "analyst1@finance.com",
    "fullName": "Analyst One Updated",
    "status": "ACTIVE",
    "roles": ["ROLE_ANALYST"],
    "createdAt": "2026-04-05T10:20:30.000"
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Deactivate User
**DELETE** `/api/v1/users/{id}`

Response (200):
```json
{
  "success": true,
  "message": "User deactivated",
  "data": null,
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Assign Role
**POST** `/api/v1/users/{id}/roles?roleName=ROLE_ANALYST`

Response (200):
```json
{
  "success": true,
  "message": "Role assigned successfully",
  "data": null,
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

### Financial Records

#### Create Record (Admin only)
**POST** `/api/v1/records`

Request:
```json
{
  "amount": 1500.00,
  "type": "INCOME",
  "category": "Salary",
  "recordDate": "2024-01-15",
  "description": "Monthly salary"
}
```

Response (201):
```json
{
  "success": true,
  "message": "Record created successfully",
  "data": {
    "id": 1,
    "amount": 1500.00,
    "type": "INCOME",
    "category": "Salary",
    "recordDate": "2024-01-15",
    "description": "Monthly salary",
    "createdBy": 1,
    "createdAt": "2026-04-05T10:20:30.000"
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Get Records (Admin/Analyst)
**GET** `/api/v1/records`

Query parameters:
- `type`: `INCOME` or `EXPENSE`
- `category`: string
- `startDate`: `yyyy-MM-dd`
- `endDate`: `yyyy-MM-dd`
- `page`, `size`, `sort`

Example:
```
GET /api/v1/records?type=INCOME&category=Salary&startDate=2024-01-01&endDate=2024-12-31&page=0&size=20&sort=createdAt,desc
```

Response (200):
```json
{
  "success": true,
  "message": "Records retrieved",
  "data": {
    "content": [
      {
        "id": 1,
        "amount": 1500.00,
        "type": "INCOME",
        "category": "Salary",
        "recordDate": "2024-01-15",
        "description": "Monthly salary",
        "createdBy": 1,
        "createdAt": "2026-04-05T10:20:30.000"
      }
    ],
    "page": 0,
    "size": 20,
    "totalElements": 1,
    "totalPages": 1,
    "first": true,
    "last": true
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Get Record by ID (Admin/Analyst)
**GET** `/api/v1/records/{id}`

Response (200):
```json
{
  "success": true,
  "message": "Record retrieved",
  "data": {
    "id": 1,
    "amount": 1500.00,
    "type": "INCOME",
    "category": "Salary",
    "recordDate": "2024-01-15",
    "description": "Monthly salary",
    "createdBy": 1,
    "createdAt": "2026-04-05T10:20:30.000"
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Update Record (Admin only)
**PUT** `/api/v1/records/{id}`

Request:
```json
{
  "amount": 120.75,
  "type": "EXPENSE",
  "category": "Utilities",
  "recordDate": "2024-01-20",
  "description": "Electricity bill"
}
```

Response (200):
```json
{
  "success": true,
  "message": "Record updated",
  "data": {
    "id": 1,
    "amount": 120.75,
    "type": "EXPENSE",
    "category": "Utilities",
    "recordDate": "2024-01-20",
    "description": "Electricity bill",
    "createdBy": 1,
    "createdAt": "2026-04-05T10:20:30.000"
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Delete Record (Admin only)
**DELETE** `/api/v1/records/{id}`

Response (200):
```json
{
  "success": true,
  "message": "Record deleted successfully",
  "data": null,
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

### Dashboard

#### Summary (Admin/Analyst/Viewer)
**GET** `/api/v1/dashboard/summary`

Response (200):
```json
{
  "success": true,
  "message": "Summary retrieved",
  "data": {
    "totalIncome": 5000.00,
    "totalExpenses": 2500.00,
    "netBalance": 2500.00,
    "recordCount": 42,
    "lastUpdated": "2026-04-05T10:20:30.000"
  },
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Category Breakdown (Admin/Analyst)
**GET** `/api/v1/dashboard/categories`

Response (200):
```json
{
  "success": true,
  "message": "Category breakdown retrieved",
  "data": [
    {
      "category": "Salary",
      "total": 5000.00
    },
    {
      "category": "Utilities",
      "total": 350.00
    }
  ],
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Monthly Trends (Admin/Analyst)
**GET** `/api/v1/dashboard/trends/{year}`

Example:
```
GET /api/v1/dashboard/trends/2024
```

Response (200):
```json
{
  "success": true,
  "message": "Monthly trends retrieved",
  "data": [
    {
      "month": 1,
      "monthName": "January",
      "income": 1200.00,
      "expense": 800.00
    }
  ],
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

#### Recent Activity (Admin/Analyst/Viewer)
**GET** `/api/v1/dashboard/recent?limit=10`

Response (200):
```json
{
  "success": true,
  "message": "Recent activity retrieved",
  "data": [
    {
      "id": 5,
      "amount": 120.75,
      "type": "EXPENSE",
      "category": "Utilities",
      "recordDate": "2024-01-20",
      "description": "Electricity bill",
      "createdBy": 1,
      "createdAt": "2026-04-05T10:20:30.000"
    }
  ],
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": []
}
```

## Error Responses
Validation and authorization errors are returned with `success: false` and populated `errors` array:
```json
{
  "success": false,
  "message": "Validation error",
  "data": null,
  "timestamp": "2026-04-05T10:20:30.000",
  "errors": [
    "email must be a well-formed email address"
  ]
}
```

## Postman Testing Notes
- Use the provided Postman collection and environment in `postman/`.
- The Login request auto-saves tokens into environment variables.
- All protected requests use `Authorization: Bearer {{accessToken}}`.
