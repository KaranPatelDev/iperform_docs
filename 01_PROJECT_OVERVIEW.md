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
