# Data Organization & Settings

## Understanding Your Data

This guide explains how information is organized in iPerform and what settings you can configure.

## How Data is Stored

Think of iPerform's database as a **well-organized digital filing system** where everything has its place and can be found quickly.

### Main Information Categories

**People & Users**:
- User accounts (login credentials, roles, permissions)
- Salesperson profiles
- Field warrior (representative) details
- Partner company contacts
- Manager hierarchies

**Sales & Leads**:
- Lead information (prospects, potential customers)
- Customer records (converted leads)
- Lead status history (tracking progress)
- Assignment records (who owns which lead)
- Follow-up schedules

**Communication**:
- Email history (all sent emails)
- WhatsApp messages
- Phone call logs
- Meeting notes
- Scheduled reminders

**Support**:
- Customer tickets
- Issue descriptions
- Resolution notes
- Technical documentation
- Support history

**Business Data**:
- Proposals and quotes
- Contracts and agreements
- Pricing plans
- Product catalogs
- Commission structures

**Activity Tracking**:
- Login history
- Status changes
- User actions
- System events
- Audit trails

## User Roles & Permissions

### The 9 User Types Explained

**1. Administrator (ADMIN)**
- **Can Access**: Everything in the system
- **Can See**: All leads, all customers, all tickets, all reports
- **Can Do**: Create users, change settings, assign leads, view any data, generate reports
- **Use Case**: System managers, business owners, IT administrators

**2. Manager (MANAGER)**
- **Can Access**: Their team's data and aggregate reports
- **Can See**: All leads assigned to their team, team performance metrics
- **Can Do**: Assign leads to salespersons, view team activities, access reports
- **Use Case**: Sales managers, team leaders

**3. Salesperson (SALESPERSON)**
- **Can Access**: Their own leads and customers
- **Can See**: Leads assigned to them, their performance metrics
- **Can Do**: Update lead status, send emails/WhatsApp, create tickets, schedule follow-ups
- **Use Case**: Inside sales representatives, account executives

**4. Warrior (Field Representative)**
- **Can Access**: Leads assigned to them in their territory
- **Can See**: Their customer list, meeting schedules
- **Can Do**: Update lead information, mark deals won/lost, schedule meetings
- **Use Case**: Field sales reps, regional representatives

**5. Integration Specialist**
- **Can Access**: Technical tickets and integration data
- **Can See**: Customer technical setups, integration status
- **Can Do**: Manage technical tickets, update integration status, access technical settings
- **Use Case**: Technical integration team, implementation specialists

**6. Customer Success Manager**
- **Can Access**: Existing customer data and support tickets
- **Can See**: Customer health metrics, support history
- **Can Do**: Create/manage tickets, communicate with customers, track satisfaction
- **Use Case**: Customer success team, account managers

**7. Developer (DEV)**
- **Can Access**: Technical aspects and system settings
- **Can See**: Technical tickets, system logs, error reports
- **Can Do**: Resolve technical issues, access debugging tools, manage integrations
- **Use Case**: Development team, technical support

**8. Co-Sales Support (COSALES)**
- **Can Access**: Limited sales data to assist sales team
- **Can See**: Selected leads, basic customer information
- **Can Do**: Add notes, create tickets, send communications
- **Use Case**: Sales assistants, support staff

**9. Guest (View-Only)**
- **Can Access**: Selected reports and dashboards
- **Can See**: Summary data, reports (no detailed customer information)
- **Can Do**: View only, cannot edit anything
- **Use Case**: Executives, stakeholders, investors

### What Each Role Can See

| Information | Admin | Manager | Salesperson | Warrior | Tech Team | Guest |
|------------|-------|---------|-------------|---------|-----------|-------|
| All Leads | ✓ | ✓ | Own Only | Own Only | Relevant | Summary |
| All Customers | ✓ | ✓ | Own Only | Own Only | ✓ | No |
| Team Performance | ✓ | ✓ | Own Only | Own Only | No | Summary |
| Support Tickets | ✓ | View Only | View Only | No | ✓ | No |
| System Settings | ✓ | No | No | No | Limited | No |
| Financial Data | ✓ | ✓ | No | No | No | No |
| Audit Logs | ✓ | No | No | No | ✓ | No |

## System Settings

### Environment Configuration

**Development Mode** (Testing):
- Used for testing new features
- Errors shown in detail
- Debug information available
- Test data allowed

**Production Mode** (Live):
- Used for actual business operations
- Error details hidden from users
- Enhanced security
- Real customer data

### Lead Configuration

**Lead Statuses** (18 stages):

**Early Stage**:
- **Open**: Brand new lead
- **Connected**: Made first contact
- **Demo Scheduled**: Booked product demo
- **Reschedule Demo**: Demo needs rescheduling

**Mid Stage**:
- **Demo Given**: Completed product demonstration
- **Demo on Data**: Demo using customer's actual data
- **Trial Demo**: Customer trying product
- **Proposal Sent**: Sent pricing and proposal

**Advanced Stage**:
- **On Hold**: Temporarily paused
- **Integration Approved**: Technical approval received
- **Integration Approved CRM**: CRM integration approved
- **Integration Complete**: Fully integrated

**Final Stage**:
- **Won**: Deal closed successfully! 🎉
- **Lost**: Opportunity lost
- **Delight**: Ongoing customer relationship

**Special Status**:
- **Form Resubmitted**: Customer inquired again
- **RS-Unanswered**: Rescheduled but no response
- **Renew**: Renewal opportunity

### Ticket System Settings

