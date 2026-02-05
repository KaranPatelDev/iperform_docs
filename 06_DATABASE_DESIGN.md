# Database Design

## Database Architecture Overview

iPerform operates with a **multi-database strategy** optimized for different workloads:

```
┌─────────────────────────────────────────────────────────────┐
│                   iPerform Database Cluster                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────┐  ┌──────────────────┐  ┌─────────────┐
│  iPerform (MySQL)   │  │  Magenta (MySQL) │  │ ClickHouse  │
│  Primary Data       │  │  Client Data     │  │  Analytics  │
│  Transactions       │  │  Company Config  │  │  & History  │
└─────────────────────┘  └──────────────────┘  └─────────────┘
      5.7+                    5.7+              20.x+
```

---

## 1. iPerform Database (Primary MySQL)

### **Purpose**
Core operational database storing real-time lead, user, and activity data.

### **Connection**
```
Host: DB_HOST (default: 43.250.167.242)
Port: DB_PORT (default: 3306)
Database: iperform
Driver: mysql+pymysql
```

### **Core Tables**

#### **Authentication & User Management**

**`user`** - System users and authentication
```sql
CREATE TABLE user (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(128) NOT NULL UNIQUE,
    email VARCHAR(128) NOT NULL,
    password VARCHAR(255) NOT NULL,        -- bcrypt hash
    role_id INT NOT NULL,                  -- FK: role.id
    user_id INT,                           -- FK: magenta.user.id
    active BOOLEAN DEFAULT 1,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    changed_on DATETIME,
    created_by INT,
    changed_by INT,
    FOREIGN KEY (role_id) REFERENCES role(id),
    INDEX idx_username (username),
    INDEX idx_email (email),
    INDEX idx_role_id (role_id)
);
```

**`role`** - User roles with hierarchical structure
```sql
CREATE TABLE role (
    id INT PRIMARY KEY,                    -- 1=ADMIN, 2=USER, 3=SALESPERSON, ...
    role_name VARCHAR(50) NOT NULL UNIQUE,
    permissions JSON,                      -- Feature permissions
    view_list VARCHAR(255),                -- Default views for role
    allowed_status_list VARCHAR(500),      -- Status options per role
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

**`session`** & **`all_session`** - Session management
```sql
CREATE TABLE all_session (
    id_session INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    token VARCHAR(500),                    -- JWT token
    session_start DATETIME,
    session_end DATETIME,
    device_info VARCHAR(255),
    user_agent TEXT,
    ip_address VARCHAR(45),                -- IPv4 or IPv6
    active BOOLEAN DEFAULT 1,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES user(id),
    INDEX idx_user_id (user_id),
    INDEX idx_token (token(100))           -- Partial index for large token
);

