
# IRIS Assistant Workflows

This directory contains all n8n workflow JSON files for the IRIS Assistant system.

## Workflow Files

### 1. IRIS-AI-Assistant.json
**Main workflow** - AI-powered Telegram bot for crew management queries.

**Features:**
- Claude Sonnet 4 AI integration
- Natural language crew search
- Document retrieval and email delivery
- Activity logging to Google Sheets
- Telegram HTML formatted responses

**Required Credentials:**
- Anthropic API (Claude Sonnet 4)
- Telegram Bot API
- Google Sheets OAuth2
- MySQL Database

---

### 2. Find-Crew-Member-Tool.json
**Search tool** - Fuzzy search for crew members by name or ID.

**Features:**
- Webhook-based tool endpoint
- Fuzzy name matching
- Returns applicant ID and full name

**Required Credentials:**
- MySQL Database

---

### 3. Get-Crew-Details-Tool.json
**Data retrieval** - Fetches detailed crew information and documents.

**Features:**
- Personal information retrieval
- Document list with AWS S3 links
- Multiple info types supported (personal, documents)

**Required Credentials:**
- MySQL Database

---

### 4. Find-Available-Documents.json
**Document manager** - Filters and emails crew documents by category.

**Features:**
- Document categorization (Documents, Licenses, Training, Vaccines, Visas, Flags, Medical)
- SQL deduplication logic
- HTML email formatting via Mailjet
- Medical document privacy protection

**Required Credentials:**
- MySQL Database
- Mailjet Email API
- Google Sheets OAuth2

---

### 5. Query-Crew-Lists-Tool.json
**Reporting tool** - Generates crew lists and statistical reports.

**Features:**
- Expiring documents report (within X days)
- Deployed crew list
- Available crew list
- Status-based filtering
- Excel/CSV export via email

**Required Credentials:**
- MySQL Database
- Mailjet Email API
- Google Sheets OAuth2

---

### 6. Weekly-Access-Report.json
**Automated reporting** - Weekly audit log delivery.

**Features:**
- Scheduled trigger (Fridays at 6 PM)
- Google Sheets data export
- CSV attachment generation
- Email delivery to stakeholders

**Required Credentials:**
- Google Sheets OAuth2
- Mailjet Email API

## Import Order

⚠️ **IMPORTANT:** Import workflows in this sequence to satisfy dependencies:

1. Find-Crew-Member-Tool.json
2. Get-Crew-Details-Tool.json
3. Find-Available-Documents.json
4. Query-Crew-Lists-Tool.json
5. **IRIS-AI-Assistant.json** (main - import last)
6. Weekly-Access-Report.json

## Post-Import Configuration

After importing each workflow, you must:

1. **Replace all credential placeholders** with your actual credentials
2. **Update webhook URLs** (production vs. test)
3. **Configure Google Sheet IDs** (for audit logging)
4. **Update authorized Telegram user IDs** (in IRIS-AI-Assistant)
5. **Verify database connection** for all workflows

See `../docs/WORKFLOW_IMPORT.md` for detailed instructions.

## Security Notes

- All credential IDs have been removed from these files
- Webhook IDs must be regenerated on import
- Google Sheet IDs are placeholders
- MySQL credentials must be configured in n8n
- Never commit files with actual credentials

## File Modifications

If you modify these workflows:
- Always export with "Export with IDs" disabled
- Remove credentials before committing
- Update this README with changes
- Increment version number in CHANGELOG.md

## Support

For issues with workflow import or configuration, refer to:
- `../docs/WORKFLOW_IMPORT.md` - Import guide
- `../docs/CREDENTIAL_SETUP.md` - Credential configuration
- `../docs/TROUBLESHOOTING.md` - Common issues

