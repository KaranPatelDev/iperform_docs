# API Design

## REST API Overview

iPerform exposes a comprehensive REST API with **50+ endpoints** covering all business operations, organized into logical domains.

---

## API Architecture

### **Base URL**
```
https://iperform.magentabi.com/api/v1     (Production)
https://iperform.test.magenta-bi.com/api/v1 (Testing)
http://localhost:5000/api/v1               (Development)
```

### **Authentication**
```
Type: Bearer Token (JWT)
Header: Authorization: Bearer <jwt_token>
Expiration: Configurable (typically 24 hours)
Refresh: Token refresh endpoint available
```

### **Response Format**
```json
{
  "status": 200,
  "message": "Operation successful",
  "data": { ... },
  "count": 10,
  "page": 1,
  "total": 100,
  "timestamp": "2024-02-05T10:30:00Z"
}
```

### **Error Responses**
```json
{
  "status": 400,
  "message": "Bad request",
  "error": "Validation failed",
  "details": {
    "field": "error message"
  }
}
```

---

## API Endpoints by Domain

### **1. Authentication Endpoints**

#### **POST /i_auth**
- **Purpose**: Authenticate user with iPerform credentials
- **Method**: POST
- **Request Body**:
  ```json
  {
    "username": "user@example.com",
    "password": "encrypted_password",
    "user_agent": "Mozilla/5.0...",
    "authflow": "jwt"           // optional
  }
  ```
- **Response**:
  ```json
  {
    "status": 200,
    "token": "eyJhbGciOiJIUzI1NiIs...",
    "user_id": 5,
    "role": "SALESPERSON",
    "company_id": 15,
    "expires_in": 86400
  }
  ```
- **Error Cases**:
  - 500: User not found
  - 500: Password mismatch
  - 500: User inactive
  - 505: Suspended account

#### **POST /ex_auth**
- **Purpose**: Authenticate with Extractor credentials
- **Method**: POST
- **Request Body**:
  ```json
  {
    "username": "extractor_user",
    "password": "password",
    "company_id": 15
  }
  ```
- **Response**: Similar to /i_auth

#### **POST /logout**
- **Purpose**: Invalidate current session
- **Method**: POST
- **Authentication**: Required
- **Response**: 200 OK

---

### **2. Lead Management Endpoints**

#### **GET /leads**
- **Purpose**: Retrieve list of leads
- **Method**: GET
- **Query Parameters**:
  ```
  ?status=Demo&salesperson_id=5&page=1&limit=20
  ?search=ABC&company_id=15
  ?created_from=2024-01-01&created_to=2024-02-05
  ```
- **Response**: Array of lead objects with pagination
- **Access**: Role-based filtering applied automatically

**GET /leads/:id**
- **Purpose**: Get single lead details
- **Method**: GET
- **Response**: Complete lead object with all fields

#### **POST /leads**
- **Purpose**: Create new lead
- **Method**: POST
- **Request Body**:
  ```json
  {
    "firm_name": "ABC Corp",
    "client_first_name": "John",
    "client_last_name": "Doe",
    "client_email_id": "john@example.com",
    "client_mobile_no": "+91-9876543210",
    "industry": "IT",
    "erp": "SAP",
    "status": "New",
    "source": "Facebook",
    "company_id": 15
  }
  ```
- **Response**: Created lead object with ID
- **Validation**: Email uniqueness per company
- **Access**: ADMIN, MANAGER, SALESPERSON, INTEGRATION

#### **PUT /leads/:id**
- **Purpose**: Update existing lead
- **Method**: PUT
- **Request Body**: Partial or complete lead object
- **Response**: Updated lead object
- **Audit**: Change logged in lead_logs

#### **DELETE /leads/:id**
- **Purpose**: Delete lead (soft delete)
- **Method**: DELETE
- **Response**: 200 OK
- **Effect**: Sets is_deleted=1, moves to archive

#### **POST /leads/bulk-action**
- **Purpose**: Perform bulk operations on multiple leads
- **Method**: POST
- **Request Body**:
  ```json
  {
    "action": "update_status|assign|share|delete",
    "lead_ids": [1, 2, 3, 4, 5],
    "data": {
      "status": "Proposal",
      "warrior_id": 10
    }
  }
  ```
