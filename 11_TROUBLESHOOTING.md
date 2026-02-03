# Common Issues & Troubleshooting

## What This Guide is For

This document helps you solve common problems you might encounter while using iPerform. It's designed for all users, with simple steps to fix issues yourself.

**Golden Rule**: Before you do anything, **refresh the page**. Sometimes, that's all it takes!

## Section 1: For All Users

### Login Problems

**Problem: "I can't log in."**

**Solution 1: Check Your Credentials**
- Are you using the correct email address?
- Is Caps Lock on? Passwords are case-sensitive.
- Try typing your password in a text editor to see it, then copy and paste.

**Solution 2: Reset Your Password**
1. On the login page, click **"Forgot Password?"**.
2. Enter your email address.
3. Check your email for a reset link.
4. Follow the instructions to create a new password.
*If you don't get the email, check your spam folder.*

**Solution 3: Contact Your Administrator**
- Your account might be locked or deactivated.
- Your administrator can check your account status and reset your password for you.

---

### Slow Performance

**Problem: "The system is running very slow."**

**Solution 1: Check Your Internet Connection**
- Try visiting another website (like google.com). If it's also slow, the problem is likely your internet.
- Run an internet speed test.
- Try restarting your router.

**Solution 2: Clear Your Browser Cache**
- Your browser stores temporary data that can sometimes slow things down.
- **How to clear cache**:
  * **Chrome**: Settings > Privacy and security > Clear browsing data
  * **Firefox**: Options > Privacy & Security > Cookies and Site Data > Clear Data
  * **Edge**: Settings > Privacy, search, and services > Clear browsing data
- After clearing, close and reopen your browser.

**Solution 3: Try a Different Browser or Incognito Mode**
- This helps check if a browser extension is causing the issue.
- If it's faster in another browser, you may need to disable extensions in your primary browser.

**Solution 4: Is It Slow for Everyone?**
- Ask a colleague if they are experiencing slowness.
- If yes, the issue is likely with the server. Report it to your administrator.

---

### Data Not Saving

**Problem: "I updated a lead, but the changes didn't save."**

**Solution 1: Check for Error Messages**
- Look for any red banners or pop-up messages on the screen. They often explain the problem (e.g., "Phone number is invalid").
- Correct the error and try saving again.

**Solution 2: Check Your Internet Connection**
- If your internet disconnected while you were making changes, it might not be able to save.
- Look for a "Reconnecting..." or "Offline" message.
- Wait for your connection to restore and try again.

**Solution 3: Refresh and Re-enter**
- Copy any notes you've written.
- Refresh the page.
- Re-enter the information and save.

**Solution 4: Check Your Permissions**
- You might not have permission to edit certain fields.
- If you think you should have access, contact your administrator.

---

### Can't Find a Lead/Record

**Problem: "I know I entered a lead, but now I can't find it."**

**Solution 1: Check Your Filters**
- You might have a filter applied that is hiding the record.
- Look for active filters at the top of the list (e.g., "Status: Open", "Date Range: Last 7 Days").
- Click **"Clear All Filters"** or **"Reset View"**.

**Solution 2: Use the Global Search**
- Use the main search bar at the top of the page.
- Try searching by name, company, email, or phone number.

**Solution 3: Check if it was Reassigned**
- The lead might have been reassigned to another user.
- Ask your manager or administrator to check.

**Solution 4: Check for Duplicates**
- It might have been merged with another duplicate record.
- Search for the company name and see if it's under a different contact.

---

## Section 2: For Sales & Marketing Users

### Facebook Leads Not Appearing

**Problem: "We're running a Facebook ad, but leads aren't showing up in iPerform."**

**For Users**:
1. **Report it immediately** to your manager or administrator. This is a critical issue.
2. Provide details: Which ad campaign? When did you notice the problem?

**For Administrators**:
1. **Check Integration Status**: Go to Admin > Integrations > Facebook. Is it "Connected"?
2. **Re-authorize Connection**: Facebook tokens expire. Try re-connecting the integration.
3. **Check Webhook**: Ensure the Facebook webhook is correctly configured and active.
4. **Test with Facebook Tool**: Use the Facebook Lead Ads Testing Tool to send a test lead.
   - If the test lead comes through, the issue might be with the specific ad form.
   - If the test lead fails, the problem is with the connection itself.
5. **Check for Errors**: Look at the system logs for any error messages related to the Facebook API.

---

### Email Campaign Issues

**Problem: "My email campaign has a low open rate or is going to spam."**

**Solutions**:
1. **Check Your Subject Line**: Avoid spammy words like "Free," "Offer," "Sale," "!!!"
2. **Review Your Content**: Is there a good balance of images and text? Too many images can trigger spam filters.
3. **Check Your Sender Reputation**: Use online tools to check if your domain is on any blacklists.
4. **Verify Email Authentication**: Ask your admin to confirm that SPF, DKIM, and DMARC records are set up correctly for your domain. This is crucial for deliverability.
5. **Clean Your List**: High bounce rates hurt your reputation. Use a list-cleaning service or remove emails that have hard-bounced.

