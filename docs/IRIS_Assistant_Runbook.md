# IRIS Assistant - Operations Runbook

**Version:** 1.0  
**Last Updated:** October 2025  
**System:** IRIS AI Assistant - Crew Management Bot

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Daily Operations](#daily-operations)
3. [Starting & Stopping](#starting--stopping)
4. [Credential Management](#credential-management)
5. [Adding Authorized Users](#adding-authorized-users)
6. [Monitoring](#monitoring)
7. [Troubleshooting](#troubleshooting)
8. [Maintenance Tasks](#maintenance-tasks)
9. [Emergency Procedures](#emergency-procedures)

---

## System Overview

### Architecture

```
Telegram User → Telegram Bot API → n8n Workflows → MySQL Database
                                   ↓
                                   ├─→ Mailjet (Email)
                                   ├─→ OpenAI (AI Processing)
                                   └─→ Google Sheets (Audit Logs)
```

### Components

| Component | Purpose | Location |
|-----------|---------|----------|
| n8n | Workflow automation platform | Docker container |
| MySQL Database | Crew data storage | 44.233.4.43 (read-only) |
| Telegram Bot | User interface | @your_bot_name |
| Mailjet | Email delivery | mailjet.com |
| OpenAI | Natural language processing | platform.openai.com |
| Google Sheets | Audit logging | Google Drive |

### Workflows

1. **IRIS AI Assistant** (Main workflow)
   - Receives Telegram messages
   - Routes to appropriate tools
   - Returns responses

2. **Find Crew Member Tool**
   - Searches crew by name
   - Returns applicant_id

3. **Get Crew Details Tool**
   - Retrieves individual crew information
   - Shows personal, deployment, or document data

4. **Query Crew Lists Tool**
   - Handles bulk queries and statistics
   - Auto-emails reports for large results

5. **Send Email with Documents Tool**
   - Sends filtered crew documents via email
   - Supports expired, expiring, valid, or all documents

---

## Daily Operations

### Normal Operation Checklist

**Every Morning:**
- Check audit logs for overnight activity
- Verify no failed executions in n8n
- Check OpenAI usage (shouldn't exceed daily budget)
- Verify Mailjet email quota

**Every Week:**
- Review unauthorized access attempts
- Check for workflow errors
- Verify database connection is active

**Every Month:**
- Review OpenAI costs
- Backup n8n workflows
- Update authorized user list if needed

### Accessing n8n

1. Open browser: `http://localhost:5678` (or your configured URL)
2. Login with credentials from `.env` file
3. Dashboard shows recent executions

---

## Starting & Stopping

### Starting the System

**From command line:**

```bash
# Navigate to project directory
cd /path/to/IRIS_Assistant_Docker

# Start containers
docker-compose up -d

# Verify it's running
docker-compose ps

# Check logs
docker-compose logs -f n8n
```

**Expected output:**
```
✓ Container iris_assistant_n8n Started
n8n is now running on http://localhost:5678
```

**Wait 30 seconds** before accessing n8n web interface.

### Stopping the System

**Graceful shutdown:**
```bash
docker-compose down
```

**Force stop (if needed):**
```bash
docker-compose kill
docker-compose down
```

### Restarting the System

```bash
# Restart without rebuilding
docker-compose restart

# Full restart with rebuild
docker-compose down
docker-compose up -d
```

### Activating Workflows

After starting, activate the main workflow:

1. Login to n8n web interface
2. Go to **Workflows**
3. Open **"IRIS AI Assistant"**
4. Toggle **"Active"** switch to ON (blue)
5. Verify "Last execution" updates when you test

**Note:** Sub-workflows (tools) don't need to be activated separately.

---

## Credential Management

### Viewing Current Credentials

1. Login to n8n
2. Click **Settings** (gear icon)
3. Click **Credentials**
4. See list of all configured credentials

**You should have 5 credentials:**
- IRIS_Database (MySQL)
- Telegram_Bot (Telegram API)
- Mailjet Email account (Mailjet)
- OpenAi account (OpenAI)
- Google Sheets account (Google OAuth)

### Rotating MySQL Password

**When:** Database admin changes password

**Steps:**
1. Get new password from database admin
2. n8n → Settings → Credentials
3. Find "IRIS_Database"
4. Click to edit
5. Update Password field
6. Click "Test" to verify connection
7. Click "Save"
8. Test by sending bot query

**Verify:** No database connection errors in workflow executions

### Rotating Telegram Bot Token

**When:** Security incident or annual rotation

**Steps:**
1. Open Telegram, message @BotFather
2. Send: `/token`
3. Select your bot
4. Send: `/revoke`
5. Confirm revocation
6. BotFather gives new token
7. n8n → Settings → Credentials
8. Find "Telegram_Bot"
9. Update Access Token
10. Save
11. Test by sending message to bot

**Downtime:** Bot will be offline during this process (~2 minutes)

### Rotating Mailjet API Keys

**When:** Annual rotation or security incident

**Steps:**
1. Login to Mailjet account
2. Account Settings → API Key Management
3. Click "Create New API Key"
4. Copy new API Key and Secret
5. n8n → Settings → Credentials
6. Find "Mailjet Email account"
7. Update both API Key and API Secret
8. Save
9. Test by requesting a document email

**Old keys:** Deactivate after confirming new ones work

### Rotating OpenAI API Key

**When:** Annual rotation or budget control

**Steps:**
1. Login to platform.openai.com
2. Go to API Keys section
3. Create new key: "IRIS Assistant v2"
4. Copy the key (starts with sk-)
5. n8n → Settings → Credentials
6. Find "OpenAi account"
7. Update API Key
8. Save
9. Delete old key from OpenAI dashboard
10. Test bot query that uses AI

### Refreshing Google Sheets OAuth

**When:** Token expires (usually after 6 months) or "Authorization Failed" errors

**Steps:**
1. n8n → Settings → Credentials
2. Find "Google Sheets account"
3. Click "Reconnect"
4. Login with Google account
5. Grant permissions
6. Save
7. Test by sending bot query (check audit log updates)

---

## Adding Authorized Users

### Getting User's Telegram ID

**Option 1: Using @userinfobot**
1. User opens Telegram
2. Searches for @userinfobot
3. Sends /start
4. Bot replies with user ID (number like: 123456789)
5. User sends you this number

**Option 2: Checking Unauthorized Access Log**
1. Check Google Sheets → Unauthorize_Access tab
2. Find recent attempt
3. Copy telegram_user_id

### Adding User to Whitelist

1. Login to n8n
2. Go to **Workflows**
3. Open **"IRIS AI Assistant"**
4. Find node: **"Authorization Check"**
5. Click to open node settings
6. You'll see conditions like:
   ```
   User ID equals 2061662252
   User ID equals 7993232859
   User ID equals 2105346661
   ```
7. Click **"Add Condition"**
8. Set:
   - Field: `{{ $json.user_id }}`
   - Operation: `equals`
   - Value: `[paste new user ID here]`
9. Make sure condition is connected with **"OR"**
10. Click outside node to close
11. Click **"Save"** (top right)
12. Workflow automatically updates (it's already active)

### Testing New User Access

1. New user sends message to bot: "Hello"
2. Should receive response (not "Access Denied")
3. Check audit log - should appear in authorized sheet
4. If still denied: verify user ID is correct

### Removing User Access

1. Open "Authorization Check" node
2. Find condition with that user's ID
3. Click **X** to delete condition
4. Save workflow
5. User immediately loses access

---

## Monitoring

### Checking Workflow Executions

**View Recent Activity:**
1. n8n → Executions (left sidebar)
2. See list of all executions
3. Green checkmark = success
4. Red X = failed

**Filtering:**
- Click "Status" dropdown → select "Error" to see only failures
- Use date range to filter by time

**Viewing Details:**
1. Click on any execution
2. See complete flow with data
3. Red nodes show where failure occurred
4. Click node to see error message

### Audit Logs

**Location:** Google Sheets → IRIS_Audit_Log

**Sheet1 (Authorized Access):**
- Shows all successful queries
- Check `action_type` column for what tools were used
- Check `recipient_email` for email deliveries
- Review `bot_response` for what was sent

**Unauthorize_Access Sheet:**
- Shows blocked access attempts
- Monitor for repeated attempts (possible attack)
- Verify only known users appear here during onboarding

**Red Flags:**
- Same unknown user_id appearing multiple times
- Access attempts at unusual hours (2am-5am)
- Repeated failed attempts from same user

### Monitoring OpenAI Usage

1. Login to platform.openai.com
2. Go to **Usage**
3. Check daily/monthly costs
4. Set up usage alerts:
   - Settings → Limits
   - Set monthly budget (recommended: $50)
   - Set email alerts at 50%, 75%, 100%

**Normal usage:** $1-5 per day depending on query volume

**High usage triggers:**
- Spike in conversations
- Long back-and-forth exchanges
- Complex queries requiring multiple AI calls

### Email Delivery Monitoring

1. Login to Mailjet
2. Dashboard shows:
   - Emails sent today
   - Delivery rate
   - Bounce rate
   - Spam complaints

**Healthy metrics:**
- Delivery rate: >95%
- Bounce rate: <5%
- Spam complaints: 0

### Setting Up Alerts

**n8n Workflow Errors:**
1. n8n → Settings → Log Streaming
2. Configure webhook to send errors to Slack/email
3. Or check Executions daily manually

**Database Connection:**
- Test query daily: "How many crew on-board?"
- If fails: database connection issue

**Mailjet:**
- Enable email notifications in Mailjet for bounces
- Account Settings → Notifications

---

## Troubleshooting

### Bot Not Responding

**Symptom:** User sends message, no response

**Check:**
1. Is workflow active?
   - n8n → Workflows → IRIS AI Assistant
   - Toggle should be blue/ON

2. Is container running?
   ```bash
   docker-compose ps
   ```
   Should show "Up"

3. Check recent executions
   - n8n → Executions
   - Look for errors in last hour

4. Test webhook:
   - Send simple message: "Hello"
   - Check if execution appears
   - If no execution: webhook issue

**Fix:**
```bash
# Restart container
docker-compose restart

# Check logs for errors
docker-compose logs -f n8n
```

### "Access Denied" for Authorized User

**Check:**
1. Get user's actual Telegram ID
   - Ask them to message @userinfobot
2. Compare with ID in Authorization Check node
3. Common issue: User ID entered as string "123" instead of number 123

**Fix:**
- Edit Authorization Check node
- Make sure user ID has no quotes
- Save and test

### Database Connection Failed

**Error:** "ER_ACCESS_DENIED_ERROR" or "Can't connect to MySQL"

**Causes:**
1. MySQL password changed
2. Database server down
3. Network issue
4. IP address blocked

**Fix:**
1. Test connection manually:
   ```bash
   docker exec -it iris_assistant_n8n ping 44.233.4.43
   ```

2. Verify credentials:
   - n8n → Settings → Credentials → IRIS_Database
   - Click "Test"
   - Should show "Connection successful"

3. Contact database admin if test fails

### Email Not Sending

**Error:** "Mailjet API Error" or emails not received

**Check:**
1. Mailjet credentials valid?
   - n8n → Settings → Credentials → Mailjet Email account
   - Verify API keys

2. Check Mailjet dashboard:
   - Any emails in "Queued" status?
   - Bounces or blocks?

3. Verify sender email verified in Mailjet

**Fix:**
- If credentials expired: rotate keys
- If sender not verified: verify in Mailjet
- If recipient blocking: check spam folder

### AI Not Understanding Queries

**Symptom:** Bot gives generic responses or wrong results

**Causes:**
1. OpenAI API key expired
2. Budget limit reached
3. Prompt/context issue

**Check:**
1. OpenAI dashboard → Usage
   - Any API errors?
   - Budget exceeded?

2. n8n → Credentials → OpenAi account
   - Test if key is valid

**Fix:**
- If budget issue: increase limit or wait for reset
- If key expired: rotate key
- If persistent: check AI Agent node configuration

### Google Sheets Not Logging

**Symptom:** Audit log not updating

**Causes:**
1. OAuth token expired
2. Sheet permissions changed
3. Sheet deleted/renamed

**Check:**
1. n8n → Executions → find failed log attempt
2. Error message will indicate issue

**Fix:**
1. Refresh OAuth:
   - Settings → Credentials → Google Sheets account
   - Reconnect

2. Verify sheet ID in workflow matches actual spreadsheet

3. Check sheet permissions (edit access required)

### Container Won't Start

**Error:** Container exits immediately

**Check logs:**
```bash
docker-compose logs n8n
```

**Common issues:**
1. Port 5678 already in use
   - Change N8N_PORT in .env
   - Update docker-compose.yml ports

2. Invalid .env file
   - Check for syntax errors
   - Verify no special characters break format

3. Corrupt volume data
   ```bash
   # Last resort - removes all data
   docker-compose down -v
   docker-compose up -d
   ```

---

## Maintenance Tasks

### Weekly Backup

**Backup workflows:**
```bash
# Export all workflows from n8n UI
# Or backup entire data volume
docker run --rm \
  -v iris_assistant_docker_n8n_data:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/n8n_backup_$(date +%Y%m%d).tar.gz /data
```

**Store backups:** Keep last 4 weekly backups (1 month)

### Monthly Updates

**Update n8n Docker image:**
```bash
# Pull latest version
docker-compose pull

# Restart with new image
docker-compose down
docker-compose up -d

# Verify workflows still work
```

**Review and cleanup:**
1. Delete old executions (n8n keeps 7 days by default)
2. Archive old audit log data if sheet gets large
3. Review and remove deactivated users

### Quarterly Security Review

- Rotate all API keys
- Review authorized user list
- Check for any security alerts from services
- Update documentation if processes changed
- Test disaster recovery procedure

---

## Emergency Procedures

### Complete System Failure

**If n8n won't start at all:**

1. **Stop everything:**
   ```bash
   docker-compose down
   ```

2. **Check disk space:**
   ```bash
   df -h
   ```
   If full: clean up logs/temp files

3. **Restore from backup:**
   ```bash
   # Stop system
   docker-compose down -v
   
   # Restore data
   tar xzf n8n_backup_20250104.tar.gz
   
   # Restart
   docker-compose up -d
   ```

4. **Verify:**
   - Login to n8n
   - Check workflows are active
   - Test with sample query

### Database Compromised

**If read-only user is compromised:**

1. Contact database admin immediately
2. Request password rotation
3. Update credential in n8n
4. Review audit logs for suspicious activity
5. Check what data was accessed

### Telegram Bot Token Leaked

**If bot token is public:**

1. Immediately revoke token via @BotFather
2. Create new bot token
3. Update credential in n8n
4. Review Unauthorize_Access logs
5. Notify security team

### Mass Unauthorized Access Attempts

**If many unknown users trying to access:**

1. Check Unauthorize_Access sheet
2. Identify pattern (same IP/time)
3. If attack: temporarily disable bot
   - n8n → deactivate IRIS AI Assistant workflow
4. Contact security team
5. Review access logs
6. Re-enable when safe

---

## Support Contacts

| Issue | Contact |
|-------|---------|
| n8n Platform | https://docs.n8n.io |
| Database Access | Database Admin |
| Telegram Issues | https://core.telegram.org/bots/faq |
| Mailjet Support | https://www.mailjet.com/support/ |
| OpenAI Issues | https://help.openai.com |

---

## Appendix: Quick Reference Commands

```bash
# Start system
docker-compose up -d

# Stop system
docker-compose down

# View logs
docker-compose logs -f n8n

# Restart system
docker-compose restart

# Check status
docker-compose ps

# Backup data
docker run --rm -v iris_assistant_docker_n8n_data:/data -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz /data

# Shell access to container (advanced)
docker exec -it iris_assistant_n8n sh
```

---

**Document Control**

- Maintained by: Operations Team
- Review Frequency: Quarterly
- Last Reviewed: October 2025
- Next Review: January 2026