CREATE TABLE session_info (
    user_id INT PRIMARY KEY,
    no_of_logins INT DEFAULT 0,
    last_login DATETIME,
    FOREIGN KEY (user_id) REFERENCES user(id)
);
```

---

#### **Lead Management**

**`leads`** - Main lead repository
```sql
CREATE TABLE leads (
    id INT PRIMARY KEY AUTO_INCREMENT,
    firm_name VARCHAR(128),                -- Company name
    company_id INT NOT NULL,               -- Parent company
    crm_company_id INT,                    -- ERP company mapping
    
    -- Contact Information
    client_first_name VARCHAR(45),
    client_last_name VARCHAR(45),
    client_email_id VARCHAR(128),
    client_mobile_no VARCHAR(45),
    whatsapp_number VARCHAR(20),
    
    -- Integration Data
    integration_first_name VARCHAR(45),
    integration_last_name VARCHAR(45),
    integration_mobile_no VARCHAR(128),
    integration_email_id VARCHAR(128),
    
    -- Business Information
    industry VARCHAR(45),
    website VARCHAR(164),
    erp VARCHAR(32),                       -- SAP, Tally, Zoho, etc.
    erp_version TEXT,
    gstno VARCHAR(32),
    
    -- Lead Status & Qualification
    status VARCHAR(32),                    -- Status from lead_status table
    lead_status VARCHAR(32),               -- Additional status field
    reason_for_lost VARCHAR(45),
    qualified BOOLEAN DEFAULT 0,
    qualified_by VARCHAR(32),
    
    -- Assignment
    warrior_id VARCHAR(128),               -- FK: warrior.id
    shared_salesperson_id VARCHAR(128),    -- Multiple IDs (comma-separated)
    
    -- Pricing & Planning
    price_plan_id INT,                     -- FK: pricing_plan.id
    plan_expiry_date DATE,
    user_count INT,
    crm_price_plan_id INT,
    crm_plan_expiry_date DATE,
    crm_user_count INT,
    
    -- Lead Scoring
    probability INT DEFAULT 0,             -- 0-100 percentage
    tentative_value INT DEFAULT 0,         -- Deal value in currency units
    
    -- Additional Data
    platform_id JSON,                      -- Multiple platform IDs
    source VARCHAR(128),                   -- Facebook, Manual, API, etc.
    pain_point TEXT,                       -- Customer pain points
    remarks TEXT,
    adset_name VARCHAR(128),               -- Ad campaign name
    drive_location TEXT,                   -- File/Document location
    
    -- Flags
    paid BOOLEAN DEFAULT 0,
    favourite INT DEFAULT 0,
    is_user BOOLEAN DEFAULT 0,
    is_deleted INT DEFAULT 0,
    excel_created_on DATETIME DEFAULT '1900-01-01 00:00:00',
    
    -- Conversion Tracking
    converted_to_customer_on DATETIME,
    no_of_subcompanies INT,
    
    -- Audit Trail
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    changed_on DATETIME,
    created_by INT,
    changed_by INT,
    
    -- Indexes for Performance
    INDEX idx_company_id (company_id),
    INDEX idx_status (status),
    INDEX idx_warrior_id (warrior_id),
    INDEX idx_email (client_email_id),
    INDEX idx_mobile (client_mobile_no),
    INDEX idx_created_on (created_on),
    UNIQUE INDEX idx_client_email (client_email_id, company_id)
);
```

**`lead_status`** - Lead status tracking
```sql
CREATE TABLE lead_status (
    id INT PRIMARY KEY AUTO_INCREMENT,
    lead_id INT NOT NULL,
    status VARCHAR(32),                    -- New, Qualified, Demo, Proposal, Trial, Won, Lost
    changed_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    changed_by INT,
    reason_for_change TEXT,
    FOREIGN KEY (lead_id) REFERENCES leads(id) ON DELETE CASCADE,
    INDEX idx_lead_id (lead_id),
    INDEX idx_status (status),
    INDEX idx_changed_on (changed_on)
);
```

**`lead_logs`** - Detailed lead activity log
```sql
CREATE TABLE lead_logs (
    id INT PRIMARY KEY AUTO_INCREMENT,
    lead_id INT NOT NULL,
    company_id INT NOT NULL,
    activity_type VARCHAR(50),             -- call, email, meeting, update
    activity_details TEXT,
    logged_by INT,                         -- User ID
    logged_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    metadata JSON,                         -- Additional data
    FOREIGN KEY (lead_id) REFERENCES leads(id) ON DELETE CASCADE,
    INDEX idx_lead_id (lead_id),
    INDEX idx_company_id (company_id),
    INDEX idx_logged_on (logged_on)
);
```

---

#### **User Hierarchy Management**

**`salesperson`** - Salesperson records
```sql
CREATE TABLE salesperson (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,                  -- FK: user.id
    manager_id INT,                        -- FK: salesperson.id (parent)
    under_salesperson VARCHAR(255),        -- Comma-separated warrior IDs
    territory VARCHAR(128),
    target_value DECIMAL(15,2),
    active BOOLEAN DEFAULT 1,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES user(id),
    INDEX idx_user_id (user_id),
    INDEX idx_manager_id (manager_id),
    FULLTEXT INDEX ft_territory (territory)
);
```

**`warrior`** - Warrior records (follow-up specialists)
```sql
CREATE TABLE warrior (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,                  -- FK: user.id
    salesperson_id INT,                    -- FK: salesperson.id (parent)
    partner_company_id INT,
    data_visibility VARCHAR(50),           -- 'All' or specific company
    active BOOLEAN DEFAULT 1,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES user(id),
    INDEX idx_user_id (user_id),
    INDEX idx_salesperson_id (salesperson_id)
);
```

**`partner_company`** - Partner company information
```sql
CREATE TABLE partner_company (
    id INT PRIMARY KEY AUTO_INCREMENT,
    company_id INT NOT NULL,               -- Parent company
    partner_name VARCHAR(128) NOT NULL,
    contact_person VARCHAR(128),
    email VARCHAR(128),
    phone VARCHAR(20),
    address TEXT,
    city VARCHAR(50),
    active BOOLEAN DEFAULT 1,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_company_id (company_id),
    INDEX idx_partner_name (partner_name)
);
```

---

#### **Communication & Follow-up**

**`followup_activity`** - Follow-up tracking
```sql
CREATE TABLE followup_activity (
    id INT PRIMARY KEY AUTO_INCREMENT,
    lead_id INT NOT NULL,
    salesperson_id INT,
    warrior_id INT,
    next_followup_date DATETIME,
    followup_description TEXT,
    followup_status VARCHAR(50),           -- Pending, Completed, Postponed
    actual_followup_date DATETIME,
    comments TEXT,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    changed_on DATETIME,
    FOREIGN KEY (lead_id) REFERENCES leads(id) ON DELETE CASCADE,
    INDEX idx_lead_id (lead_id),
    INDEX idx_next_followup_date (next_followup_date)
);
```

**`whatsapp_messages`** - WhatsApp message log
```sql
CREATE TABLE whatsapp_messages (
    id INT PRIMARY KEY AUTO_INCREMENT,
    lead_id INT,
    company_id INT NOT NULL,
    phone_number VARCHAR(20),
    message_type VARCHAR(50),              -- Outbound, Inbound
    message TEXT,
    status VARCHAR(50),                    -- Sent, Delivered, Read, Failed
    sent_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    delivered_on DATETIME,
    read_on DATETIME,
    message_id VARCHAR(128) UNIQUE,
    FOREIGN KEY (lead_id) REFERENCES leads(id) ON DELETE SET NULL,
    INDEX idx_lead_id (lead_id),
    INDEX idx_sent_on (sent_on),
    INDEX idx_status (status)
);
```

**`iperform_mail`** - Email tracking
```sql
CREATE TABLE iperform_mail (
    id INT PRIMARY KEY AUTO_INCREMENT,
    lead_id INT,
    user_id INT NOT NULL,
    recipient_email VARCHAR(128),
    subject VARCHAR(255),
    body TEXT,
    status VARCHAR(30),                    -- not_send, sent, bounced, opened, clicked
    date DATETIME DEFAULT CURRENT_TIMESTAMP,
    mail_type VARCHAR(35),                 -- reminder, template, custom
    opened_count INT DEFAULT 0,
    opened_on DATETIME,
    FOREIGN KEY (lead_id) REFERENCES leads(id) ON DELETE SET NULL,
    INDEX idx_lead_id (lead_id),
    INDEX idx_status (status),
    INDEX idx_date (date)
);
```

---

#### **Ticketing System**

**`tickets`** - Support tickets
```sql
CREATE TABLE tickets (
    id INT PRIMARY KEY AUTO_INCREMENT,
    lead_id INT,
    company_id INT NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    ticket_type VARCHAR(50),               -- Bug, Feature Request, Sync, etc.
    priority VARCHAR(20),                  -- Urgent, High, Medium, Low
    status VARCHAR(50),                    -- Assigned, In Process, Resolved, Closed
    assigned_to INT,                       -- User ID
    created_by INT NOT NULL,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    resolved_on DATETIME,
    resolution_notes TEXT,
    FOREIGN KEY (lead_id) REFERENCES leads(id) ON DELETE SET NULL,
    INDEX idx_company_id (company_id),
    INDEX idx_status (status),
    INDEX idx_created_on (created_on)
);

