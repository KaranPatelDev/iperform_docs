# iPerform - Project Overview

## Executive Summary

**iPerform** is a comprehensive **Lead Management and CRM Integration Platform** designed to help enterprises track, manage, and convert leads into customers. It provides real-time visibility into sales pipelines, lead activities, and performance metrics across multiple roles (Salespeople, Warriors, Partners, and Managers) with role-based access control and multi-database support.

---

## What is iPerform?

iPerform is a Flask-based web application that serves as a centralized hub for:
- **Lead Tracking**: Monitor leads across different stages (Demo, Proposal, Trial, Won, Lost)
- **Activity Management**: Track follow-ups, communications, and interactions
- **Multi-Company Support**: Manage multiple companies with isolated databases
- **ERP Integration**: Sync data with various ERP systems (SAP, Tally, Zoho, etc.)
- **Data Extraction**: Extract and transform data from external sources via the Extractor service
- **Real-time Reporting**: Generate activity reports, salesperson performance metrics, and dashboards

---

## Key Characteristics

| Aspect | Details |
|--------|---------|
| **Architecture** | Flask REST API with multi-database support |
| **Primary Database** | MySQL (iPerform core) + Magenta (Client management) |
| **Analytics Database** | ClickHouse (High-performance analytics) |
| **Authentication** | Token-based JWT authentication |
| **Deployment** | Docker containerization (Docker Compose) |
| **Scalability** | Connection pooling, multi-tenant architecture |
| **Data Storage** | AWS S3 for file management, MySQL for transactional data |

---

## System Components

### 1. **Core Services**
- **Authentication Service** (`api/login.py`, `api/extractor.py`)
  - User login via iPerform or Extractor credentials
  - JWT token generation
  - Session management

- **Lead Management Service** (`api/leads.py`)
  - Create, update, retrieve lead information
  - Lead status transitions
  - Lead assignment to salespeople/warriors

- **Reporting Service** (`api/report.py`, `api/activity_report.py`)
  - Salesperson performance reports
  - Activity metrics (logins, follow-ups, calls)
  - Lead funnel analysis

### 2. **Supporting Services**
- **File Management** (`api/file_management.py`)
  - Upload/download files
  - Folder management

- **Email & Notification** (`api/send_mail.py`, `api/template_mail.py`, `api/send_notification.py`)
  - Send templated emails
  - WhatsApp messages
  - Email tracking

- **Integration Services** (`api/fb_api_data_fetch.py`, `api/pc_integration.py`)
  - Facebook Lead Ads integration
  - Partner Company ERP sync
  - Data extraction and transformation

---

## Database Architecture

iPerform operates with **three distinct database clusters**:

### **iPerform Database** (MySQL)
Stores core operational data:
- User management (`user`, `role`, `salesperson`, `warrior`)
- Lead information (`leads`, `lead_status`, `lead_logs`)
- Session and authentication (`session`, `session_info`, `all_session`)
- Communications (`whatsapp_messages`, `followup_activity`)
- Support tickets (`tickets`, `ticket_communication`)

### **Magenta Database** (MySQL - Client Side)
Stores client company information:
- Company settings
- User accounts (per company)
- Dynamic database configurations
- Email templates

### **ClickHouse Cluster** (Analytics)
Stores analytical and historical data:
- `iperform_helper`: Lead master data
- `extractor`: Data extraction logs and settings
- Company-specific schemas: Customer/transaction data
- High-volume log data (WhatsApp, activity logs)

---

## User Roles & Permissions

iPerform supports **7 primary user roles** with hierarchical access:

| Role | Access Level | Views Available | Key Functions |
|------|--------------|-----------------|----------------|
| **ADMIN** | Full | All leads, Salespeople, Warriors, Partners, Reports | System configuration, user management |
| **MANAGER** | Team | Team's leads, assigned salespeople, reports | Team performance tracking, lead assignment |
| **SALESPERSON** | Team+Own | Own leads, co-salesperson leads, warriors | Lead management, activity tracking |
| **WARRIOR** | Assigned | Assigned leads only | Lead follow-ups, customer interaction |
| **COSALES** | Shared | Shared leads, co-salesperson leads | Collaborative selling |
| **CUSTOMER_SUCCESS** | Assigned | Support-related leads, tickets | Customer support, issue resolution |
| **INTEGRATION** | Data | Lead data only (no UI) | External integrations, webhooks |
| **DEV** | Full (API Only) | All data for development | Testing, debugging |
| **GUEST** | Read-Only | Leads, partners (no reports) | View-only access |

---

## Key Features

### **Lead Management**
- Create and track leads from multiple sources (Facebook Ads, manual entry, API)
- Lead qualification and status transitions
- Assign leads to salespeople with hierarchical rules
- Lead sharing between salespeople
- Lead deletion with audit trail