- **Response**: Count of affected records

#### **POST /leads/import**
- **Purpose**: Bulk import leads from CSV/Excel
- **Method**: POST (multipart/form-data)
- **Request**: CSV file with lead data
- **Response**:
  ```json
  {
    "imported": 45,
    "failed": 2,
    "errors": [
      {"row": 3, "error": "Invalid email format"}
    ]
  }
  ```

---

### **3. Lead Information Endpoints**

#### **POST /get_lead_info**
- **Purpose**: Get lead field options and configurations
- **Method**: POST
- **Request Body**: `{"__token": "token"}`
- **Response**:
  ```json
  {
    "status": 200,
    "data": {
      "LEAD_STATUS": ["New", "Demo", "Proposal", "Trial", "Won", "Lost"],
      "LEAD_TYPE": ["Hot", "Warm", "Cold"],
      "INDUSTRY": ["IT", "Finance", "Retail", ...],
      "LEAD_SOURCE": ["Facebook", "LinkedIn", "Manual", ...],
      "REASON_FOR_LOST": ["Budget", "Timeline", "Competition", ...],
      "ERP_LIST": ["SAP", "Tally", "Zoho", ...],
      "NATURE_OF_BUSINESS": ["B2B", "B2C", "B2B2C"]
    }
  }
  ```
- **Access**: All authenticated users

---

### **4. Reporting Endpoints**

#### **GET /reports/salesperson-report**
- **Purpose**: Get salesperson performance metrics
- **Method**: GET
- **Query Parameters**:
  ```
  ?salesperson_id=5&from_date=2024-01-01&to_date=2024-02-05
  ?company_id=15&group_by=week
  ```
- **Response**:
  ```json
  {
    "status": 200,
    "data": {
      "salesperson_id": 5,
      "salesperson_name": "John Smith",
      "leads_assigned": 50,
      "leads_converted": 12,
      "conversion_rate": 24,
      "pipeline_value": 500000,
      "avg_deal_value": 41667
    }
  }
  ```

#### **GET /reports/activity-report**
- **Purpose**: Get activity metrics
- **Method**: GET
- **Query Parameters**:
  ```
  ?user_id=5&from_date=2024-01-01&to_date=2024-02-05
  ?metric=logins|calls|emails|followups
  ```
- **Response**: Activity metrics by time window

#### **GET /reports/lead-funnel**
- **Purpose**: Get lead funnel analysis
- **Method**: GET
- **Query Parameters**:
  ```
  ?company_id=15&from_date=2024-01-01&to_date=2024-02-05
  ```
- **Response**:
  ```json
  {
    "status": 200,
    "data": {
      "New": 100,
      "Demo": 45,
      "Proposal": 20,
      "Trial": 10,
      "Won": 5,
      "Lost": 25
    }
  }
  ```

#### **GET /reports/custom**
- **Purpose**: Execute custom report with dynamic query
- **Method**: GET or POST
- **Request Body** (POST):
  ```json
  {
    "query": "SELECT status, COUNT(*) FROM leads WHERE company_id = ? GROUP BY status",
    "parameters": [15]
  }
  ```
- **Response**: Query result set
- **Access**: ADMIN, MANAGER, INTEGRATION (with restrictions)

---

### **5. Communication Endpoints**

#### **POST /send-email**
- **Purpose**: Send email to lead
- **Method**: POST
- **Request Body**:
  ```json
  {
    "lead_id": 100,
    "recipient_email": "john@example.com",
    "subject": "Demo Scheduled",
    "template_id": 5,
    "variables": {
      "name": "John",
      "date": "2024-02-10"
    }
  }
  ```
- **Response**: Email ID and status
- **Email Tracking**: Open/click tracking enabled

#### **POST /send-whatsapp**
- **Purpose**: Send WhatsApp message
- **Method**: POST
- **Request Body**:
  ```json
  {
    "lead_id": 100,
    "phone_number": "+91-9876543210",
    "message": "Your demo is scheduled for 2024-02-10",
    "template_id": 3,
    "media_url": "https://..."
  }
  ```
- **Response**: Message ID and delivery status