CREATE TABLE ticket_communication (
    id INT PRIMARY KEY AUTO_INCREMENT,
    ticket_id INT NOT NULL,
    commented_by INT,
    comment_text TEXT,
    attachment_url VARCHAR(255),
    commented_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (ticket_id) REFERENCES tickets(id) ON DELETE CASCADE,
    INDEX idx_ticket_id (ticket_id)
);
```

---

#### **Activity & Logging**

**`login_history`** - User login tracking
```sql
CREATE TABLE login_history (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    login_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    logout_on DATETIME,
    device_info VARCHAR(255),
    ip_address VARCHAR(45),
    location VARCHAR(128),
    FOREIGN KEY (user_id) REFERENCES user(id) ON DELETE CASCADE,
    INDEX idx_user_id (user_id),
    INDEX idx_login_on (login_on)
);

CREATE TABLE logs (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    company_id INT,
    endpoint VARCHAR(255),                 -- API endpoint called
    method VARCHAR(10),                    -- GET, POST, PUT, DELETE
    request_body TEXT,
    response_status INT,
    response_body TEXT,
    execution_time_ms INT,
    logged_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_user_id (user_id),
    INDEX idx_logged_on (logged_on),
    INDEX idx_endpoint (endpoint)
);
```

---

#### **File Management**

**`deleted_files`** - Deleted files audit trail
```sql
CREATE TABLE deleted_files (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    deleted_file VARCHAR(255),
    file_path TEXT,
    comment TEXT,
    deleted_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES user(id),
    INDEX idx_deleted_on (deleted_on)
);
```

---

### **Table Count: 30+ Core Tables**

---

## 2. Magenta Database (Client MySQL)

### **Purpose**
Multi-tenant database storing company and user information accessible across iPerform ecosystem.

### **Connection**
```
Host: DB_HOST
Port: DB_PORT
Database: magenta
```

### **Core Tables**

**`company`** - Organizations/Companies
```sql
CREATE TABLE company (
    id INT PRIMARY KEY AUTO_INCREMENT,
    company_name VARCHAR(255) NOT NULL,
    gst_number VARCHAR(50),
    pan_number VARCHAR(50),
    website VARCHAR(255),
    industry VARCHAR(100),
    city VARCHAR(100),
    state VARCHAR(100),
    country VARCHAR(100),
    phone VARCHAR(20),
    email VARCHAR(128),
    
    -- iPerform Integration
    extractor_settings_token VARCHAR(255),
    db_schema_name VARCHAR(50),            -- Customer-specific DB
    
    -- Status
    active BOOLEAN DEFAULT 1,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_company_name (company_name),
    INDEX idx_active (active)
);
```

**`user`** - Client users (per company)
```sql
CREATE TABLE user (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(128) NOT NULL,
    email VARCHAR(128) NOT NULL,
    company_id INT NOT NULL,
    role_id INT,                           -- Department/Role
    is_iperform_user BOOLEAN,              -- iPerform employee flag
    active BOOLEAN DEFAULT 0,              -- Account status
    validity VARCHAR(50),                  -- full, limited, etc.
    forgot_password_token VARCHAR(255),
    created_by INT,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (company_id) REFERENCES company(id),
    UNIQUE INDEX idx_email_company (email, company_id),
    INDEX idx_company_id (company_id),
    INDEX idx_active (active)
);

