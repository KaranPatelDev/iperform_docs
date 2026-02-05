# Features

## Core Features Overview

iPerform provides a comprehensive suite of features designed to address all aspects of modern sales and lead management.

---

## **1. Lead Management System**

### 1.1 Lead Creation & Entry
- **Multiple Sources**: Create leads from:
  - Manual entry via web interface
  - Facebook Lead Ads API integration
  - CSV/Excel import with validation
  - REST API for programmatic entry
  - External system webhooks
  
- **Lead Data Fields**:
  - Company information (firm name, website, industry)
  - Contact information (name, email, phone, WhatsApp)
  - Sales data (ERP, price plan, user count)
  - Assignment data (salesperson, warrior, partner)
  - Status tracking (lead status, qualification, conversion)
  - Custom fields (flexible schema per company)

- **Data Validation**:
  - Email format validation
  - Phone number validation
  - Mandatory field enforcement
  - Real-time duplicate detection

### 1.2 Lead Status Management
- **Predefined Statuses**: Demo, Proposal, Trial, Won, Lost, Pending, etc.
- **Status Transitions**: Define allowed status progression rules
- **Reason Tracking**: Capture reason for lost leads
- **Status History**: Complete audit trail of all status changes
- **Auto-status**: Automatic status updates based on conditions

### 1.3 Lead Lifecycle Tracking
```
New Lead → Qualification → Demo Scheduled → Proposal Sent → 
Trial Initiated → Purchase Completed / Lost
```
- Track each stage completion
- Measure time spent in each stage
- Identify bottlenecks in conversion
- Generate funnel reports

### 1.4 Lead Assignment
- **Automatic Assignment**: Assign based on rules (geography, skill, availability)
- **Manual Assignment**: Direct assignment by managers
- **Salesperson Hierarchy**: 
  - Manager assigns to Salesperson
  - Salesperson delegates to Warrior
  - Warriors handle day-to-day follow-ups
- **Co-assignment**: Multiple salespeople can work on same lead
- **Reassignment**: Change ownership based on performance
- **Assignment History**: Track all ownership changes

### 1.5 Lead Search & Filtering
- **Global Search**: Search by name, email, company, phone
- **Advanced Filters**: Filter by status, source, salesperson, date range
- **Saved Views**: Save custom filter combinations
- **Bulk Operations**: Select and perform actions on multiple leads
- **Export Capability**: Export filtered leads to CSV/Excel

### 1.6 Lead Sharing
- **Salesperson Sharing**: Share leads between salespeople for co-selling
- **Team Sharing**: Team managers can share across team members
- **Partner Sharing**: Send leads to partner companies
- **Access Tracking**: Know who has access to each lead
- **Revoke Access**: Remove access when needed

### 1.7 Lead Quality Scoring
- **Qualification Status**: Mark leads as qualified/unqualified
- **Probability Score**: Assign conversion probability (0-100%)
- **Tentative Value**: Estimated deal value
- **Lead Scoring Algorithm**: Automatic scoring based on interactions
- **Quality Metrics**: Track quality by source and salesperson

---

## **2. Activity Tracking & Management**

### 2.1 Lead Activity History
- **Timeline View**: Chronological view of all lead activities
- **Activity Types**:
  - Email communications
  - Phone calls (logged manually or via integration)
  - WhatsApp messages
  - Follow-up meetings
  - Status changes
  - Data modifications

- **Activity Metadata**: Who, What, When, Why
- **Activity Filtering**: Filter by type, date, user
- **Activity Analytics**: Understand what activities lead to conversion

### 2.2 Follow-up Management
- **Schedule Follow-ups**: Set next follow-up date and time
- **Follow-up Reminders**: Email/notification reminders before scheduled time
- **Follow-up History**: Track all follow-ups completed
- **Due Follow-ups**: View overdue and upcoming follow-ups
- **Follow-up Notes**: Add context and notes to each follow-up

