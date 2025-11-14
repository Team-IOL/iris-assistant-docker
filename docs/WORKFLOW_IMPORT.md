# Workflow Import Guide

Complete step-by-step guide for importing IRIS Assistant workflows into n8n.

---

## Prerequisites

Before importing workflows, ensure:

- ✅ n8n is running and accessible at http://localhost:5678
- ✅ You can login to n8n interface
- ✅ All 5 credentials are configured (see CREDENTIAL_SETUP.md)
- ✅ Workflow JSON files are downloaded from the `workflows/` directory

---

## Import Order

⚠️ **CRITICAL:** Import workflows in this exact sequence to avoid dependency errors.
```
1. Find-Crew-Member-Tool.json (Independent)
2. Get-Crew-Details-Tool.json (Independent)
3. Find-Available-Documents.json (Independent)
4. Query-Crew-Lists-Tool.json (Independent)
5. IRIS-AI-Assistant.json (Depends on 1-4) ← IMPORT LAST
6. Weekly-Access-Report.json (Independent)

```

---

## Step-by-Step Import Process

### Step 1: Access n8n Workflows Page

1. Open your browser and go to [**http://localhost:5678**](http://localhost:5678)
2. Login with your credentials
3. Click on **"Workflows"** in the left sidebar
4. You should see an empty workflow list (or existing workflows)

### Step 2: Import First Workflow (Find-Crew-Member-Tool)

**Import the file:**
1. Click the **"+"** button or **"Add Workflow"** in the top right
2. Click the **three-dot menu** (⋮) in the top right corner
3. Select **"Import from File"**
4. Click **"Select file..."**
5. Choose `Find-Crew-Member-Tool.json` from your computer
6. Click **"Import"**

**Configure credentials:**
1. You'll see the workflow canvas with nodes
2. Look for any **red warning triangles** on nodes (indicates missing credentials)
3. Click on the **MySQL node** (labeled "MySQL")
4. In the right panel, find **"Credential to connect with"**
5. Select **"IRIS_Database"** from the dropdown
6. Click **"Save"** at the bottom right

**Activate webhook:**
1. Find the **Webhook node** at the beginning
2. Click on it
3. Note: The webhook URL will be auto-generated (different from placeholder)
4. Copy the **Production URL** for reference
5. Click **"Save"** (bottom right of workflow)

**Name and activate:**
1. The workflow name should be "Find Crew Member Tool"
2. Click **"Active"** toggle in the top right (turn it ON)
3. You should see "Active" in green

### Step 3: Import Second Workflow (Get-Crew-Details-Tool)

**Import the file:**
1. Go back to Workflows page (click "Workflows" in left sidebar)
2. Click **"Add Workflow"** → **"Import from File"**
3. Select `Get-Crew-Details-Tool.json`
4. Click **"Import"**

**Configure credentials:**
1. Look for nodes with red warning triangles
2. This workflow has **3 MySQL nodes** that need credentials
3. Click on each MySQL node:
   - **"Query Personal Info"** → Select "IRIS_Database"
   - **"Query Documents"** → Select "IRIS_Database"
   - **"Query Medical Info"** → Select "IRIS_Database"
4. Save each node after selecting credentials

**Save and activate:**
1. Click **"Save"** (bottom right)
2. Click **"Active"** toggle (turn it ON)

### Step 4: Import Third Workflow (Find-Available-Documents)

**Import the file:**
1. Workflows page → **"Add Workflow"** → **"Import from File"**
2. Select `Find-Available-Documents.json`
3. Click **"Import"**

**Configure credentials:**
1. Click on each node with red triangle:
   - **MySQL nodes** → Select "IRIS_Database"
   - **Mailjet node** → Select "Mailjet_Email"
   - **Google Sheets node** → Select "Google_Sheets"
2. Save each node

**Save and activate:**
1. Click **"Save"**
2. Click **"Active"** toggle

### Step 5: Import Fourth Workflow (Query-Crew-Lists-Tool)

**Import the file:**
1. Workflows page → **"Add Workflow"** → **"Import from File"**
2. Select `Query-Crew-Lists-Tool.json`
3. Click **"Import"**

**Configure credentials:**
1. This workflow has multiple MySQL nodes and routing logic
2. Click through each node with credentials:
   - All **MySQL nodes** → "IRIS_Database"
   - **Mailjet nodes** → "Mailjet_Email"
   - **Google Sheets nodes** → "Google_Sheets"
3. Save each node

**Save and activate:**
1. Click **"Save"**
2. Click **"Active"** toggle

### Step 6: Import Main Workflow (IRIS-AI-Assistant) ⚠️

**⚠️ IMPORTANT:** Import this workflow LAST after all tool workflows are imported and active.

**Import the file:**
1. Workflows page → **"Add Workflow"** → **"Import from File"**
2. Select `IRIS-AI-Assistant.json`
3. Click **"Import"**

**Configure credentials:**
This is the main workflow with the most credentials to configure:

1. **Telegram Bot nodes** → Select "Telegram_Bot"
2. **Anthropic Chat Model node** → Select "Anthropic_Claude"
3. **Google Sheets nodes** → Select "Google_Sheets"
4. **MySQL node** (if any) → Select "IRIS_Database"

**Configure authorized users:**
1. Find the **"Authorization Check"** node (usually near the beginning)
2. Click on it
3. In the code/expression field, you'll see a list of Telegram user IDs
4. Replace the placeholder IDs with your team's actual Telegram IDs

```
// Example - replace with your actual user IDs
const authorizedUsers = ;
```

5. To find your Telegram ID: Message [@userinfobot](https://t.me/userinfobot) on Telegram

**Configure Google Sheets:**
1. Find **"Track Activity"** or **"Log to Sheets"** nodes
2. Click on each one
3. Replace the placeholder Sheet ID with your actual Google Sheet ID
4. Format: `1OoOyAtghGqQ5GxiUWbTTWiwesZyPDKTKxGbPsi5455Q` (from your Sheet URL)

**Configure Execute Workflow nodes:**
1. This workflow calls other workflows using "Execute Workflow" nodes
2. Click on each **"Execute Workflow"** node
3. In the dropdown, select the corresponding workflow:
- Find Crew Member → "Find Crew Member Tool"
- Get Crew Details → "Get Crew Details Tool"
- Find Documents → "Find Available Documents"
- Query Lists → "Query Crew Lists Tool"

**Save and activate:**
1. Click **"Save"**
2. Click **"Active"** toggle (turn it ON)
3. The main assistant is now ready!

### Step 7: Import Weekly Report Workflow (Optional)

**Import the file:**
1. Workflows page → **"Add Workflow"** → **"Import from File"**
2. Select `Weekly-Access-Report.json`
3. Click **"Import"**

**Configure credentials:**
1. **Google Sheets node** → Select "Google_Sheets"
2. **Mailjet node** → Select "Mailjet_Email"

**Configure schedule:**
1. Find the **"Schedule Trigger"** node
2. Click on it
3. Default: Fridays at 6:00 PM
4. Modify if needed using the schedule settings

**Configure Google Sheet:**
1. Find the **"Get Audit Data"** node
2. Replace Sheet ID with your actual audit log sheet

**Configure recipients:**
1. Find the **"Send Email"** node
2. Update recipient email addresses

**Save and activate:**
1. Click **"Save"**
2. Click **"Active"** toggle if you want automated reports

---

## Post-Import Verification

### Test Each Workflow

**1. Test Find Crew Member Tool:**
- Use the webhook URL in a REST client (Postman, Insomnia)
- Send a POST request with:
```
{
"search_query": "Juan dela Cruz"
}
```
- Should return crew member data

**2. Test IRIS AI Assistant:**
- Open Telegram and message your bot
- Send: "Hello"
- Should receive welcome message
- Try: "Find Juan dela Cruz"
- Should receive crew information

**3. Check Execution History:**
- Go to "Executions" in left sidebar
- Should see successful executions (green checkmarks)
- Click on any execution to see detailed logs

### Common Import Issues

**Issue 1: "Credential not found" errors**
- **Solution:** Make sure all credentials are created with exact names
- Expected names: `IRIS_Database`, `Telegram_Bot`, `Anthropic_Claude`, `Mailjet_Email`, `Google_Sheets`

**Issue 2: "Workflow not found" in Execute Workflow nodes**
- **Solution:** Ensure tool workflows are imported and saved BEFORE the main workflow
- Re-select the workflows in Execute Workflow dropdowns

**Issue 3: Webhook URLs not working**
- **Solution:** Webhooks are auto-generated on import
- Test webhooks are at: `http://localhost:5678/webhook-test/...`
- Production webhooks are at: `http://localhost:5678/webhook/...`

**Issue 4: Google Sheets access denied**
- **Solution:** Make sure OAuth consent screen is configured
- Re-authenticate the Google Sheets credential

---

## Workflow Activation Checklist

Before going live, verify:

- [ ] All 6 workflows imported successfully
- [ ] All credentials configured and tested
- [ ] Authorized Telegram user IDs updated
- [ ] Google Sheet IDs replaced with actual sheets
- [ ] Execute Workflow nodes point to correct workflows
- [ ] Webhook URLs are accessible
- [ ] Database connection works (test query)
- [ ] Email sending works (send test email)
- [ ] Telegram bot responds to messages
- [ ] No red warning triangles on any nodes
- [ ] All workflows set to "Active"

---

## Troubleshooting

For detailed troubleshooting, see [TROUBLESHOOTING.md](TROUBLESHOOTING.md)

Quick fixes:
- **Workflow won't save:** Check for validation errors in nodes
- **Credentials not showing:** Refresh the page or re-login
- **Import fails:** Check JSON file is not corrupted
- **Can't activate workflow:** Ensure all required credentials are set

---

## Next Steps

After successful import:
1. Review [DEPLOYMENT.md](DEPLOYMENT.md) for production deployment
2. Test thoroughly in development environment
3. Monitor execution logs for errors
4. Configure backup schedule for workflows

---

## Support

For additional help:
- Check n8n documentation: https://docs.n8n.io
- Review execution logs in n8n interface
- Check Docker logs: `docker-compose logs n8n`
