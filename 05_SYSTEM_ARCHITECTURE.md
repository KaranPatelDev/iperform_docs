# System Architecture

## Architecture Overview

iPerform is built on a modern, scalable, multi-tiered architecture designed for enterprise deployments. The system separates concerns across multiple layers while maintaining tight integration through well-defined interfaces.

---

## High-Level Architecture Diagram

```
                           │ HTTPS/HTTP (REST API)
                           │ (from API Clients)
┌──────────────────────────▼──────────────────────────────────────┐
│              API Gateway & Load Balancer                        │
│            (Optional: NGINX, Cloud Load Balancer)              │
└──────────────────────────┬──────────────────────────────────────┘
                           │ REST API
┌──────────────────────────▼──────────────────────────────────────┐
│              Application Server Layer                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Flask Application (Gunicorn WSGI Server)               │  │
│  │  ├─ Authentication Service (JWT, Session)              │  │
│  │  ├─ Lead Management Service                            │  │
│  │  ├─ Reporting Service                                  │  │
│  │  ├─ Integration Service (ERP, Webhook)                 │  │
│  │  ├─ File Management Service                            │  │
│  │  ├─ Communication Service (Email, WhatsApp)            │  │
│  │  └─ Admin Service (User, Company, Settings)            │  │
│  └──────────────────────────────────────────────────────────┘  │
└──────────────────────────┬──────────────────────────────────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
┌───────▼─────┐  ┌────────▼────────┐  ┌──────▼──────┐
│  MySQL      │  │  ClickHouse     │  │  AWS S3     │
│  (Primary)  │  │  (Analytics)    │  │  (Files)    │
└─────────────┘  └─────────────────┘  └─────────────┘
```

---

## Architectural Layers

### **1. API Layer (REST API)**

**Technology**: Flask 2.2.2, RESTful design patterns

**Components**:

#### **Core API Endpoints** (50+ endpoints organized by feature)

```
Authentication
├── POST /i_auth                           (iPerform login)
├── POST /set_password                     (Set user password)
├── POST /reset_password                   (Reset password)
├── POST /logout                           (Logout user)
├── POST /forget_password_mail             (Send password reset email)
├── POST /get_user_data                    (Get authenticated user data)
├── POST /user_session_data                (Get user session data)
├── POST /set_session                      (Set session data)
├── POST /login_history                    (Get login history)
├── POST /check_token                      (Validate JWT token)
└── POST /generate-jwt-token               (Generate JWT token)

Leads Management
├── POST /leads/view                       (List/filter leads)
├── POST /leads/add                        (Create new lead)
├── POST /leads/update                     (Update lead)
├── POST /leads/edit_data                  (Edit lead data)
├── POST /get_lead_info                    (Get single lead details)
├── POST /get_warrior_info                 (Get warrior information)
├── POST /leads/add-remarks                (Add lead remarks/notes)
├── POST /leads/delete-remark              (Delete remark)
├── POST /leads/get-logs-data              (Get lead activity logs)
├── POST /leads/share-lead                 (Share lead with users)
├── POST /leads/reassign-lead              (Reassign lead)
├── POST /leads/info                       (Get lead comprehensive info)
├── POST /leads/get-sharelead-data         (Get shared lead data)
├── POST /leads/delete-facebook-lead       (Delete Facebook lead)
├── POST /lead-favourite                   (Mark lead as favourite)
├── POST /proposal_data                    (Get proposal data)
├── POST /update_proposal_data             (Update proposal data)
├── POST /get_reminders_data               (Get reminders)
├── POST /integration/update_data          (Update integration data)
└── POST /integration/get_data             (Get integration data)

Reports
├── POST /get-mirror-view-data             (Mirror view report)
├── POST /get-caller-data                  (Caller report)
├── POST /get-salesperson-report-count     (Salesperson report count)
├── POST /get-salesperson-report-data      (Salesperson report data)
├── POST /get-followup-activity-report-count (Follow-up activity count)
└── POST /get-focus-customer-data          (Focus customer report)

Communication
├── POST /send-mail                        (Send email)
├── POST /whatsapp_send_msg                (Send WhatsApp message)
├── POST /whatsapp_get_msg                 (Get WhatsApp messages)
├── POST /whatsapp_get_templates           (Get WhatsApp templates)
├── POST /whatsapp_add_templates           (Add WhatsApp template)
├── POST /whatsapp_update_templates        (Update WhatsApp template)
├── POST /whatsapp_delete_templates        (Delete WhatsApp template)
├── POST /check_user_associated            (Check WhatsApp association)
├── POST /generate_qr_code                 (Generate WhatsApp QR)
├── POST /whatsapp_logout                  (WhatsApp logout)
└── POST /whatsapp/add                     (Add WhatsApp configuration)

File Management
├── POST /file_management/upload           (Upload file to S3)
├── GET  /file_management/view_file/<folder>/<filename> (View file)
├── GET  /file_management/folders/<path>   (List folders/files)
├── POST /file_management/folders/<path>   (Create folder)
├── DELETE /file_management/folders/       (Delete folder)
├── GET  /file_management/file/download/<path> (Download file)
├── POST /file_management/file/upload/     (Upload file)
├── POST /file_management/rename           (Rename file/folder)
├── GET  /file_management/files/<path>     (Get files in folder)
└── DELETE /file_management/files/         (Delete file)

Activity Tracking
├── POST /followup_activity/add            (Add follow-up activity)
├── POST /followup_activity/update         (Update activity)
├── POST /followup_activity/view           (View activities)
└── POST /activity_report                  (Activity report)

Integration & Sync
├── POST /fb_api_data_fetch                (Fetch Facebook leads)
├── POST /pc_integration                   (Partner company integration)
├── POST /sync_details                     (Get sync details)
└── POST /extractor                        (ERP data extraction)

Ticketing System
├── POST /ticketing_system/create          (Create ticket)
├── POST /ticketing_system/update          (Update ticket)
├── POST /ticketing_system/view            (View tickets)
└── POST /ticketing_system/comment         (Add comment)

Others
├── POST /global_search                    (Global search)
├── POST /send_notification                (Send notification)
├── POST /custom_reports                   (Custom reports)
├── POST /openai                           (OpenAI integration)
└── POST /todo                             (Todo management)
```