### 2.3 Communication Management
- **Email Integration**: 
  - Send templated emails
  - Link email replies to leads
  - Track email open/click rates
  - Email archive with lead

- **WhatsApp Integration**:
  - Send WhatsApp messages from app
  - Link WhatsApp messages to leads
  - Message templates
  - WhatsApp delivery tracking

- **Call Management**:
  - Log calls to leads
  - Call duration and notes
  - Missed call tracking
  - Call outcome recording

- **Communication History**: Complete interaction timeline per lead

### 2.4 Activity Reports
- **Login Activity Report**: User login patterns and frequency
- **Company Activity Report**: Activity metrics by company
- **Activity by Time Range**: Daily, weekly, monthly views
- **User Active Now**: Real-time view of who's active
- **Activity Metrics**: Calls, emails, follow-ups per user

### 2.5 Lead Aging & Stalling Detection
- **Leads Without Follow-up**: Identify leads not followed up for N days
- **Automatic Alerts**: Notify managers of stalled leads
- **Aging Analysis**: Age distribution of leads
- **Risk Scoring**: Score leads at risk of being lost
- **Intervention Recommendations**: Suggest actions for at-risk leads

---

## **3. Reporting & Analytics**

### 3.1 Pre-built Reports (50+ templates)

#### **Sales Performance Reports**
- **Salesperson Report**: Individual salesperson metrics
  - Leads assigned, converted, lost
  - Conversion rate
  - Average deal value
  - Pipeline value

- **Salesperson Count Report**: Lead funnel by stage
  - Demo scheduled count
  - Proposal sent count
  - Trial initiated count
  - Won/Lost count

- **Salesperson Activity Report**: Activity metrics
  - Follow-ups completed
  - Emails sent
  - Calls made
  - Login frequency

#### **Lead Reports**
- **Lead Status Report**: Distribution of leads by status
- **Lost Leads Report**: Analysis of why leads were lost
- **Focus Leads Report**: High-value lead tracking
- **Mirror View Report**: Caller/salesperson view of leads
- **Lead Quality Report**: Quality metrics and trends

#### **Activity Reports**
- **Company Activity Report**: Company-wide metrics
- **User Activity Report**: Individual user metrics
- **Login Activity Report**: User login patterns
- **Activity by Time Window**: Hourly, daily, monthly views

#### **Partner & Warrior Reports**
- **Partner Company Report**: Partner performance and assignments
- **Warrior Report**: Warrior activity and metrics
- **Partner vs Internal Sales**: Comparison analysis

#### **Ad Campaign Reports**
- **Monthly Adset Report**: Ad campaign lead generation metrics
- **Cost-Revenue Analysis**: ROI by adset
- **Lead Details by Adset**: Leads generated per campaign

### 3.2 Real-time Dashboards
- **Executive Dashboard**: High-level KPIs and metrics
- **Sales Dashboard**: Individual salesperson metrics
- **Team Dashboard**: Team performance overview
- **Manager Dashboard**: Team management and assignment
- **Admin Dashboard**: System health and user metrics

### 3.3 Custom Reporting
- **Dynamic Query Builder**: Create custom reports without coding
- **Report Scheduling**: Automated report generation and email
- **Report Export**: CSV, Excel, PDF, JSON formats
- **Report Sharing**: Share reports with team members
- **Saved Reports**: Save custom reports for reuse

### 3.4 Analytics Queries
- **Direct SQL Execution**: Execute custom SQL queries (admin only)
- **Query History**: Track executed queries
- **Saved Queries**: Save frequently used queries
- **Query Performance**: Monitor query execution time

---

## **4. Multi-Company & Multi-Database Support**

### 4.1 Company Management
- **Company Onboarding**: Add new companies to system
- **Company Settings**: Configure per-company settings
- **Company Isolation**: Complete data isolation between companies
- **Company Hierarchy**: Parent-subsidiary relationships
- **Company Status**: Active/inactive management