### **Activity Tracking**
- Login activity monitoring
- Follow-up scheduling and tracking
- Communication history (emails, WhatsApp, calls)
- Activity reports with time-based filtering

### **Multi-Company Support**
- Isolated data per company
- Dynamic database assignment
- Company-wise lead synchronization
- Per-company extractor settings

### **Reporting & Analytics**
- Real-time activity reports
- Salesperson performance metrics
- Lead funnel analysis (Demo → Won/Lost)
- Partner company contribution reports
- Warrior performance tracking
- ClickHouse-powered analytics

### **ERP Integration**
- Support for 10+ ERP systems
- Data extraction and transformation
- Real-time synchronization
- Custom field mapping
- Integration webhooks

### **Communication Management**
- Email templating and tracking
- WhatsApp message management
- Mail notifications
- Reminder system for follow-ups

---

## Authentication System

iPerform implements a comprehensive authentication and password management system with multiple endpoints and security features. The authentication module handles user login, session management, and password recovery.

### **1. iPerform Authentication (i_auth)**

**Endpoint**: `POST /i_auth`

**Purpose**: Authenticate users and generate JWT tokens for API access

**Flow**:
```
1. Client sends login request with encrypted password
   ├─ Request Parameters: username/email, password, platform, public_ip
   └─ Authentication Method: Credentials checked against User table

2. Server validates credentials
   ├─ Decrypt password (using PyCryptodome)
   ├─ Compare with stored hash (using Werkzeug)
   └─ Check if user is active

3. Session management
   ├─ Generate unique session ID (UUID + timestamp)
   ├─ Validate session limit per platform
   └─ Create All_Session record

4. Token generation
   ├─ Method 1: Encrypted session ID (default)
   ├─ Method 2: JWT token (if authflow='jwt' parameter)
   └─ Return encrypted token to client

5. Response
   ├─ Status: 200 (success) or 505 (session suspended)
   ├─ Return encrypted token/JWT token
   └─ Store session details (platform, IP, OS, device)
```

**Libraries Used**:
- **Werkzeug** (2.2.2): Password hashing and verification
- **PyJWT** (2.10.1): JWT token generation and validation
- **PyCryptodome** (3.15.0): Asymmetric encryption/decryption
- **uuid**: Session ID generation

---

### **2. Forgot Password (forget_password_mail)**

**Endpoint**: `POST /forget_password_mail`

**Purpose**: Send password reset link via email to user's registered email address

**Flow**:
```
1. Client requests password reset
   ├─ Request Parameter: email address
   └─ Validation: Email format check using regex

2. Verify email exists in system
   ├─ Query User table for email
   ├─ If found: Generate reset token
   └─ If not found: Return error (email not registered)

3. Generate and store reset token
   ├─ Token generation: UUID (hex format)
   ├─ Database update: Store token in magenta.user.forgot_password_token
   └─ Token validity: Remains valid until password reset

4. Fetch email template
   ├─ Query magenta.email_template
   ├─ Template slug: 'new_custom_user'
   ├─ Extract: subject, body_text, sender_name
   └─ Context variables: url, user_name, company, etc.

5. Send email via AWS SES
   ├─ Library: Boto3 (AWS SDK)
   ├─ Email content: Jinja2 templating (variable substitution)
   ├─ Recipients: User's email address
   └─ Subject: Template subject
   
6. Response
   ├─ Status: 200 (success)
   ├─ Data: URL format and mail name
   └─ Message: Confirmation email sent

7. User clicks reset link
   ├─ Frontend redirects to: /reset-password-both/{forgot_gen_token}
   ├─ User enters new password
   └─ Frontend sends to /reset_password endpoint
```

**Libraries Used**:
- **Boto3** (1.24.71): AWS SES service for sending emails
- **botocore** (1.27.71): AWS core functionality
- **Jinja2** (3.1.1): Email template rendering with variable substitution
- **re (regex)**: Email format validation
- **uuid**: Unique token generation

---

### **3. Reset Password (reset_password)**

**Endpoint**: `POST /reset_password`

**Purpose**: Update user password using the reset token received via forget_password_mail