#### **API Features**:
- **RESTful Design**: Standard HTTP methods (GET, POST, PUT, DELETE)
- **JSON Request/Response**: Standard JSON data format
- **Authentication**: JWT Bearer token authentication
- **Rate Limiting**: Prevent API abuse
- **Versioning**: Support multiple API versions
- **Error Handling**: Standardized error responses

---

### **2. Business Logic Layer**

**Technology**: Python, SQLAlchemy ORM

**Components**:

#### **Service Classes**
```
auth_service.py
├── authenticate_user()
├── generate_jwt_token()
├── validate_token()
└── refresh_token()

lead_service.py
├── create_lead()
├── update_lead()
├── assign_lead()
├── get_lead_pipeline()
└── perform_bulk_actions()

reporting_service.py
├── generate_report()
├── execute_custom_query()
├── schedule_report()
└── get_analytics()

integration_service.py
├── sync_erp_data()
├── transform_data()
├── handle_errors()
└── trigger_webhooks()

communication_service.py
├── send_email()
├── send_whatsapp()
├── track_delivery()
└── log_communication()
```

#### **Helper Functions**
```
helper.py
├── get_user_data()        (Get authenticated user info)
├── get_user_info()        (Get user details from Magenta)
├── get_role_wise_condition() (Build SQL conditions by role)
├── validate_request()     (Validate incoming request)
├── dynamic_db()           (Get company-specific DB)
├── clickhouse_db()        (Get ClickHouse connection)
└── Access to ORM objects by schema
```

#### **Query Module**
```
query.py
├── Pre-built SQL queries
├── Report generation queries
├── Dynamic query construction
├── Query optimization
└── Query templating
```

#### **Decorators**
```
decorator.py
├── @validate_request()    (Validate request)
├── @logs_this()          (Log API call)
├── @time_it()            (Measure execution time)
└── @role_required()      (Check user role)
```

---

### **4. Data Access Layer (DAL)**

**Technology**: SQLAlchemy ORM, Raw SQL for complex queries

**Components**:

#### **ORM Models** (SQLAlchemy)
```
Models for iPerform Database:
├── User
├── Role
├── Salesperson
├── Warrior
├── Leads
├── Lead_Status
├── Lead_Logs
├── Followup_Activity
├── Tickets
├── Session
├── Whatsapp_Messages
└── 30+ more models

Models for Magenta Database:
├── Company
├── User (Client)
├── User_Info
├── Pricing_Plan
├── Dynamic_DB
├── Email_Template
└── User_Setting
```