### 4.2 Dynamic Database Assignment
- **Per-Company Database**: Each company can have dedicated database
- **Database Configuration**: Manage database connection per company
- **Database Type Support**: MySQL, ClickHouse, PostgreSQL
- **Dynamic Database Switching**: Switch databases on per-query basis
- **Database Health Monitoring**: Monitor database connection health

### 4.3 Multi-Tenant Architecture
- **Data Isolation**: Complete logical separation of company data
- **Role-based Visibility**: Users only see their company's data
- **Cross-company Admin**: Admin users can view all companies
- **Scalability**: Support hundreds of companies in single instance

---

## **5. User Management & Access Control**

### 5.1 User Roles (9 Types)
| Role | Purpose | Key Access |
|------|---------|-----------|
| **ADMIN** | System administrator | All features, all data, system settings |
| **MANAGER** | Team manager | Team leads, team performance, assignments |
| **SALESPERSON** | Sales representative | Own leads, assigned leads, team leads |
| **WARRIOR** | Follow-up specialist | Assigned leads only, communication |
| **PARTNER** | Partner company | Assigned leads, lead details |
| **COSALES** | Co-seller | Shared leads, collaboration |
| **CUSTOMER_SUCCESS** | Support specialist | Support tickets, customer leads |
| **INTEGRATION** | Data integration | API access only, no UI |
| **DEV** | Development | All data for testing, API access |

### 5.2 Role-based Permissions
- **Feature Access**: Control which features each role can access
- **Data Access**: Filter data based on user role
- **Action Permissions**: Define what actions each role can perform
- **Field-level Access**: Control access to sensitive fields
- **Time-based Access**: Temporary access elevation
- **IP-based Restrictions**: Restrict access by IP address (optional)

### 5.3 Organizational Hierarchy
- **Manager-Salesperson-Warrior Structure**:
  ```
  Manager
  ├── Salesperson 1
  │   ├── Warrior 1
  │   ├── Warrior 2
  │   └── Salesperson 1.1 (Sub-team)
  └── Salesperson 2
      └── Warrior 3
  ```

- **Hierarchical Filtering**: Show only data relevant to hierarchy level
- **Cascading Assignments**: Assign leads down the hierarchy
- **Team Performance**: Roll-up metrics at each level

### 5.4 User Profile Management
- **User Information**: Name, email, phone, role
- **User Preferences**: Theme, language, timezone
- **Password Management**: Change password, reset, strong policy
- **User Status**: Active/inactive toggle
- **User Activity**: Track user login and activity history

### 5.5 Permission Management
- **Create Permissions**: Define custom permission sets
- **Assign Permissions**: Assign multiple permissions per role
- **Dynamic Permissions**: Change permissions without app restart
- **Audit Permission**: Track all permission changes
- **Permission Expiry**: Time-limited permission escalation

---

## **6. ERP & System Integration**

### 6.1 Supported ERP Systems (14+)
- **AJMERA**: Ajmera ERP system integration for master data and transaction sync
- **ALIGN_BOOKS**: Align Books accounting software integration
- **BUSY**: BUSY accounting software data sync
- **CTRON**: Ctron ERP system integration
- **INTELLIGENT**: Intelligent ERP system for business operations
- **MARG**: Marg ERP software integration for invoice and master data
- **RATNAGIRI_HANA**: Ratnagiri HANA ERP system integration
- **SAP_B1**: SAP Business One complete data sync
- **SEPL**: SEPL ERP system integration
- **SHARED_ERP**: Shared ERP infrastructure for common data access
- **SYSPRO**: Syspro ERP system integration
- **TALLY**: Tally accounting software for invoice and master data
- **UMIYA**: Umiya ERP system integration
- **ZOHO**: Zoho CRM and accounting integration