**Issue Types** (11 categories):
- **Sync Issues**: Data synchronization problems
- **Data Mismatch**: Information doesn't match
- **Feature Request**: Customer wants new feature
- **Product Bug**: Something's not working right
- **Customization**: Custom development needed
- **Integration**: Technical integration issues
- **Product Tour**: Training/demo request
- **Testing Bug**: Found during testing
- **Writer Bug**: Documentation issue
- **Reintegration**: Needs re-integration
- **Others**: Miscellaneous issues

**Ticket Priority Levels**:
- **Normal**: Can wait, address in regular workflow
- **High**: Important, address within 24 hours
- **Critical**: Urgent, address immediately

**Ticket Statuses** (10 stages):
- **In Queue**: Waiting for assignment
- **Assigned**: Assigned to someone
- **In Process**: Actively being worked on
- **Pending At Customer**: Waiting for customer response
- **Pending At Salesperson**: Needs sales team input
- **Resolved**: Solution found
- **Pending For Review**: Needs manager review
- **Deployed**: Changes live in production
- **Closed**: Issue completely resolved
- **Archived**: Closed and archived for records

### Communication Settings

**Email Configuration**:
- Sender email address (shows in "From" field)
- Email templates for common scenarios
- Email tracking enabled/disabled
- Automatic CC to specific addresses

**WhatsApp Settings**:
- WhatsApp Business number
- Approved message templates
- Auto-response rules
- Business hours settings

**Notification Preferences**:
- Push notifications on/off
- Email notifications on/off
- Notification frequency
- Which events trigger notifications

### Lead Source Configuration

**Where Leads Come From**:
- **Facebook**: Facebook lead ads
- **Instagram**: Instagram campaigns
- **Google**: Google ads, organic search
- **Website**: Your company website forms
- **Partner**: Partner company referrals
- **Client Reference**: Existing customer referrals
- **ACMA**: Industry association
- **Self**: Direct outreach
- **Outbound**: Proactive sales
- **Others**: Miscellaneous sources

### Industry Categories

**Customer Industry Types** (50+ options):
Agriculture, Automotive, Healthcare, IT Services, Manufacturing, Retail, Finance, Real Estate, Education, and many more...

This helps you understand your customer base and tailor your approach.

### Partner Configuration

**Partner Types**:
- **Magenta Growth Partner**: Strategic growth partners
- **Magenta Direct Partner**: Direct sales partners
- **Magenta State Partner**: State-level partners
- **Magenta Regional Partner**: Regional partners

**Data Visibility Settings**:
- **Self**: Partner sees only their own data
- **All**: Partner sees all data in their company

### Access Control Settings

**Who Can Create Tickets**:
Administrators, Customer Success, Integration Team, Co-Sales, Developers, Salespersons

**Who Can Edit Tickets**:
Administrators, Customer Success, Developers, Integration Team, Co-Sales

**Who Can View Tickets**:
Everyone except Guests and Warriors (field team)

**Who Can Assign Leads**:
Administrators and Managers only

## Security Settings

### Password Requirements

- Minimum 8 characters
- Must include letters and numbers
- Changed periodically for security
- Cannot reuse last 3 passwords

### Session Management

- Sessions expire after 90 days of inactivity
- Automatic logout after extended idle time
- Device information tracked
- Multiple device login allowed
- Can view and revoke active sessions

### Data Privacy

**What Gets Logged**:
- Login/logout times
- Lead status changes
- Data modifications
- Email sends
- Report generation
- System events

**Who Can See Logs**:
- Administrators: All logs
- Managers: Their team's logs
- Others: Own activity only

## Performance Settings

### Database Connection Settings

- Maximum simultaneous connections: 700
- Connection recycle time: 280 seconds
- Connection timeout: 300 seconds
- Health checks enabled

**What This Means**: System can handle up to 700 simultaneous users efficiently without slowing down.

### Email Delivery Settings

- Maximum emails per day: Based on AWS SES limits
- Email retry attempts: 3 times if delivery fails
- Bounce handling: Automatic
- Unsubscribe handling: Automatic

## Feature Flags

### Enabled/Disabled Features

**Token Expiration**:
- **Enabled**: Sessions expire (more secure, recommended for production)
- **Disabled**: Sessions don't expire (convenient for testing)

**Email Tracking**:
- **Enabled**: Track when emails are opened
- **Disabled**: No tracking (more privacy)

**Debug Mode**:
- **Enabled**: Show detailed errors (development only)
- **Disabled**: Hide error details (production)

## Customization Options

### Branding

- Company logo
- Color scheme
- Email templates with company branding
- Custom domains for links

### Regional Settings

- Date format (DD-MM-YYYY or MM-DD-YYYY)
- Currency symbol
- Decimal precision
- Fiscal year start month
- Timezone

### Workflow Customization

- Custom lead statuses (if standard 18 aren't enough)
- Custom ticket types
- Custom fields for leads
- Custom report parameters

## Backup Settings

### What Gets Backed Up

- All database records (complete)
- Uploaded documents
- System configuration
- Email templates
- User preferences

### Backup Schedule

- **Daily**: Full database backup at 2 AM
- **Weekly**: Complete system backup on Sundays
- **Retention**: 30 days of backups kept

## Best Practices

### For Administrators

✓ Review user permissions quarterly  
✓ Monitor system performance weekly  
✓ Check backup success daily  
✓ Update security settings regularly  
✓ Audit user activity monthly  

### For Managers

✓ Ensure team members have correct roles  
✓ Review lead assignment distribution  
✓ Monitor team performance metrics  
✓ Keep communication templates updated  
✓ Regularly check report accuracy  

### For All Users

✓ Use strong passwords  
✓ Logout when done  
✓ Don't share login credentials  
✓ Report suspicious activity  
✓ Keep contact information updated  

---

**Previous**: [← How It Works](03_ARCHITECTURE.md)  
**Next**: [Available Features →](06_API_REFERENCE.md)