#### **POST /schedule-followup**
- **Purpose**: Schedule follow-up for lead
- **Method**: POST
- **Request Body**:
  ```json
  {
    "lead_id": 100,
    "followup_date": "2024-02-10T14:00:00Z",
    "description": "Call to discuss proposal",
    "assigned_to": 5
  }
  ```
- **Response**: Followup ID and confirmation

#### **GET /followup/due**
- **Purpose**: Get due follow-ups for current user
- **Method**: GET
- **Response**: List of overdue and upcoming follow-ups

---

### **6. Activity Tracking Endpoints**

#### **GET /activity/user-activity**
- **Purpose**: Get user activity statistics
- **Method**: GET
- **Query Parameters**:
  ```
  ?user_id=5&from_date=2024-01-01&to_date=2024-02-05&company_id=15
  ```
- **Response**:
  ```json
  {
    "logins": 22,
    "calls": 45,
    "emails": 60,
    "followups": 30,
    "daily_breakdown": [...]
  }
  ```

#### **GET /activity/company-activity**
- **Purpose**: Get company-wide activity
- **Method**: GET
- **Response**: Aggregated company metrics

#### **GET /activity/login-now**
- **Purpose**: Get currently active users
- **Method**: GET
- **Response**: List of active users with last activity time

---

### **7. File Management Endpoints**

#### **POST /files/upload**
- **Purpose**: Upload file to S3
- **Method**: POST (multipart/form-data)
- **Parameters**:
  ```
  file: <binary file content>
  path: "leads/lead_123/"
  type: "proposal|contract|other"
  ```
- **Response**:
  ```json
  {
    "file_id": "abc123",
    "file_url": "https://s3.../abc123",
    "size": 1024000,
    "uploaded_on": "2024-02-05T10:00:00Z"
  }
  ```

#### **GET /files/:file_id**
- **Purpose**: Download file
- **Method**: GET
- **Response**: File content with appropriate headers

#### **DELETE /files/:file_id**
- **Purpose**: Delete file from S3
- **Method**: DELETE
- **Response**: 200 OK

---

### **8. Folder Management Endpoints**

#### **GET /folders** and **GET /folders/<path>**
- **Purpose**: List files and folders in directory
- **Method**: GET
- **Response**:
  ```json
  {
    "status": 200,
    "data": [
      {
        "folderfilename": "proposal.pdf",
        "size": "2.5 MB",
        "created_date": "2024-02-05 10:00:00",
        "last_modified": "2024-02-05 10:00:00",
        "download": 1
      }
    ]
  }
  ```

#### **POST /folders/**
- **Purpose**: Create new folder
- **Method**: POST
- **Request Body**:
  ```json
  {
    "path": "smit",
    "foldername": "new_folder"
  }
  ```

#### **DELETE /folders/**
- **Purpose**: Delete folder or file
- **Method**: DELETE
- **Request Body**:
  ```json
  {
    "path": "smit",
    "folder_file": "item_to_delete",
    "comment": "No longer needed"
  }
  ```

---

### **9. Integration Endpoints**

#### **POST /integration/sync**
- **Purpose**: Trigger ERP data synchronization
- **Method**: POST
- **Request Body**:
  ```json
  {
    "company_id": 15,
    "erp_type": "SAP",
    "sync_type": "full|incremental"
  }
  ```
- **Response**: Sync job ID and status

#### **GET /integration/sync-status/:sync_id**
- **Purpose**: Get sync job status
- **Method**: GET
- **Response**:
  ```json
  {
    "sync_id": "sync_abc123",
    "status": "in_progress|completed|failed",
    "progress": 65,
    "records_processed": 6500,
    "errors": 2,
    "error_details": [...]
  }
  ```

#### **POST /webhook/receive**
- **Purpose**: Receive webhook from external system
- **Method**: POST
- **Request Body**: System-specific format
- **Response**: 200 OK with processing confirmation
- **Authentication**: Webhook signature validation

#### **GET /integration/settings**
- **Purpose**: Get company integration settings
- **Method**: GET
- **Response**: Integration configuration for company

---

### **10. User & Company Management Endpoints**

