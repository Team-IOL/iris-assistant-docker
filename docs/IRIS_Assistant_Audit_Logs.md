# IRIS Assistant - Audit Log Access Guide

**Version:** 1.0  
**Last Updated:** October 2025  
**For:** Administrators and Compliance Officers

---

## Table of Contents

1. [Overview](#overview)
2. [Accessing Audit Logs](#accessing-audit-logs)
3. [Log Structure](#log-structure)
4. [Reading the Logs](#reading-the-logs)
5. [Common Analysis Tasks](#common-analysis-tasks)
6. [Security Monitoring](#security-monitoring)
7. [Data Retention](#data-retention)
8. [Compliance and Privacy](#compliance-and-privacy)

---

## Overview

### What Are Audit Logs?

The IRIS Assistant automatically logs every interaction with the system in Google Sheets. These logs provide:

- Complete history of all bot interactions
- User activity tracking
- Security monitoring
- Compliance documentation
- Usage statistics

### Log Types

**Two separate log sheets:**

1. **Sheet1** - Authorized access logs
   - Successful queries
   - Document requests
   - Email deliveries
   - Normal operations

2. **Unauthorize_Access** - Unauthorized access attempts
   - Blocked users
   - Access denied events
   - Security incidents

---

## Accessing Audit Logs

### Location

**Google Sheets:** IRIS_Audit_Log

**Direct Link:** `https://docs.google.com/spreadsheets/d/[YOUR_SPREADSHEET_ID]/edit`

### Required Permissions

You need **Edit** access to:
- View all logs
- Filter and sort data
- Create reports
- Export data

**To request access:**
1. Contact the system administrator
2. Provide your Google account email
3. Specify your role (viewer, editor, admin)

### Navigation

1. Open the Google Sheet link
2. Two tabs at the bottom:
   - **Sheet1** - Authorized access
   - **Unauthorize_Access** - Blocked attempts
3. Use tabs to switch between log types

---

## Log Structure

### Authorized Access Log (Sheet1)

**Columns:**

| Column | Description | Example |
|--------|-------------|---------|
| timestamp | When the request occurred (PHT) | 2025-01-04 14:23:45 |
| telegram_user_id | User's Telegram ID | 2061662252 |
| telegram_first_name | User's first name | Juan |
| user_message | Original user query | "Show expired documents" |
| action_type | Tool that was used | send_email_with_documents |
| crew_searched | Crew member referenced | Juan Dela Cruz |
| applicant_id | Database crew ID | 12345 |
| recipient_email | Email address used | admin@company.com |
| filter_type | Document filter applied | expired |
| categories | Document categories | training,medical |
| status | Success or error | success |
| bot_response | What was returned to user | "Sent 5 documents..." |

### Unauthorized Access Log

**Columns:**

| Column | Description | Example |
|--------|-------------|---------|
| timestamp | When attempt occurred (PHT) | 2025-01-04 14:23:45 |
| telegram_user_id | Unauthorized user's ID | 9876543210 |
| telegram_first_name | User's first name | Unknown |
| user_message | What they tried to ask | "Show crew list" |
| attempted_action | What they wanted | unauthorized_access_blocked |
| chat_id | Telegram chat ID | -1234567890 |

---

## Reading the Logs

### Understanding Timestamps

**Format:** YYYY-MM-DD HH:MM:SS  
**Timezone:** Philippine Time (PHT, UTC+8)

**Example:** `2025-01-04 14:23:45`
- Year: 2025
- Month: January (01)
- Day: 4th
- Time: 2:23:45 PM

### Action Types

Common values in `action_type` column:

| Action Type | Meaning |
|-------------|---------|
| find_crew_member | User searched for a crew member |
| get_crew_details | User requested crew information |
| query_crew_lists | User requested lists or statistics |
| send_email_with_documents | User requested documents via email |
| conversation | General chat (no specific tool) |

### Filter Types

Values in `filter_type` column:

| Filter | Meaning |
|--------|---------|
| expired | Only expired documents |
| expiring | Documents expiring within 90 days |
| valid | Valid documents |
| all | All documents |

### Categories

Values in `categories` column:

| Category | Meaning |
|----------|---------|
| training | Training certificates |
| medical | Medical certificates |
| license | Licenses |
| document | General documents |
| all | All categories |

---

## Common Analysis Tasks

### Finding User Activity

**To see what a specific user did:**

1. Click on column **B** (telegram_user_id)
2. Data → Create a filter
3. Click filter icon on column B
4. Select the user ID you want
5. View all their activities

**Or use this formula in a new sheet:**
```
=QUERY(Sheet1!A:L, "SELECT * WHERE B = 2061662252 ORDER BY A DESC")
```
Replace `2061662252` with the actual user ID.

### Finding Email Deliveries

**To see all documents sent via email:**

1. Filter column **E** (action_type)
2. Select: `send_email_with_documents`
3. View column **H** (recipient_email) to see where sent

**Or use formula:**
```
=QUERY(Sheet1!A:L, "SELECT * WHERE E = 'send_email_with_documents' ORDER BY A DESC")
```

### Daily Activity Summary

**To count requests per day:**

1. Create pivot table:
   - Data → Pivot table
   - Rows: Add `timestamp` (grouped by day)
   - Values: Add `timestamp` (COUNTA)
2. Shows requests per day

### Most Active Users

**To find who uses the system most:**

1. Create pivot table:
   - Rows: Add `telegram_first_name`
   - Values: Add `telegram_user_id` (COUNTA)
   - Sort by count descending
2. Shows usage by user

### Error Tracking

**To find failed requests:**

1. Filter column **K** (status)
2. Select any value that isn't "success"
3. Review errors

### Document Request Analysis

**To see what documents are requested most:**

1. Filter for `send_email_with_documents` in action_type
2. Review `filter_type` and `categories` columns
3. Create pivot table to summarize

---

## Security Monitoring

### Monitoring Unauthorized Access

**Daily check:**

1. Open **Unauthorize_Access** sheet
2. Look for new entries
3. Note any patterns:
   - Repeated attempts from same user_id
   - Attempts at unusual hours
   - Multiple unknown users

**Red flags:**

- Same unknown user trying repeatedly (>5 times)
- Attempts at night (2am-5am PHT)
- Sudden spike in unauthorized attempts
- Users with suspicious first names

### Investigation Process

**If you see suspicious activity:**

1. **Document the incident:**
   - Note the timestamp
   - Record the user_id
   - Copy the user_message
   - Check if pattern exists

2. **Check if legitimate:**
   - Is this a new employee trying to access?
   - Did someone forget their authorized account?
   - Contact the telegram_first_name if known

3. **Take action if malicious:**
   - Report to security team
   - Monitor for continued attempts
   - Consider temporarily disabling bot if under attack

### Weekly Security Review

**Every Monday morning:**

1. Check Unauthorize_Access for past week
2. Verify no new authorized users needed
3. Confirm no security incidents
4. Report any anomalies to management

---

## Data Retention

### Current Storage

**Google Sheets limits:**
- Maximum 10 million cells per sheet
- Typical capacity: ~100,000 log entries
- At average usage: ~6-12 months before full

### When Sheets Get Full

**Option 1: Archive to new sheet**

1. Create new sheet: `IRIS_Audit_Log_Archive_2025`
2. Copy older data (>6 months) to archive
3. Delete from main sheet
4. Keep archive accessible for compliance

**Option 2: Export and archive**

1. File → Download → CSV
2. Save as: `IRIS_Audit_Log_2025_Q1.csv`
3. Store in secure location
4. Clear old data from sheet

**Recommended schedule:**
- Archive quarterly (every 3 months)
- Keep last 6 months in active sheet
- Retain archives for 3 years minimum

### Backup Procedure

**Monthly backup:**

1. File → Download → Microsoft Excel (.xlsx)
2. Name: `IRIS_Audit_Backup_YYYY-MM.xlsx`
3. Store in:
   - Company backup server
   - Google Drive backup folder
   - Encrypted offline storage

---

## Compliance and Privacy

### Data Privacy

**Personal data in logs:**
- Telegram user IDs
- User first names
- Email addresses
- Crew member names
- Document details

**Privacy requirements:**
- Limit access to authorized personnel only
- Do not share logs publicly
- Redact personal info if sharing for analysis
- Follow company data privacy policy

### Compliance Uses

**Audit logs support:**

1. **Security Audits**
   - Who accessed what data
   - When access occurred
   - What was done with data

2. **Compliance Reviews**
   - Document access history
   - Email delivery proof
   - User activity verification

3. **Incident Investigation**
   - Timeline of events
   - User actions during incident
   - System response verification

4. **Usage Reporting**
   - System adoption metrics
   - Feature usage statistics
   - Resource planning data

### Legal Considerations

**Log retention requirements:**
- Check local regulations
- Follow company policy
- Minimum 1 year recommended
- 3-5 years for compliance-critical industries

**Audit trail integrity:**
- Do not delete entries (archive instead)
- Do not modify historical data
- Document any corrections in separate notes
- Maintain chain of custody

---

## Sample Queries and Reports

### Query 1: User Activity Report

**Find all activity for a specific user in the last 30 days:**

```
=QUERY(Sheet1!A:L, 
  "SELECT A, D, E, G 
   WHERE B = 2061662252 
   AND A >= date '" & TEXT(TODAY()-30, "yyyy-mm-dd") & "' 
   ORDER BY A DESC"
)
```

Result shows: timestamp, message, action, crew searched

### Query 2: Email Delivery Report

**List all emails sent in the last week:**

```
=QUERY(Sheet1!A:L, 
  "SELECT A, C, G, H, I, J 
   WHERE E = 'send_email_with_documents' 
   AND A >= date '" & TEXT(TODAY()-7, "yyyy-mm-dd") & "' 
   ORDER BY A DESC"
)
```

Result shows: when, who, crew, email, filter, categories

### Query 3: Error Log

**Find all failed operations:**

```
=QUERY(Sheet1!A:L, 
  "SELECT A, C, D, E, K, L 
   WHERE K <> 'success' 
   ORDER BY A DESC 
   LIMIT 50"
)
```

Result shows: when, who, message, action, status, response

### Query 4: Daily Statistics

**Count of requests per day:**

```
=QUERY(Sheet1!A:L, 
  "SELECT TODATE(A), COUNT(A) 
   GROUP BY TODATE(A) 
   ORDER BY TODATE(A) DESC 
   LABEL TODATE(A) 'Date', COUNT(A) 'Requests'"
)
```

### Query 5: Top Users This Month

**Most active users in current month:**

```
=QUERY(Sheet1!A:L, 
  "SELECT C, COUNT(B) 
   WHERE MONTH(A) = " & MONTH(TODAY()) & " 
   AND YEAR(A) = " & YEAR(TODAY()) & " 
   GROUP BY C 
   ORDER BY COUNT(B) DESC 
   LABEL C 'User', COUNT(B) 'Requests'"
)
```

---

## Troubleshooting

### Logs Not Updating

**Symptom:** New requests not appearing in sheet

**Possible causes:**
1. Google Sheets OAuth expired
2. Sheet permissions changed
3. Workflow error

**Check:**
1. n8n → Executions → Look for log errors
2. Verify sheet ID in workflow matches
3. Check Google Sheets credential in n8n

**Fix:**
- Reconnect Google Sheets OAuth in n8n
- Verify edit permissions on sheet
- Check workflow is active

### Cannot Access Sheet

**Symptom:** "You need permission" error

**Fix:**
1. Request access from sheet owner
2. Verify you're using correct Google account
3. Contact administrator to add your email

### Sheet Loading Slowly

**Cause:** Too much data (>50,000 rows)

**Solutions:**
1. Archive old data
2. Use filters instead of scrolling
3. Create summary sheets with queries
4. Export to Excel for analysis

---

## Best Practices

### Regular Reviews

**Daily (if high usage):**
- Check for unauthorized access attempts
- Verify no critical errors

**Weekly:**
- Review usage patterns
- Check for anomalies
- Verify email deliveries successful

**Monthly:**
- Generate usage reports
- Archive if needed
- Backup logs
- Review security incidents

### Data Management

**Do:**
- Keep logs organized
- Archive regularly
- Backup monthly
- Document any issues

**Don't:**
- Delete entries
- Modify historical data
- Share logs publicly
- Store unencrypted backups

### Access Control

**Who should have access:**
- System administrators (edit access)
- Compliance officers (view access)
- Security team (view access)
- Management (view access - summary only)

**Who should not have access:**
- Regular users
- External parties
- Unauthorized personnel

---

## Support

**For assistance with audit logs:**
- System Administrator: [contact info]
- Compliance Officer: [contact info]
- IT Support: [contact info]

**For technical issues:**
- Check n8n workflow executions
- Verify Google Sheets connection
- Review runbook troubleshooting section

---

**Document Information**

- Version: 1.0
- Last Updated: October 2025
- Next Review: January 2026
- Owner: System Administrator