#### **Database Connections**
```python
# Primary MySQL Connection
SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://user:pass@host:3306/iperform'

# Client Magenta Connection  
CLIENT_DB = 'mysql+pymysql://user:pass@host:3306/magenta'

# ClickHouse Analytics
CLICKHOUSE = 'clickhouse://user:pass@host:8123/database'
```

#### **Query Execution**
- ORM queries: Use SQLAlchemy for standard operations
- Complex queries: Raw SQL via db.session.execute()
- Stored procedures: Call database procedures when needed
- Transaction management: Explicit session commits

---

### **4. Database Layer**

#### **Database Architecture**

```
┌─────────────────────────────────────────────────┐
│         Multi-Database Architecture              │
└─────────────────────────────────────────────────┘

iPerform Database (MySQL)          Magenta Database (MySQL)
├─ user                            ├─ company
├─ role                            ├─ user
├─ salesperson                     ├─ user_info
├─ warrior                         ├─ pricing_plan
├─ leads                           ├─ dynamic_db
├─ lead_status                     ├─ email_template
├─ lead_logs                       ├─ user_setting
├─ followup_activity               ├─ view_setting
├─ tickets                         ├─ company_setting
├─ whatsapp_messages               └─ (Client-specific)
├─ iperform_mail
├─ session
├─ all_session
└─ ... (40+ tables)

ClickHouse Analytics               Customer ERP Database
├─ iperform_helper                (Company-specific)
│  ├─ leads                        ├─ transactions
│  ├─ lead_logs                    ├─ customers
│  ├─ leaddetails2                 ├─ invoices
│  └─ lead_status                  ├─ purchase_orders
├─ extractor                       └─ ... (custom)
│  ├─ credentials
│  ├─ subcompany_settings
│  ├─ reportwise_settings
│  ├─ sync_history
│  └─ data_update
└─ company_schemas (h_001, h_002, ...)
```

#### **Database Features**
- **Connection Pooling**: SQLAlchemy connection pool (size=700, timeout=300s)
- **Connection Recycling**: Recycle connections every 280s
- **Pre-ping**: Verify connection health before use
- **Multi-tenant**: Complete isolation between companies
- **Replication**: Master-slave setup optional
- **Backup**: Automated daily backups

---

### **6. External Services Integration**

#### **ERP Systems**
```
Extractor Service
├─ Data Extraction
│  ├─ Query customer data
│  ├─ Query transaction data
│  └─ Query invoice data
├─ Transformation
│  ├─ Map fields
│  ├─ Clean data
│  └─ Validate data
└─ Loading
   ├─ Insert into iPerform
   ├─ Update existing records
   └─ Log sync history
```

#### **Communication Services**
```
Email Service (AWS SES or SMTP)
├─ SMTP: Office 365
├─ SES: AWS Simple Email Service
└─ Mail Templates

WhatsApp Integration
├─ WhatsApp API
├─ Message Queue
└─ Delivery Tracking

Notification Service
├─ In-app notifications
├─ Email notifications
└─ SMS notifications (optional)
```

#### **Cloud Storage**
```
AWS S3
├─ File Upload
├─ File Storage
├─ File Retrieval
└─ File Cleanup
```

---

## Technology Stack Details

### **Backend**

| Component | Technology | Version | Purpose |
|-----------|-----------|---------|---------|
| Framework | Flask | 2.2.2 | Web framework |
| WSGI Server | Gunicorn | 20.1.0 | Application server |
| ORM | SQLAlchemy | 3.0.2 | Database abstraction |
| Database Driver | PyMySQL | - | MySQL connection |
| Database Driver | Psycopg2 | 2.8.6 | PostgreSQL connection |
| Database Driver | PyHive | 0.6.1 | Hive/Hadoop connection |
| Data Processing | Pandas | 1.1.5 | Data manipulation |
| Data Processing | NumPy | 1.22.4 | Numerical computing |
| Authentication | PyJWT | - | JWT tokens |
| Encryption | PyCryptodome | 3.15.0 | Cryptography |
| HTTP Client | Requests | - | HTTP requests |
| Async | Gevent | 23.7.0 | Async I/O |
| Async Websocket | gevent-websocket | 0.10.1 | Websocket support |
| Validation | Marshmallow | 3.16.0 | Data validation |
| CORS | Flask-Cors | 3.0.10 | CORS support |
| Session | Flask-Session | 0.3.1 | Session management |
| Caching | Flask-Cache | 0.13.1 | Caching layer |
| PDF Generation | pdfkit | 1.0.0 | PDF reports |
| AWS SDK | Boto3 | 1.24.71 | AWS S3, SES |
| Environment | python-dotenv | - | Configuration |