**Flow**:
```
1. Client submits new password
   ├─ Request Parameters: __token (forgot_password_token), is_both (0/1), passwords
   ├─ Validation: Check if token exists and not empty
   └─ is_both flag: 0 = different passwords for BI & iPerform, 1 = same password for both

2. Verify reset token validity
   ├─ Query magenta.user table WHERE forgot_password_token = received_token
   ├─ Retrieve user_id and validate token exists
   ├─ If invalid: Return "Unauthorized"
   └─ If valid: Proceed with password reset

3. Decrypt incoming passwords
   ├─ Decrypt password (using PyCryptodome)
   ├─ Decode from bytes to UTF-8 string
   └─ Use decrypted password for hashing

4. Hash and update password
   ├─ Generate password hash (Werkzeug.generate_password_hash)
   ├─ Update iPerform database: User table
   ├─ If is_both=1: Also update Magenta database user table
   └─ Set changed_on timestamp (Asia/Kolkata timezone)

5. Clear all user sessions (logout all sessions)
   ├─ Delete all records from iPerform.all_session
   ├─ If BI password updated: Delete magenta.all_session records
   └─ Purpose: Force re-login with new password for security

6. Clear reset token
   ├─ Update magenta.user SET forgot_password_token = ""
   ├─ Prevents token reuse
   └─ One-time use token

7. Response
   ├─ Status: 200 (success)
   ├─ Message: "Password change successfully"
   └─ User can now login with new password
```

**Libraries Used**:
- **Werkzeug** (2.2.2): Password hashing (generate_password_hash)
- **PyCryptodome** (3.15.0): Decrypt incoming password
- **pytz** (2022.1): Timezone handling (Asia/Kolkata)
- **datetime**: Timestamp management

---

### **4. Set Password (set_password)**

**Endpoint**: `POST /set_password`

**Purpose**: Initial password setting for new users (similar to reset but for first-time setup)

**Parameters**: Same as reset_password (__token, is_both, passwords)

**Key Difference**: 
- Used for new user onboarding
- Can set different passwords for BI and iPerform systems
- Similar flow to reset_password, but intended for first-time setup

---

## Environment Variables for Authentication

```
Email Configuration (in config.py):
- EMAIL_FORMAT: Regex pattern for email validation
- EMAIL_HOST: SMTP server or AWS SES
- EMAIL_PORT: SMTP port (587 for Office 365, 465 for AWS SES)
- EMAIL_USER: Sender email address
- EMAIL_PASSWORD: SMTP password or AWS credentials
- AWS_REGION: AWS region for SES (e.g., us-east-1)
- ENV_DEC: Key for JWT encoding/decoding

URL Configuration:
- URL_OPEN: Base URL for password reset links
- MAGENTA_SCHEMA: Magenta database name
```

---

## Most Frequently Used Libraries

| Library | Version | Category | Purpose |
|---------|---------|----------|---------|
| **Flask** | 2.2.2 | Web Framework | Core REST API framework with routing and request handling |
| **Flask-SQLAlchemy** | 3.0.2 | ORM | Object-relational mapping for database interaction with Flask |
| **SQLAlchemy** | 1.4.46 | ORM | Database abstraction layer and query builder |
| **Werkzeug** | 2.2.2 | Security | Password hashing (generate_password_hash), WSGI utilities, HTTP utils |
| **PyJWT** | 2.10.1 | Authentication | JWT token generation and validation for API auth |
| **PyCryptodome** | 3.15.0 | Security | Encryption/decryption of passwords and sensitive data |
| **Jinja2** | 3.1.1 | Templating | Email template rendering with variable substitution |
| **Gunicorn** | 20.1.0 | Server | WSGI HTTP server for production deployment |
| **Gevent** | 23.7.0 | Async I/O | Lightweight concurrency and async operations |
| **gevent-websocket** | 0.10.1 | WebSocket | Real-time WebSocket communication support |
| **Boto3** | 1.24.71 | AWS SDK | AWS S3 file upload/download, SES email sending |
| **botocore** | 1.27.71 | AWS | Core AWS functionality and service definitions |
| **PyMySQL** | 1.0.2 | DB Driver | Pure Python MySQL client for database connections |
| **mysqlclient** | 1.3.13 | DB Driver | High-performance MySQL adapter |
| **clickhouse-sqlalchemy** | 0.2.4 | DB Driver | SQLAlchemy dialect for ClickHouse analytics queries |
| **Pandas** | 1.1.5 | Data Processing | Data manipulation, filtering, transformation for reports |
| **NumPy** | 1.22.4 | Data Processing | Numerical computing and array operations |
| **python-dotenv** | 0.11.0 | Configuration | Load environment variables from .env file |
| **pytz** | 2022.1 | Timezone | Timezone handling and conversion (Asia/Kolkata) |
| **Flask-Cors** | 3.0.10 | CORS | Cross-Origin Resource Sharing support for APIs |
| **marshmallow** | 3.16.0 | Serialization | Object serialization and validation |
| **Flask-Session** | 0.3.1 | Session | Server-side session storage and management |
| **python-dateutil** | 2.8.2 | DateTime | Extended date/time utilities and parsing |
| **pdfkit** | 1.0.0 | PDF | PDF report generation from HTML |
| **openai** | 1.64.0 | AI/ML | OpenAI API integration for AI features |
| **firebase_admin** | 6.0.1 | Cloud | Firebase push notifications and services |
| **requests** | Latest | HTTP | HTTP requests for external API calls |
| **itsdangerous** | 2.1.2 | Security | Signing/unsigning data for secure token handling |
| **Flask-Cache** | 0.13.1 | Caching | In-memory caching to reduce database queries |
| **decorator** | 4.3.0 | Utilities | Function decorator utilities for custom decorators |

