# IRIS Assistant - n8n Docker Deployment

**Version 2.0.0** | AI-powered Telegram assistant for crew management

## 🚀 What's New in v2.0

- **Enhanced AI Integration** - Upgraded to Claude Sonnet 4 for improved natural language understanding
- **Modular Workflow Architecture** - 6 specialized workflows for better maintainability
- **Advanced Document Management** - Categorized document filtering and retrieval
- **Professional Email Templates** - HTML-formatted email delivery via Mailjet
- **Comprehensive Audit Logging** - Google Sheets integration for activity tracking
- **Automated Weekly Reports** - Scheduled access reports for stakeholders

---

## 📋 Features

### Core Capabilities
- **Natural Language Queries** - Ask questions about crew members in plain English
- **Intelligent Crew Search** - Fuzzy name matching and ID-based lookups
- **Document Retrieval** - Find and email crew documents by category:
  - Personal Documents
  - Licenses & Certificates
  - Training Records
  - Vaccination Records
  - Visa Information
  - Flag Endorsements
  - Medical Records (view-only for privacy)
- **Statistical Reports** - Generate crew lists and expiring document reports
- **Email Delivery** - Professional HTML emails with document links
- **Activity Tracking** - Comprehensive audit logging to Google Sheets
- **Weekly Automation** - Automated access reports every Friday

### Security Features
- Authorized Telegram user validation
- Email domain whitelisting (adamsonphil.com, iol.ph, duck.com, tidal-solutions.com)
- Read-only database access
- Medical document privacy protection
- Secure credential management

---

## 🛠 Prerequisites

Before starting the installation, ensure you have:

- **Docker** and **Docker Compose** installed on your system
- **MySQL Database** access with the following:
  - Host, port, database name
  - Read-only user credentials
  - Required tables: `personal`, `applicant_documents`, `applicant_licenses`, `training`, `medical`, `applicant_vaccines`, `applicant_visas`, `applicant_flag_endorsements`