### **Databases**
- **MySQL**: 5.7+ for transactional data
- **ClickHouse**: 20.x+ for analytics
- **PostgreSQL**: Optional alternative

### **DevOps**
- **Containerization**: Docker, Docker Compose
- **Orchestration**: Kubernetes (optional)
- **CI/CD**: GitLab CI, GitHub Actions
- **Monitoring**: Prometheus, Grafana (optional)

---

## Request/Response Flow

### **Typical API Request Flow**

```
1. Client sends HTTP request to API endpoint
   ├─ Header: Authorization: Bearer <jwt_token>
   ├─ Method: GET/POST/PUT/DELETE
   ├─ Body: JSON payload (if applicable)
   └─ URL: /api/endpoint

2. API Gateway/Load Balancer routes to Flask app

3. Flask Application
   ├─ Route matching via @app.route()
   ├─ Request validation via @validate_request()
   ├─ Token verification
   ├─ User authentication via get_user_data()
   └─ Role-based access control

4. Business Logic Layer
   ├─ Service function execution
   ├─ Data validation
   ├─ Complex calculations
   └─ Business rule enforcement

5. Data Access Layer
   ├─ ORM query construction
   ├─ Raw SQL execution (if needed)
   ├─ Transaction management
   └─ Error handling

6. Database Query Execution
   ├─ Connection from pool
   ├─ Query execution
   ├─ Result fetching
   └─ Connection return to pool

7. Response Construction
   ├─ Data serialization (dict to JSON)
   ├─ Response formatting
   ├─ Status code assignment
   └─ Header setup

8. Response Sent to Client
   ├─ HTTP Status Code (200, 201, 400, 500, etc.)
   ├─ JSON Response Body
   ├─ Content-Type: application/json
   └─ CORS headers (if applicable)

9. Logging
   ├─ @logs_this decorator logs the request
   ├─ Log includes: user, endpoint, timestamp, duration
   └─ Stored in iperform.logs table
```

### **Example Request: Get Leads**
```
REQUEST:
GET /leads?status=Demo&salesperson_id=5
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
Accept: application/json

FLOW:
1. Validate JWT token
2. Extract user_id from token
3. Call get_user_data(token) → Get user role and view_list
4. Build SQL where clause based on role:
   - ADMIN: No filter
   - MANAGER: manager_id = X
   - SALESPERSON: salesperson.id = X OR shared_salesperson = X
   - WARRIOR: warrior.id = X
5. Apply status filter (status = 'Demo')
6. Apply salesperson filter (salesperson_id = 5)
7. Execute query against ClickHouse or MySQL
8. Format results as JSON array
9. Return 200 OK with leads data

RESPONSE:
{
  "status": 200,
  "data": [
    {
      "id": 123,
      "firm_name": "ABC Corp",
      "status": "Demo",
      "lead_status": "Active",
      "salesperson_id": 5,
      ...
    }
  ],
  "count": 25,
  "total": 100
}
```

---

## Data Flow Architecture

### **Lead Ingestion Pipeline**
```
Multiple Sources
├─ Manual Entry (Web Form)
├─ Facebook Lead Ads API
├─ CSV/Excel Upload
├─ REST API Post
└─ Webhook Inbound

         ↓
    Data Validation
    (Email, Phone, Format)
         ↓
    Duplicate Detection
    (Check existing leads)
         ↓
    Enrichment
    (Lookup company info, ERP data)
         ↓
    iPerform Database
    (Insert into leads table)
         ↓
    ClickHouse Mirror
    (Async insert for analytics)
         ↓
    Notifications
    (Email to assigned salesperson)
         ↓
    Event Webhooks
    (Trigger external integrations)
```