CREATE TABLE user_info (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    mobile_1 VARCHAR(20),
    mobile_2 VARCHAR(20),
    user_birthdate DATE,
    user_anniversary_date DATE,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES user(id) ON DELETE CASCADE
);
```

**`pricing_plan`** - Subscription plans
```sql
CREATE TABLE pricing_plan (
    id INT PRIMARY KEY AUTO_INCREMENT,
    plan_name VARCHAR(100) NOT NULL,
    plan_duration INT,                     -- Days
    plan_price DECIMAL(10,2),
    no_of_users INT,
    features JSON,
    active BOOLEAN DEFAULT 1,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

**`dynamic_db`** - Dynamic database per customer
```sql
CREATE TABLE dynamic_db (
    id INT PRIMARY KEY AUTO_INCREMENT,
    company_id INT NOT NULL,
    db_type VARCHAR(50),                   -- mysql, clickhouse, postgresql
    db_host VARCHAR(255),
    db_port INT,
    db_user VARCHAR(100),
    db_password VARCHAR(255),              -- Encrypted
    db_schema_name VARCHAR(100),
    db_name VARCHAR(100),
    clickhouse_type INT,                   -- ClickHouse specific
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (company_id) REFERENCES company(id)
);
```

**`email_template`** - Email templates per company
```sql
CREATE TABLE email_template (
    id INT PRIMARY KEY AUTO_INCREMENT,
    company_id INT,
    template_name VARCHAR(255),
    template_subject VARCHAR(500),
    template_body LONGTEXT,
    created_by INT,
    created_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (company_id) REFERENCES company(id)
);
```

**`view_setting`**, **`user_setting`**, **`company_setting`** - User preferences
```sql
CREATE TABLE view_setting (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    setting JSON,                          -- Dashboard, column visibility
    updated_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES user(id)
);

CREATE TABLE user_setting (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    app_setting JSON,                      -- Theme, language, timezone
    FOREIGN KEY (user_id) REFERENCES user(id)
);

CREATE TABLE company_setting (
    id INT PRIMARY KEY AUTO_INCREMENT,
    company_id INT NOT NULL,
    user_id INT,
    setting JSON,
    FOREIGN KEY (company_id) REFERENCES company(id)
);
```

---

## 3. ClickHouse Database (Analytics)

### **Purpose**
High-performance analytics database for reporting, large-scale data storage, and real-time queries.

### **Connection**
```
Host: CLICKHOUSE_DB_HOST
Port: CLICKHOUSE_DB_PORT (8123)
Engine: ClickHouse
Version: 20.x+
```

### **Core Tables**

#### **iPerform Helper Schema**
```sql
-- Mirror tables from iPerform for analytics

CREATE TABLE iperform_helper.leads ENGINE=ReplacingMergeTree()
PARTITION BY company_id
ORDER BY (id, version)
AS SELECT 
    id, firm_name, company_id, client_email_id, 
    status, created_on, changed_on, warrior_id,
    toDateTime(now()) as version
FROM iperform.leads;

CREATE TABLE iperform_helper.lead_logs ENGINE=MergeTree()
PARTITION BY toYYYYMM(logged_on)
ORDER BY (lead_id, logged_on)
AS SELECT * FROM iperform.lead_logs;

CREATE TABLE iperform_helper.lead_status ENGINE=MergeTree()
PARTITION BY toYYYYMM(changed_on)
ORDER BY (lead_id, changed_on)
AS SELECT * FROM iperform.lead_status;

CREATE TABLE iperform_helper.demo_scheduled_data ENGINE=ReplacingMergeTree()
ORDER BY (lead_id, version)
AS SELECT 
    lead_id, scheduled_date, 
    toDateTime(now()) as version
FROM iperform.demo_scheduled_data;
```

#### **Extractor Schema**
```sql
-- Data extraction tracking

CREATE TABLE extractor.credentials ENGINE=MergeTree()
ORDER BY (company_id, created_on)
AS SELECT 
    company_id, erp_type, credentials_json,
    created_on, updated_on
FROM extractor.credentials;

CREATE TABLE extractor.subcompany_settings ENGINE=MergeTree()
PARTITION BY company_id
ORDER BY (id, company_id)
AS SELECT * FROM extractor.subcompany_settings;

CREATE TABLE extractor.reportwise_settings ENGINE=MergeTree()
ORDER BY (company_id, report_name)
AS SELECT * FROM extractor.reportwise_settings;

CREATE TABLE extractor.sync_history ENGINE=MergeTree()
PARTITION BY toYYYYMM(sync_date)
ORDER BY (company_id, sync_date)
AS SELECT 
    company_id, report_name, sync_date,
    status, records_processed, errors
FROM extractor.sync_history;

CREATE TABLE extractor.data_update ENGINE=MergeTree()
ORDER BY (company_id, created_on)
AS SELECT * FROM extractor.data_update;
```

#### **Company-Specific Schemas**
```
Each company gets its own ClickHouse schema:
- h_001 (Company 1)
- h_002 (Company 2)
- h_NNN (Company N)

Each schema contains:
- transactions
- customers
- invoices
- purchase_orders
- sales_vouchers
- (ERP-specific tables)
```

---

## Database Relationships

### **Key Relationships**

```
User Hierarchy:
    user (id=1) role_id=1 (ADMIN)
    user (id=2) role_id=3 (SALESPERSON)
    └─ salesperson (user_id=2) manager_id=NULL
        ├─ warrior (salesperson_id=X) user_id=5
        ├─ warrior (salesperson_id=X) user_id=6
        └─ salesperson (manager_id=2) (Sub-team)

Lead Ownership:
    leads (id=100) warrior_id=5 shared_salesperson_id="2,3"
    ├─ lead_status (lead_id=100) → status history
    ├─ lead_logs (lead_id=100) → activity log
    ├─ followup_activity (lead_id=100) → follow-ups
    ├─ whatsapp_messages (lead_id=100) → messages
    └─ iperform_mail (lead_id=100) → emails

Company Configuration:
    company (id=15)
    ├─ user (company_id=15) → Company users
    ├─ pricing_plan (id=X) → Subscription
    ├─ dynamic_db (company_id=15) → Custom DB
    ├─ email_template (company_id=15) → Templates
    └─ leads (company_id=15) → All company leads
```

---

## Indexing Strategy

### **Performance Indexes**

| Table | Index | Columns | Purpose |
|-------|-------|---------|---------|
| leads | idx_company_id | company_id | Filter by company |
| leads | idx_status | status | Status filtering |
| leads | idx_warrior_id | warrior_id | Lead ownership |
| leads | idx_created_on | created_on | Date range queries |
| leads | UNIQUE idx_email | client_email_id | Uniqueness check |
| lead_logs | idx_lead_id | lead_id | Lead activity lookup |
| lead_logs | idx_logged_on | logged_on | Time-range queries |
| user | idx_username | username | Login queries |
| login_history | idx_user_id | user_id | User activity tracking |
| followup_activity | idx_next_followup | next_followup_date | Due follow-ups |
| whatsapp_messages | idx_lead_id | lead_id | Message lookup |
| tickets | idx_status | status | Ticket filtering |

---

## Backup & Recovery

### **Backup Strategy**
- **Daily MySQL Backups**: Full backup once per week, incremental daily
- **ClickHouse Backups**: Materialized views for recovery
- **Retention**: 30 days minimum
- **Disaster Recovery**: Point-in-time recovery up to 30 days

### **Backup Commands**
```bash
# MySQL Backup
mysqldump -u user -p database > backup.sql
# Restore
mysql -u user -p database < backup.sql

# ClickHouse Backup
clickhouse-backup create backup_name
# Restore
clickhouse-backup restore backup_name
```

---

## Performance Considerations

### **Query Optimization**
- Use of database indexes for fast lookups
- Partitioning in ClickHouse for large tables
- Connection pooling (700 connections)
- Query result caching

### **Scaling**
- Horizontal: Database replication (master-slave)
- Vertical: Increase server resources
- Sharding: Optional sharding by company_id

---

## Security

- **Encryption**: Sensitive data encrypted at rest and in transit
- **Access Control**: User/password authentication
- **Audit Trail**: All modifications logged with user/timestamp
- **Backup Security**: Encrypted backups stored securely
- **Data Deletion**: Soft delete with archival (not permanent)

---

## Summary

iPerform's database design provides:
- ✅ Operational excellence through multi-database optimization
- ✅ Scalability through ClickHouse for analytics
- ✅ Data integrity through careful relational design
- ✅ Performance through strategic indexing
- ✅ Security through encryption and audit trails
- ✅ Flexibility through multi-tenant architecture
