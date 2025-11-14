# Credential Setup Guide

Complete guide for configuring all credentials required for IRIS Assistant workflows.

---

## Overview

IRIS Assistant requires **5 credentials** to function properly:

1. ✅ **MySQL** - Database connection
2. ✅ **Telegram Bot API** - Chat interface
3. ✅ **Anthropic API** - AI language model (Claude Sonnet 4)
4. ✅ **Mailjet Email API** - Email delivery
5. ✅ **Google Sheets OAuth2** - Audit logging

---

## Prerequisites

Before setting up credentials, obtain:

- MySQL database connection details (host, port, database name, username, password)
- Telegram bot token from [@BotFather](https://t.me/botfather)
- Anthropic API key from [console.anthropic.com](https://console.anthropic.com)
- Mailjet API credentials from [app.mailjet.com](https://app.mailjet.com)
- Google Cloud project with Sheets API enabled

---

## Accessing Credentials Settings

1. Open n8n interface at [**http://localhost:5678**](http://localhost:5678)
2. Login with your credentials
3. Click **Settings** (gear icon) in the bottom left sidebar
4. Click **Credentials** in the settings menu
5. You'll see a list of existing credentials (if any)

---

## Credential 1: MySQL Database

### Step 1: Create Credential

1. Click **"Add Credential"** button
2. Search for **"MySQL"** in the search box
3. Click on **"MySQL"** to select it

### Step 2: Configure Connection

Enter the following details:
```
**Credential Name:**
IRIS_Database
```

⚠️ **Important:** Use this exact name!

```
**Host:**
your-mysql-host.example.com
```

(Provided by client/database administrator)
```
**Database:**
iris_db
```

(Or the actual database name containing crew data)
```
**User:**
readonly_user
```
(Read-only database user - recommended for security)
```
**Password:**
your-secure-password
```
```
**Port:**
3306
```
(Default MySQL port, unless different)

### Step 3: Additional Settings (Optional)

**SSL/TLS:**
- If your database requires SSL, toggle **"SSL"** to ON
- Usually required for production/cloud databases

**Connection Timeout:**
- Default: 10000 ms
- Increase if you have slow network: 30000 ms

### Step 4: Test & Save

1. Click **"Test"** button at the bottom
2. Should see: ✅ "Connection successful"
3. If error, verify host, credentials, and firewall rules
4. Click **"Save"** once test succeeds

### Troubleshooting MySQL Connection

**Error: "Connection refused"**
- Check if MySQL host is accessible from your Docker container
- Verify firewall allows connections from your IP
- Confirm port number (3306 is default)

**Error: "Access denied"**
- Verify username and password are correct
- Check if user has SELECT permissions on required tables
- Confirm user can connect from your host

**Error: "Unknown database"**
- Verify database name is correct
- Check if database exists: `SHOW DATABASES;`

---

## Credential 2: Telegram Bot API

### Step 1: Create Telegram Bot

**If you don't have a bot token yet:**

1. Open Telegram and search for **@BotFather**
2. Start a chat and send: `/newbot`
3. Follow prompts to name your bot
4. BotFather will provide a token like: `123456789:ABCdefGHIjklMNOpqrsTUVwxyz`
5. **Save this token securely** - you'll need it below

**Bot setup commands (send to BotFather):**
```
/setdescription - Set bot description
/setabouttext - Set about text
/setcommands - Set available commands
```

Example commands to set:
```
start - Start the assistant
help - Show help information
```

### Step 2: Create Credential in n8n

1. Click **"Add Credential"**
2. Search for **"Telegram"**
3. Select **"Telegram API"**

### Step 3: Configure

**Credential Name:**
```
Telegram_Bot
```
⚠️ **Important:** Use this exact name!
```
**Access Token:**
123456789:ABCdefGHIjklMNOpqrsTUVwxyz
```
(Paste your actual bot token from BotFather)

### Step 4: Test & Save

1. Click **"Test"** (if available)
2. Click **"Save"**

### Find Your Telegram User ID

To configure authorized users, you need your Telegram user ID:

1. Open Telegram
2. Search for **@userinfobot**
3. Start a chat and send: `/start`
4. Bot will reply with your user ID (e.g., 123456789)
5. Save this ID - you'll need it when configuring the main workflow

---

## Credential 3: Anthropic API (Claude)

### Step 1: Get API Key

1. Go to [console.anthropic.com](https://console.anthropic.com)
2. Create an account or login
3. Navigate to **"API Keys"** section
4. Click **"Create Key"**
5. Give it a name: `IRIS Assistant`
6. Copy the API key (starts with `sk-ant-api03-...`)
7. **Save this key securely** - it won't be shown again!

### Step 2: Add Funds (If Required)

- Anthropic requires prepaid credits
- Go to **"Billing"** section
- Add payment method and credits
- Recommended starting amount: $20-50

### Step 3: Create Credential in n8n

1. Click **"Add Credential"**
2. Search for **"Anthropic"**
3. Select **"Anthropic API"**

### Step 4: Configure

**Credential Name:**
```Anthropic_Claude
```
⚠️ **Important:** Use this exact name!

```
**API Key:**
sk-ant-api03-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```
(Paste your actual Anthropic API key)

### Step 5: Save

1. No test available for this credential
2. Click **"Save"**
3. Credential will be validated when workflow runs

### Model Configuration

The workflows use **Claude Sonnet 4** with these settings:
- Model: `claude-sonnet-4-20250514`
- Temperature: `0.1` (more focused/deterministic)
- Max Tokens: `4096`

These are pre-configured in the workflows - no need to change unless needed.

---

## Credential 4: Mailjet Email API

### Step 1: Create Mailjet Account

1. Go to [www.mailjet.com](https://www.mailjet.com)
2. Sign up for a free account
3. Verify your email address
4. Complete account setup

### Step 2: Get API Credentials

1. Login to Mailjet dashboard
2. Go to **"Account Settings"** → **"REST API"**
3. Click **"Master API Key & Sub API Key Management"**
4. You'll see:
   - **API Key** (public key)
   - **Secret Key** (private key)
5. Copy both keys

### Step 3: Configure Sender

1. In Mailjet dashboard, go to **"Account Settings"** → **"Sender Domains & Addresses"**
2. Add your domain: `adamsonphil.com`
3. Verify domain ownership (DNS records)
4. Add sender email: `noreply@adamsonphil.com`
5. Verify sender email (click verification link in email)

### Step 4: Create Credential in n8n

1. Click **"Add Credential"**
2. Search for **"Mailjet"**
3. Select **"Mailjet API"**

### Step 5: Configure

**Credential Name:**
```
Mailjet_Email
```
⚠️ **Important:** Use this exact name!

**API Key:**
```
your-mailjet-api-key
```
(32-character alphanumeric string)

**Secret Key:**
```
your-mailjet-secret-key
```
(32-character alphanumeric string)

### Step 6: Test & Save

1. Click **"Test"** button
2. Should see: ✅ "Connection successful"
3. Click **"Save"**

### Mailjet Limits

**Free Plan:**
- 6,000 emails per month
- 200 emails per day
- Limited to verified senders

**Upgrade if needed:**
- Higher volume requirements
- Better deliverability
- More features

---

## Credential 5: Google Sheets OAuth2

This is the most complex credential setup due to OAuth2 requirements.

### Step 1: Create Google Cloud Project

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project or select existing one
3. Name it: `IRIS Assistant`
4. Click **"Create"**

### Step 2: Enable Google Sheets API

1. In your project dashboard, click **"APIs & Services"** → **"Library"**
2. Search for **"Google Sheets API"**
3. Click on it
4. Click **"Enable"**

### Step 3: Configure OAuth Consent Screen

1. Go to **"APIs & Services"** → **"OAuth consent screen"**
2. Select **"External"** user type
3. Click **"Create"**

**Fill in the form:**
- **App name:** `IRIS Assistant`
- **User support email:** Your email
- **Developer contact:** Your email
- **Authorized domains:** (leave empty for testing)
- Click **"Save and Continue"**

**Scopes:**
- Click **"Add or Remove Scopes"**
- Search for: `https://www.googleapis.com/auth/spreadsheets`
- Select it
- Click **"Update"**
- Click **"Save and Continue"**

**Test users (for development):**
- Click **"Add Users"**
- Add your Gmail address
- Click **"Save and Continue"**

**Summary:**
- Review and click **"Back to Dashboard"**

### Step 4: Create OAuth2 Credentials

1. Go to **"APIs & Services"** → **"Credentials"**
2. Click **"Create Credentials"** → **"OAuth client ID"**
3. Application type: **"Web application"**
4. Name: `n8n IRIS Assistant`

**Authorized redirect URIs:**
```
Add this URI (replace with your n8n URL if different):
http://localhost:5678/rest/oauth2-credential/callback
```

For production (replace with your domain):
```
https://your-n8n-domain.com/rest/oauth2-credential/callback
```

5. Click **"Create"**
6. Copy **Client ID** and **Client Secret**
7. Click **"OK"**

### Step 5: Create Credential in n8n

1. In n8n, click **"Add Credential"**
2. Search for **"Google Sheets"**
3. Select **"Google Sheets OAuth2 API"**

### Step 6: Configure

**Credential Name:**
```
Google_Sheets
```
⚠️ **Important:** Use this exact name!

```
**Client ID:**
your-client-id.apps.googleusercontent.com
```
(From Google Cloud Console)

```
**Client Secret:**
your-client-secret
```
(From Google Cloud Console)

**OAuth Redirect URL:**
- This is auto-filled by n8n
- Should be: `http://localhost:5678/rest/oauth2-credential/callback`
- **Copy this URL** - you need to add it to Google Console (see Step 4)

### Step 7: Authenticate

1. Click **"Connect my account"** button
2. A Google login popup will appear
3. Select your Google account
4. Review permissions (read/write access to Google Sheets)
5. Click **"Allow"**
6. You'll be redirected back to n8n
7. Should see: ✅ "Successfully connected"

### Step 8: Save

1. Click **"Save"**
2. Credential is now ready to use

### Troubleshooting Google Sheets OAuth

**Error: "Redirect URI mismatch"**
- Verify the redirect URI in Google Console matches exactly what n8n shows
- No trailing slashes
- Correct protocol (http vs https)

**Error: "Access blocked: This app's request is invalid"**
- Make sure OAuth consent screen is configured
- Add your email to test users
- Enable Google Sheets API

**Error: "Token expired"**
- Re-authenticate by clicking "Reconnect"
- OAuth tokens can expire if not used

---

## Create Audit Log Google Sheet

For activity tracking, create a Google Sheet:

### Step 1: Create Sheet

1. Go to [sheets.google.com](https://sheets.google.com)
2. Click **"Blank"** to create new spreadsheet
3. Name it: `IRIS Audit Log`

### Step 2: Create Tabs

Create two tabs:
1. **Logs** - For general activity logging
2. **Access_Logs** - For weekly access reports

### Step 3: Set Up Headers

**In "Logs" tab, add headers in Row 1:**
- A1: `Timestamp`
- B1: `User ID`
- C1: `Username`
- D1: `Query`
- E1: `Action`
- F1: `Result`

**In "Access_Logs" tab, add headers in Row 1:**
- A1: `Week`
- B1: `User ID`
- C1: `Username`
- D1: `Total Queries`
- E1: `Most Common Action`

### Step 4: Get Sheet ID

1. Look at the URL of your Google Sheet
2. Format: `https://docs.google.com/spreadsheets/d/SHEET_ID_HERE/edit`
3. Copy the **SHEET_ID_HERE** part
4. Example: `1OoOyAtghGqQ5GxiUWbTTWiwesZyPDKTKxGbPsi5455Q`
5. Save this ID - you'll need it when configuring workflows

---

## Credential Summary

After completing all setups, you should have:

| Credential Name | Type | Used In |
|----------------|------|---------|
| `IRIS_Database` | MySQL | All workflows |
| `Telegram_Bot` | Telegram API | IRIS AI Assistant |
| `Anthropic_Claude` | Anthropic API | IRIS AI Assistant |
| `Mailjet_Email` | Mailjet API | Document & Report workflows |
| `Google_Sheets` | Google OAuth2 | Activity logging workflows |

---

## Testing Credentials

### Test MySQL

In n8n, create a test workflow:
1. Add a **MySQL node**
2. Select **IRIS_Database** credential
3. Query: `SELECT COUNT(*) as total FROM personal`
4. Execute
5. Should return row count

### Test Telegram

1. Open Telegram
2. Search for your bot
3. Send: `/start`
4. Should receive response (after workflows are imported)

### Test Anthropic

Will be tested when the main workflow runs - no standalone test available.

### Test Mailjet

In n8n, create a test workflow:
1. Add a **Mailjet node**
2. Select **Mailjet_Email** credential
3. Send a test email to yourself
4. Execute
5. Check your inbox

### Test Google Sheets

In n8n, create a test workflow:
1. Add a **Google Sheets node**
2. Select **Google_Sheets** credential
3. Select your audit log sheet
4. Read data from sheet
5. Execute
6. Should return sheet data

---

## Security Best Practices

✅ **Do:**
- Use read-only database credentials
- Rotate API keys regularly (every 90 days)
- Use strong, unique passwords
- Enable 2FA on all accounts
- Store credentials in n8n only (encrypted)
- Use environment variables for production

❌ **Don't:**
- Share credentials via email or chat
- Commit credentials to Git
- Use production credentials for testing
- Give full database access (use read-only)
- Leave default passwords unchanged

---

## Next Steps

After setting up all credentials:
1. Proceed to [WORKFLOW_IMPORT.md](WORKFLOW_IMPORT.md)
2. Import workflows in specified order
3. Test each workflow thoroughly
4. Review [DEPLOYMENT.md](DEPLOYMENT.md) for production deployment

---

## Support

For credential issues:
- Check service-specific documentation
- Verify API key validity in respective dashboards
- Review n8n credential documentation: https://docs.n8n.io/credentials/
- Check Docker logs: `docker-compose logs n8n`