### 6.2 Data Extraction Service
- **Automated Data Pull**: Scheduled extraction from ERP
- **Real-time Sync**: Webhook-based real-time updates
- **Transformation Engine**: Map and transform ERP data
- **Error Handling**: Automatic retry with exponential backoff
- **Change Tracking**: Only sync changed records
- **Data Validation**: Validate extracted data before loading

### 6.3 Data Mapping
- **Field Mapping**: Map ERP fields to iPerform fields
- **Custom Mapping**: Create company-specific mappings
- **Multi-field Mapping**: Combine multiple ERP fields into one
- **Conditional Mapping**: Apply different logic based on conditions
- **Mapping Templates**: Pre-built templates for common ERPs

### 6.4 Integration Management
- **Integration Status**: View sync status and health
- **Sync History**: Complete history of all sync operations
- **Sync Logs**: Detailed logs of sync activities
- **Error Resolution**: Handle and resolve sync errors
- **Manual Sync Trigger**: Force sync when needed
- **Sync Scheduling**: Configure sync frequency

### 6.5 Webhook Support
- **Event Triggers**: Trigger actions on data events
- **Webhook Delivery**: Reliable webhook delivery with retry
- **Payload Customization**: Customize webhook payload
- **Webhook Testing**: Test webhook configuration
- **Event History**: Track all webhook deliveries

---

## **7. Email & Communication**

### 7.1 Email Templates
- **Template Library**: Pre-built email templates
- **Template Variables**: Dynamic field substitution
- **Email Designer**: WYSIWYG email builder
- **Template Versioning**: Manage multiple versions
- **Template Categories**: Organize templates by purpose

### 7.2 Email Management
- **Send Emails**: Send templated or custom emails
- **Email Scheduling**: Schedule emails to send later
- **Email Tracking**: Track open and click rates
- **Reply Handling**: Automatic reply linking to leads
- **Email Archive**: Archive all sent emails with lead

### 7.3 Reminder System
- **Scheduled Reminders**: Email reminders for follow-ups
- **Reminder Customization**: Choose reminder timing
- **Reminder Content**: Customize reminder message
- **Reminder Analytics**: Track reminder engagement

### 7.4 WhatsApp Integration
- **Send Messages**: Send WhatsApp messages from app
- **Message Templates**: Pre-defined WhatsApp templates
- **Two-way Communication**: Receive and reply to messages
- **Message Status**: Delivery and read status
- **Media Sharing**: Share images and files

---

## **8. File Management**

### 8.1 File Storage
- **Upload Files**: Upload proposals, contracts, documentation
- **Folder Structure**: Organize files in folders
- **File Versioning**: Track multiple versions of files
- **File Permissions**: Control who can access files
- **File Size Limits**: Configurable upload limits

### 8.2 File Operations
- **Download Files**: Download individual or multiple files
- **Share Files**: Share files with team members
- **Delete Files**: Move to trash or permanently delete
- **File Search**: Search files by name or content
- **File Preview**: Preview documents (PDF, image)

### 8.3 Cloud Integration
- **AWS S3 Integration**: Store files in S3
- **Automatic Backup**: Files automatically backed up
- **CDN Delivery**: Fast delivery of files
- **Access Control**: Control who can download

---

## **9. Ticketing System**

### 9.1 Issue Tracking
- **Create Tickets**: Submit support issues
- **Ticket Types**: Sync, Bug, Feature Request, Customization
- **Ticket Status**: Assigned, In Process, Resolved, Closed
- **Priority Levels**: Urgent, High, Medium, Low
- **Assignment**: Assign to team members or teams

### 9.2 Ticket Management
- **Ticket Details**: Title, description, attachments
- **Comments**: Discussion thread on tickets
- **Status Tracking**: Move ticket through workflow
- **Resolution**: Close with resolution notes
- **Reopen Tickets**: Reopen if issue persists