- **Telegram Bot Token** - Create a bot via [@BotFather](https://t.me/botfather)
- **Anthropic API Key** - For Claude Sonnet 4 ([console.anthropic.com](https://console.anthropic.com))
- **Mailjet Account** - For email delivery ([mailjet.com](https://www.mailjet.com))
- **Google Account** - For Google Sheets OAuth integration

---

## 📦 Installation

### Step 1: Setup Environment

1. **Clone this repository**
git clone https://github.com/Team-IOL/iris-assistant-docker.git
cd iris-assistant-docker

2. **Configure environment variables**
- Copy `.env.example` to `.env`
- Edit `.env` and set your `N8N_PASSWORD`

cp .env.example .env

Edit .env with your preferred text editor

### Step 2: Start Docker Containers

docker-compose up -d

Wait approximately 30 seconds for n8n to fully initialize.

### Step 3: Access n8n Interface

1. Open your browser and navigate to: [**http://localhost:5678**](http://localhost:5678)
2. Login with:
   - **Username:** `admin`
   - **Password:** (the password you set in `.env`)

### Step 4: Configure Credentials

Click **Settings** → **Credentials** → **Add Credential**

Add the following 5 credentials (detailed setup in `docs/CREDENTIAL_SETUP.md`):

1. **MySQL** - Name it: `IRIS_Database`
2. **Telegram API** - Name it: `Telegram_Bot`
3. **Anthropic API** - Name it: `Anthropic_Claude`
4. **Mailjet Email API** - Name it: `Mailjet_Email`
5. **Google Sheets OAuth2 API** - Name it: `Google_Sheets`

### Step 5: Import Workflows

Import the 6 workflow files in this **exact order**:

1. `workflows/Find-Crew-Member-Tool.json`
2. `workflows/Get-Crew-Details-Tool.json`
3. `workflows/Find-Available-Documents.json`
4. `workflows/Query-Crew-Lists-Tool.json`
5. `workflows/IRIS-AI-Assistant.json` ⚠️ **Import this last**
6. `workflows/Weekly-Access-Report.json`

See `docs/WORKFLOW_IMPORT.md` for detailed import instructions.

### Step 6: Configure Authorized Users

1. Open the **IRIS AI Assistant** workflow
2. Find the **Authorization Check** node
3. Update the authorized Telegram user IDs with your team's IDs
4. Save the workflow

### Step 7: Activate Workflows

1. Open **IRIS AI Assistant** workflow
2. Click the **Active** toggle (top right)
3. Optionally activate **Weekly Access Report** for automated reporting

---

## 🎯 Usage

### Basic Commands

Send messages to your Telegram bot:

**Crew Search:**
- "Find Juan dela Cruz"
- "Search for crew member ID 12345"
- "Who is John Smith?"

**Document Requests:**
- "Send me the documents for Maria Santos to maria@adamsonphil.com"
- "Email Juan's licenses to hr@adamsonphil.com"
- "Get training records for crew ID 12345"

**Crew Lists & Reports:**
- "Show me all deployed crew"
- "List crew with documents expiring in 30 days"
- "Send available crew list to hr@adamsonphil.com"

---

## 🔧 Management

### Stop the System
docker-compose down


### Restart the System
docker-compose up -d


### View Logs
docker-compose logs -f n8n


### Backup Workflows
Workflows are stored in the Docker volume. To backup:
docker-compose exec n8n n8n export:workflow --all --output=/tmp/workflows.json


---

## 📁 Repository Structure
```
iris-assistant-docker/
│
├── .env.example # Environment configuration template
├── .gitignore # Git ignore rules
├── docker-compose.yml # Docker services configuration
├── README.md # This file
├── CHANGELOG.md # Version history
│
├── workflows/ # n8n workflow JSON files
│ ├── README.md # Workflow documentation
│ ├── IRIS-AI-Assistant.json # Main AI assistant workflow
│ ├── Find-Crew-Member-Tool.json # Crew search tool
│ ├── Get-Crew-Details-Tool.json # Data retrieval tool
│ ├── Find-Available-Documents.json # Document manager
│ ├── Query-Crew-Lists-Tool.json # Reporting tool
│ └── Weekly-Access-Report.json # Automated reporting
│
└── docs/ # Documentation files
├── WORKFLOW_IMPORT.md # Step-by-step import guide
├── CREDENTIAL_SETUP.md # Credential configuration
├── DEPLOYMENT.md # Production deployment guide
└── TROUBLESHOOTING.md # Common issues and solutions
```
---

## 🔐 Security Best Practices

- Never commit `.env` file with actual credentials
- Use read-only database credentials
- Regularly rotate API keys
- Limit Telegram bot access to authorized users only
- Keep Docker images updated
- Enable firewall rules for production deployments

---

## 📚 Documentation

- **[Workflow Import Guide](docs/WORKFLOW_IMPORT.md)** - Step-by-step workflow import
- **[Credential Setup](docs/CREDENTIAL_SETUP.md)** - Detailed credential configuration
- **[Deployment Guide](docs/DEPLOYMENT.md)** - Production deployment instructions
- **[Troubleshooting](docs/TROUBLESHOOTING.md)** - Common issues and solutions
- **[Changelog](CHANGELOG.md)** - Version history and updates

---

## 🆘 Support

For issues or questions:
1. Check the [Troubleshooting Guide](docs/TROUBLESHOOTING.md)
2. Review workflow logs in n8n interface
3. Check Docker container logs: `docker-compose logs`

---

## 📝 License

Copyright © 2025 Adamson Philippines / Team IOL

---

## 🏗 Tech Stack

- **n8n** - Workflow automation platform
- **Docker** - Containerization
- **MySQL** - Database (read-only access)
- **Claude Sonnet 4** - AI language model (Anthropic)
- **Telegram Bot API** - Chat interface
- **Mailjet** - Email delivery service
- **Google Sheets** - Audit logging
- **AWS S3** - Document storage

---

## 📈 Version History

See [CHANGELOG.md](CHANGELOG.md) for detailed version history.

**Current Version:** 2.0.0 (November 2025)