**Problem: "I can't send an email to a specific lead."**

**Solutions**:
1. **Check if they Unsubscribed**: The lead may have previously unsubscribed from your emails. The system will prevent you from sending more.
2. **Check for Bounces**: If a previous email to them "hard-bounced" (invalid address), the system will block future sends to protect your sender reputation.
3. **Check for Typos**: Is the email address spelled correctly?

---

### WhatsApp Message Failures

**Problem: "My WhatsApp messages are not being delivered."**

**Solutions**:
1. **Check Template Status**: Are you using a pre-approved template? If you edited it, it needs re-approval.
2. **Verify Opt-In**: Does the customer have a valid opt-in to receive messages from you?
3. **Check Phone Number**: Is the customer's phone number correct and in the right format (with country code)?
4. **Review WhatsApp Policies**: Your message might be violating a WhatsApp Commerce Policy.
5. **Check API Status**: Ask your admin to check the status of the WhatsApp Business API connection and your credit balance.

---

## Section 3: For Administrators

### User Can't Access a Feature

**Problem: "A user says they can't see the 'Reports' tab."**

**Solutions**:
1. **Check Their Role**: Go to User Management and find the user. What is their assigned role?
2. **Check Role Permissions**: Go to Admin > Roles & Permissions. Select the user's role.
   - Is the permission for "View Reports" enabled for that role?
   - If not, enable it and save. The user will need to refresh their page.
3. **Check for Overriding Permissions**: Is there any specific user-level setting that might be overriding the role permission?

---

### Data Import Failed

**Problem: "I tried to upload an Excel file of leads, and it failed."**

**Solutions**:
1. **Download the Error Report**: The system usually provides a report detailing which rows failed and why.
2. **Common Errors**:
   - **"Required Field Missing"**: A mandatory column (like 'Name' or 'Company') was left blank.
   - **"Invalid Email Format"**: An email address is incorrect (e.g., "john@domaincom" instead of "john@domain.com").
   - **"Duplicate Header"**: You have two columns with the same name.
   - **"Incorrect Data Type"**: You put text in a field that expects a number.
3. **Fix the Excel File**: Correct the errors in your spreadsheet based on the report.
4. **Re-upload**: Try uploading the corrected file.
5. **Try a Smaller Batch**: If it's a very large file, try uploading just the first 10 rows to see if it works. This can help isolate the problem.

---

### System Backup Failed

**Problem: "I received a notification that the nightly backup failed."**

**This is a high-priority issue.**

**Solutions**:
1. **Run a Manual Backup Immediately**: Go to Admin > Backup & Restore and trigger a manual backup.
2. **Check for Error Messages**: The backup log should provide a reason for the failure.
   - **"Insufficient Storage"**: The backup location is full. You need to free up space or increase capacity.
   - **"Connection Timed Out"**: The connection to the backup storage (e.g., cloud storage) was lost. Check the network connection.
   - **"Permission Denied"**: The system doesn't have the rights to write to the backup location. Check credentials and permissions.
3. **Check Disk Space**: Is the server's main disk full? This can prevent the backup file from being created before transfer.
4. **Contact Technical Support**: If you cannot resolve it quickly, escalate to your technical support team. Consistent backups are critical.

---

## When to Contact Support

You've tried the steps above, but the problem persists. It's time to get help.

### How to Write a Good Support Ticket

Providing clear information helps us solve your problem faster.

**Include the following**:

1. **Clear, Concise Title**:
   - **Bad**: "It's broken"
   - **Good**: "Cannot Save Updates to Leads - Error Message Appears"

2. **Who is Affected?**
   - "Just me"
   - "My whole team"
   - "All users"

3. **What Were You Trying to Do?**
   - "I was trying to update the status of the lead 'ABC Corp' from 'Open' to 'Connected'."

4. **What Did You Expect to Happen?**
   - "I expected the status to save and the page to refresh."

5. **What Actually Happened?**
   - "An error message popped up saying 'Invalid Operation'. The status did not change."

6. **Steps to Reproduce the Problem**:
   - 1. Log in as a Salesperson.
   - 2. Go to the Leads list.
   - 3. Open any lead.
   - 4. Try to change the status.

7. **Include Screenshots or Videos**:
   - A picture is worth a thousand words. A screenshot of the error message is extremely helpful.

8. **What Have You Already Tried?**
   - "I have already tried refreshing the page, clearing my browser cache, and using a different browser."

**By providing these details, you help the support team diagnose and fix your issue much more quickly.**

---

**Previous**: [← Daily Operations](10_OPERATIONS.md)  
**Next**: [Project Overview →](01_PROJECT_OVERVIEW.md)