### **Reporting Pipeline**
```
Raw Data
├─ iPerform Database
├─ ClickHouse (Analytics)
└─ Customer ERP Database

         ↓
    Data Aggregation
    (Group, Sum, Count)
         ↓
    Metrics Calculation
    (Conversion rate, pipeline value)
         ↓
    Caching Layer
    (Cache results for 30 mins)
         ↓
    Report Formatting
    (JSON, CSV, PDF)
         ↓
    User Dashboard
         ↓
    Email Distribution
    (Schedule reports)
```

---

## Scalability & Performance

### **Horizontal Scaling**
- **Stateless Application**: Multiple Flask instances behind load balancer
- **Session Storage**: Use external session store (Redis optional)
- **Connection Pooling**: Each instance maintains own pool
- **Database Sharding**: Optional sharding by company_id

### **Vertical Scaling**
- **Increase Server Resources**: CPU, Memory, Disk
- **Database Optimization**: Indexing, query optimization
- **Caching**: Redis for session and result caching
- **Async Processing**: Use Gevent for I/O-bound tasks

### **Performance Metrics**
- **Response Time**: < 500ms for 95th percentile
- **Concurrent Users**: 500+ with proper resources
- **Database Queries**: Optimized with indexes
- **API Rate Limit**: 1000 req/min per token

---

## Security Architecture

### **Authentication Flow**
```
User Input (Username/Password)
         ↓
    Hash & Compare with iperform.user table
         ↓
    Password Match?
    ├─ Yes: Generate JWT token
    └─ No: Return 401 Unauthorized
         ↓
    Token Contains:
    ├─ user_id
    ├─ email
    ├─ role_id
    ├─ exp (expiration)
    └─ iat (issued at)
         ↓
    Create Session Record in iperform.all_session
    (Track: user_id, token, device_info, expiry)
         ↓
    Return JWT to Client
         ↓
    Client Stores Token (localStorage/sessionStorage)
         ↓
    Client Sends Token in Every Request
    (Authorization: Bearer <token>)
         ↓
    Server Validates Token
    ├─ Signature valid?
    ├─ Not expired?
    ├─ Session exists?
    └─ User active?
```

### **Authorization**
- Role-based filters applied to all queries
- Field-level access control
- IP-based restrictions (optional)
- Time-based access elevation

### **Data Protection**
- SQL Injection prevention: Parameterized queries
- XSS prevention: Input sanitization
- CSRF prevention: Token validation
- Password hashing: bcrypt or Argon2

---

## Deployment Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Production Environment                    │
└─────────────────────────────────────────────────────────────┘

Docker Container
├─ FROM python:3.9
├─ COPY requirements.txt
├─ RUN pip install -r requirements.txt
├─ COPY application code
├─ EXPOSE 5000 (Flask)
├─ CMD ["gunicorn", "-w 4", "-b 0.0.0.0:5000", "wsgi.py"]
└─ Health Check: /health endpoint

Docker Compose (Multi-container)
├─ Flask Application Service
│  ├─ Image: iperform:latest
│  ├─ Ports: 5000
│  ├─ Depends on: MySQL, ClickHouse
│  └─ Volumes: /home/iperform/main
├─ MySQL Service
│  ├─ Image: mysql:5.7
│  ├─ Ports: 3306
│  └─ Volumes: /var/lib/mysql
├─ ClickHouse Service (Optional)
│  ├─ Image: clickhouse/clickhouse-server
│  ├─ Ports: 8123
│  └─ Volumes: /var/lib/clickhouse
└─ Nginx Reverse Proxy (Optional)
   ├─ Image: nginx:latest
   ├─ Ports: 80, 443
   └─ Config: nginx.conf

Environment Variables
├─ DB_HOST, DB_USER, DB_PASSWORD
├─ CLICKHOUSE_DB_HOST, CLICKHOUSE_DB_USER
├─ AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY
├─ MAIL_SERVER, MAIL_USERNAME, MAIL_PASSWORD
├─ JWT_SECRET_KEY
└─ IPERFORM_ENV (dev/test/pro)
```

---

## Summary

iPerform's architecture provides:
- ✅ Scalability for enterprise deployments
- ✅ Security through multi-layered authentication & authorization
- ✅ Performance through optimized databases and caching
- ✅ Flexibility through multi-tenant design
- ✅ Maintainability through clean separation of concerns
- ✅ Integration through comprehensive REST API
- ✅ Reliability through containerization and deployment best practices
