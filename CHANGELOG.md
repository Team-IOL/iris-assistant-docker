# Changelog

All notable changes to the IRIS Assistant project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [2.0.0] - 2025-11-14

### 🎉 Major Release - Complete System Redesign

This version represents a complete architectural redesign with enhanced AI capabilities and modular workflow structure.

### Added

**New Features:**
- 🤖 Claude Sonnet 4 AI integration for natural language understanding
- 📊 Modular workflow architecture with 6 specialized workflows
- 📧 Professional HTML email templates via Mailjet
- 📝 Comprehensive audit logging with Google Sheets integration
- 📅 Automated weekly access reports (scheduled Fridays at 6 PM)
- 🔍 Advanced fuzzy search for crew member names
- 📂 Document categorization system:
  - Personal Documents
  - Licenses & Certificates
  - Training Records
  - Vaccination Records
  - Visa Information
  - Flag Endorsements
  - Medical Records (view-only)
- 📈 Statistical reporting capabilities:
  - Expiring documents reports
  - Deployed crew lists
  - Available crew lists
  - Status-based filtering

**New Workflows:**
1. `IRIS-AI-Assistant.json` - Main AI agent workflow
2. `Find-Crew-Member-Tool.json` - Crew search functionality
3. `Get-Crew-Details-Tool.json` - Document retrieval
4. `Find-Available-Documents.json` - Document filtering and email
5. `Query-Crew-Lists-Tool.json` - Statistical queries
6. `Weekly-Access-Report.json` - Automated reporting

**New Documentation:**
- Complete README redesign
- Workflow import guide
- Credential setup guide
- Deployment guide
- Troubleshooting guide
- Workflow-specific READMEs

### Changed

**Improvements:**
- 🎯 Enhanced crew search with fuzzy matching algorithm
- 🔒 Improved security with email domain validation
- 📋 SQL query optimization with deduplication logic (MAX(id) strategy)
- 🎨 Better Telegram message formatting with HTML support
- 📱 More intuitive conversation flow with natural language
- ⚡ Faster response times with optimized database queries

**Architecture:**
- Migrated from monolithic to modular workflow design
- Separated tool workflows from main assistant workflow
- Implemented webhook-based tool endpoints
- Centralized credential management

### Fixed
- Database connection timeout issues
- Duplicate document entries in search results
- Email delivery formatting inconsistencies
- Telegram message length limitations
- Medical document privacy concerns

### Security
- ✅ Email domain whitelist enforcement (adamsonphil.com, iol.ph, duck.com, tidal-solutions.com)
- ✅ Medical document view-only protection
- ✅ Authorized Telegram user validation
- ✅ Read-only database access enforcement
- ✅ Credential sanitization in repository files
- ✅ Webhook ID randomization on import

### Deprecated
- OpenAI GPT integration (replaced with Claude Sonnet 4)
- Legacy single-workflow architecture
- Manual weekly report generation

---

## [1.0.0] - 2025-10-15

### Initial Release

**Features:**
- Basic Telegram bot integration
- Simple crew information queries
- MySQL database connectivity
- Email delivery via Mailjet
- OpenAI GPT-3.5 integration

**Limitations:**
- Single monolithic workflow
- Limited natural language understanding
- No document categorization
- Manual report generation
- Basic error handling

---

## Version Numbering

- **Major (X.0.0):** Breaking changes, major feature additions
- **Minor (0.X.0):** New features, backward compatible
- **Patch (0.0.X):** Bug fixes, minor improvements

---

## Upgrade Notes

### From 1.x to 2.0

⚠️ **Breaking Changes:**
- Requires new Anthropic API credential
- Database schema must include all required tables
- Previous workflow exports are not compatible
- New credential naming conventions

**Migration Steps:**
1. Export data from v1.0 (if needed)
2. Follow fresh installation guide for v2.0
3. Import new workflow files in specified order
4. Configure all 5 required credentials
5. Update authorized Telegram user IDs
6. Test thoroughly before production deployment

See `docs/DEPLOYMENT.md` for detailed migration instructions.

---

## Contributors

- **Team IOL** - Development and implementation
- **Tidal-Solutions** - Requirements and testing

---

[2.0.0]: https://github.com/Team-IOL/iris-assistant-docker/releases/tag/v2.0.0
[1.0.0]: https://github.com/Team-IOL/iris-assistant-docker/releases/tag/v1.0.0