#### **GET /users**
- **Purpose**: List users in company
- **Method**: GET
- **Query Parameters**: `?company_id=15&role=SALESPERSON&active=1`
- **Access**: ADMIN, MANAGER only

#### **POST /users**
- **Purpose**: Create new user
- **Method**: POST
- **Request Body**:
  ```json
  {
    "username": "john.smith",
    "email": "john.smith@example.com",
    "company_id": 15,
    "role_id": 3,
    "password": "encrypted_password"
  }
  ```
- **Access**: ADMIN only

#### **PUT /users/:user_id**
- **Purpose**: Update user information
- **Method**: PUT
- **Access**: ADMIN, or user updating self

#### **DELETE /users/:user_id**
- **Purpose**: Deactivate user
- **Method**: DELETE
- **Access**: ADMIN only

#### **GET /companies**
- **Purpose**: List companies (admin view)
- **Method**: GET
- **Access**: ADMIN only

#### **POST /companies**
- **Purpose**: Create new company
- **Method**: POST
- **Request Body**:
  ```json
  {
    "company_name": "XYZ Corp",
    "gst_number": "18AABCU1234F1Z0",
    "website": "https://xyzcorp.com",
    "industry": "IT"
  }
  ```
- **Access**: ADMIN only

---

### **11. Ticket Management Endpoints**

#### **GET /tickets**
- **Purpose**: List support tickets
- **Method**: GET
- **Query Parameters**: `?status=open&priority=high&assigned_to=5`

#### **POST /tickets**
- **Purpose**: Create support ticket
- **Method**: POST
- **Request Body**:
  ```json
  {
    "title": "Data Sync Failed",
    "description": "SAP sync failing for Company 15",
    "ticket_type": "Sync",
    "priority": "High",
    "company_id": 15
  }
  ```

#### **PUT /tickets/:ticket_id**
- **Purpose**: Update ticket
- **Method**: PUT
- **Request Body**: Partial ticket object

#### **POST /tickets/:ticket_id/comments**
- **Purpose**: Add comment to ticket
- **Method**: POST
- **Request Body**:
  ```json
  {
    "comment_text": "Investigated the issue...",
    "attachment_url": "https://..."
  }
  ```

---

## API Standards

### **Pagination**
```
Query Parameters: ?page=1&limit=20
Response Headers:
  X-Total-Count: 1000
  X-Page-Count: 50
Response Body:
  "page": 1,
  "limit": 20,
  "total": 1000
```

### **Filtering**
```
Standard Filters: status, company_id, user_id, created_from, created_to
Advanced Filters: Use filter parameter with JSON
?filter={"status":"Demo","company_id":15}
```

### **Sorting**
```
?sort=created_on:desc,name:asc
```

### **Rate Limiting**
```
Default: 1000 requests per minute per API key
Headers:
  X-RateLimit-Limit: 1000
  X-RateLimit-Remaining: 999
  X-RateLimit-Reset: 1707118800
```

### **Versioning**
```
API Version in URL: /api/v1, /api/v2
Backward Compatibility: Maintained across versions
Deprecation: 6 months notice for changes
```

---

## Error Codes

| Status | Code | Meaning |
|--------|------|---------|
| 200 | OK | Request successful |
| 201 | Created | Resource created |
| 204 | No Content | Success, no response body |
| 400 | Bad Request | Invalid request parameters |
| 401 | Unauthorized | Missing/invalid authentication |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource already exists |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server error |
| 503 | Service Unavailable | Service temporarily down |

---

## API Documentation & SDKs

- **OpenAPI Spec**: `/api/v1/openapi.json`
- **Swagger UI**: `/api/v1/docs`
- **SDKs Available**:
  - Python: `pip install iperform-sdk`
  - JavaScript: `npm install iperform-sdk`
  - Go: `go get github.com/iperform/sdk-go`

---

## Summary

iPerform's REST API provides:
- ✅ Comprehensive endpoint coverage (50+ endpoints)
- ✅ Standard REST conventions
- ✅ Secure JWT authentication
- ✅ Role-based access control
- ✅ Detailed error handling
- ✅ Extensive pagination and filtering
- ✅ Real-time webhooks
- ✅ Complete OpenAPI documentation