---

## Complete Library Dependencies & Reference

All dependencies with their versions are available in [requirements.txt](../requirements.txt) file with 95+ libraries including development tools and optional integrations.

---

## Data Flow

```
External Source (Facebook, ERP, API)
         ↓
    Extractor Service
         ↓
    iPerform API
         ↓
    [MySQL Database] ← Primary transactions
    [ClickHouse] ← Analytics
         ↓
    User Dashboards
         ↓
    Actions (Email, SMS, WhatsApp)
```

---

## Technology Stack

| Layer | Technology |
|-------|-----------|
| **Framework** | Flask 2.2.2 |
| **Web Server** | Gunicorn |
| **Databases** | MySQL 5.7+, ClickHouse 20.x+ |
| **ORM** | SQLAlchemy 3.0.2 |
| **Authentication** | JWT (PyJWT) |
| **Data Processing** | Pandas, NumPy |
| **File Storage** | AWS S3 (Boto3) |
| **Email** | AWS SES, Office 365 SMTP |
| **Containerization** | Docker & Docker Compose |
| **Async Tasks** | Gevent |

---

## Security Features

- **Authentication**: JWT token-based with expiration tracking
- **Authorization**: Role-based access control (RBAC)
- **Data Encryption**: Password hashing with cryptography library
- **Database Security**: Connection pooling with SSL support
- **API Validation**: Request validation decorator
- **Audit Logging**: Track all data modifications
- **Session Management**: Secure session creation and validation

---

## Deployment Architecture

iPerform is containerized and deployable in multiple environments:

```
Docker Container
├── Flask Application
│   ├── API Routes (40+ endpoints)
│   ├── Model Layer (SQLAlchemy)
│   └── Utility Functions
├── Database Connections
│   ├── MySQL (Primary)
│   ├── MySQL (Magenta - Client)
│   └── ClickHouse (Analytics)
└── External Services
    ├── AWS S3
    ├── AWS SES
    ├── Facebook API
    └── ERP Connectors
```

---

## Integration Points

| System | Purpose | Direction |
|--------|---------|-----------|
| **Facebook API** | Lead Ads integration | Incoming leads |
| **ERP Systems** | Business data sync | Bidirectional |
| **Extractor Service** | Data extraction | Data pull |
| **Partner Company Systems** | Lead distribution | Outgoing |
| **Email Providers** | Communication | Outgoing |
| **WhatsApp API** | Direct messaging | Outgoing |
| **Webhooks** | Real-time events | Bidirectional |

---

## Project Structure Overview

```
iperform/
├── main/
│   ├── api/           # 40+ Flask blueprints
│   ├── model/         # SQLAlchemy models
│   ├── utills/        # Helper functions
│   └── media/         # User-uploaded files
├── iperform_flow/     # Documentation & flow diagrams
│   └── flow_diagrams/
│       ├── auth/      # Authentication flows
│       ├── helper/    # Utility flows
│       └── landing_page/  # UI flows
├── static/            # Frontend assets
├── templates/         # HTML templates
├── config.py          # Configuration
├── database.py        # DB initialization
├── requirements.txt   # Dependencies
├── Dockerfile         # Container config
└── docker-compose.yml # Multi-container setup
```

---

## Performance Characteristics

- **Response Time**: < 500ms for typical API calls
- **Concurrent Users**: 100-500+ with connection pooling
- **Data Processing**: Real-time for < 100K records, batch processing for larger datasets
- **Scalability**: Horizontal (multi-instance) via container orchestration
- **Database Optimization**: ClickHouse for analytics, MySQL with indexing for transactions

---

## Business Value

### For Enterprises
- **Unified Lead Management**: Single platform for all sales channels
- **Real-time Visibility**: Track sales pipeline and team activity
- **Data-Driven Decisions**: Advanced reporting and analytics
- **Cost Efficiency**: Multi-system consolidation

### For Sales Teams
- **Simplified Workflow**: Intuitive lead assignment and follow-up
- **Performance Tracking**: Clear metrics on individual and team performance
- **Cross-functional Collaboration**: Role-based visibility and sharing

### For IT/Admins
- **Scalability**: Supports growing teams and data volumes
- **Integration**: Connects with existing business systems
- **Maintainability**: Well-structured codebase and documentation
- **Security**: Enterprise-grade access control

---

## Version Information

- **Framework Version**: Flask 2.2.2
- **Database Support**: MySQL 5.7+, ClickHouse 20.x+
- **Python Version**: 3.7+
- **Last Updated**: 2024
- **Status**: Development Completed