### 9.3 Ticket Analytics
- **Open Tickets Report**: Count and aging of open tickets
- **Resolution Time**: Average time to resolve
- **Team Utilization**: Workload across team
- **Ticket Trends**: Volume and patterns over time

---

## **10. Advanced Features**

### 10.1 Global Search
- **Full-text Search**: Search across all lead data
- **Multi-field Search**: Search across multiple fields
- **Wildcard Support**: Use wildcards in search
- **Search History**: Save and reuse searches

### 10.2 Bulk Operations
- **Bulk Status Update**: Update status for multiple leads
- **Bulk Assignment**: Assign multiple leads to user
- **Bulk Sharing**: Share multiple leads with users
- **Bulk Delete**: Delete multiple leads (with confirmation)
- **Bulk Email**: Send email to multiple leads

### 10.3 Import/Export
- **CSV Import**: Bulk import leads from CSV
- **Excel Import**: Bulk import with Excel
- **CSV Export**: Export leads to CSV
- **Excel Export**: Export to Excel with formatting
- **Data Validation**: Validate imported data

### 10.4 Notifications
- **In-app Notifications**: Real-time app notifications
- **Email Notifications**: Email for important events
- **SMS Notifications**: SMS alerts for urgent items (optional)
- **Notification Preferences**: Customize notification settings
- **Notification History**: View past notifications

### 10.5 Audit Logging
- **User Actions**: Log all user actions
- **Data Changes**: Track all data modifications
- **Login History**: Record all logins
- **Access Audit**: Track data access
- **Change Details**: Show before/after values

### 10.6 Mobile Access
- **Responsive Design**: Full mobile responsive interface
- **Mobile App**: Native mobile apps (iOS/Android optional)
- **Offline Mode**: Work offline and sync when online
- **Mobile Notifications**: Push notifications on mobile
- **Touch Optimization**: Optimized for touch interface

---

## **11. System Administration**

### 11.1 Configuration Management
- **Company Settings**: Manage per-company settings
- **Feature Flags**: Enable/disable features
- **Global Settings**: System-wide configurations
- **Email Settings**: Configure email system
- **Database Settings**: Manage database connections

### 11.2 User Administration
- **User Management**: Create, edit, delete users
- **Bulk User Import**: Import users from CSV
- **Password Reset**: Reset forgotten passwords
- **User Activation**: Activate/deactivate users
- **User Auditing**: Track user activity

### 11.3 System Monitoring
- **Health Checks**: Monitor system health
- **Database Monitoring**: Monitor database performance
- **API Monitoring**: Monitor API response times
- **Error Tracking**: Track system errors
- **Performance Metrics**: System performance statistics

### 11.4 Backup & Recovery
- **Automated Backups**: Scheduled database backups
- **Backup Status**: View backup status and history
- **Point-in-time Recovery**: Restore to specific point in time
- **Disaster Recovery**: Restore from backup when needed
- **Retention Policy**: Configure backup retention

---

## **Feature Availability by Role**

Different roles have access to different features:

| Feature | Admin | Manager | Salesperson | Warrior | Partner |
|---------|-------|---------|-------------|---------|---------|
| Create Lead | ✓ | ✓ | ✓ | ✗ | ✗ |
| Edit Lead | ✓ | ✓ | ✓ | Limited | Limited |
| Assign Lead | ✓ | ✓ | ✓ | ✗ | ✗ |
| View Reports | ✓ | Team | Own | ✗ | Limited |
| Manage Users | ✓ | Team | ✗ | ✗ | ✗ |
| Email Lead | ✓ | ✓ | ✓ | ✓ | ✓ |
| View All Leads | ✓ | Team | Own | Assigned | Assigned |
| Manage Tickets | ✓ | ✓ | ✓ | Limited | ✗ |

---

## Summary

iPerform provides 100+ distinct features covering all aspects of modern sales operations, from lead capture through conversion, integrated with enterprise ERP systems, and delivered through an intuitive, role-based interface.
