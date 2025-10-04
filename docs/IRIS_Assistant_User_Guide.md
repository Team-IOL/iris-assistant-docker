# IRIS Assistant - User Guide

**Version:** 1.0  
**Last Updated:** October 2025  
**For:** Adamson Philippines Inc. Staff

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [How to Use the Assistant](#how-to-use-the-assistant)
3. [Query Types](#query-types)
4. [Understanding Responses](#understanding-responses)
5. [Email Features](#email-features)
6. [Best Practices](#best-practices)
7. [Limitations](#limitations)
8. [Troubleshooting](#troubleshooting)

---

## Getting Started

### What is IRIS Assistant?

IRIS Assistant is an AI-powered Telegram bot that helps you quickly access crew information and documents from the IRIS database. You can ask questions in natural language, and the assistant will retrieve the information you need.

### First Time Setup

1. **Open Telegram** on your phone or computer
2. **Search for the bot**: `@your_bot_name`
3. **Click "Start"** or send `/start`
4. If you're authorized, you'll receive a welcome message
5. If you see "Access Denied", contact your administrator

### Requirements

- Telegram account
- Authorization from your administrator
- Active internet connection

---

## How to Use the Assistant

### Basic Usage

Simply type your question in natural language:

**Example:**
```
How many crew are on-board?
```

The assistant will understand your question and provide the answer.

### Conversation Style

You can ask follow-up questions:

```
You: Show me all on-board crew
Bot: [Returns list of crew]

You: How many of them have expired documents?
Bot: [Returns count and details]
```

The assistant remembers the context of your conversation.

---

## Query Types

### 1. Individual Crew Information

**Get information about a specific crew member.**

**Examples:**
```
What's Juan Dela Cruz's email?
Show me Maria Santos' information
Tell me about Pedro Reyes
What documents does John Doe have?
When is Jose Garcia's contract ending?
```

**What you'll get:**
- Personal information (name, email, phone, position)
- Deployment details (vessel, principal, contract dates)
- Document status (expired, expiring, valid)

**Information Types:**

Ask for specific sections:
```
Show me Juan's personal information
What's Maria's deployment status?
List Pedro's documents
Give me all information about John
```

---

### 2. Statistics and Counts

**Get quick counts and statistics.**

**Examples:**
```
How many crew are on-board?
Show me crew statistics
Total number of crew
How many crew on vacation?
Count of line-up crew
```

**What you'll get:**
- Total crew count
- On-Board count
- Line-up count
- On Pool count
- ACTIVE - on vacation count
- Crew on Leave count
- Expired document counts by type

---

### 3. Crew Lists by Status

**Get lists of crew filtered by their status.**

**Valid Statuses:**
- On-Board
- Line-up
- On Pool
- ACTIVE - on vacation
- Crew on Leave

**Examples:**
```
Show me all on-board crew
List crew on line-up
Who is on pool?
Show ACTIVE crew on vacation
List crew on leave
```

**What you'll get:**
- Crew name
- Primary position
- Status
- Email
- Phone number

---

### 4. Disembarking Crew

**See who is disembarking soon.**

**Examples:**
```
Who's disembarking this month?
Show crew disembarking in 30 days
List disembarking crew
Crew ending contracts soon
```

**What you'll get:**
- Crew name
- Vessel name
- Principal name
- Position
- Disembarkation date
- Days until disembarkation

---

### 5. Expired Documents

**Find crew with expired documents.**

**Examples:**
```
Show all expired documents
Who has expired training certificates?
List expired medical certificates
Show expired licenses
All expired documents for on-board crew
```

**Filter by document type:**
- `training` - Training certificates
- `medical` - Medical certificates
- `license` - Licenses
- `document` - General documents
- `all` - All document types

**What you'll get:**
- Crew name
- Document type
- Document name
- Expiry date
- Status

---

### 6. Expiring Documents

**Find documents expiring within 90 days.**

**Examples:**
```
Show expiring documents
Which documents are expiring soon?
List medical certificates expiring in 90 days
Show expiring training certificates
```

**What you'll get:**
- Crew name
- Document type
- Document name
- Expiry date
- Days until expiry

---

### 7. Email Documents

**Send crew documents to an email address.**

**Basic Format:**
```
Send [crew name]'s [document filter] to [email address]
```

**Examples:**
```
Send Juan Dela Cruz's expired documents to admin@company.com
Email Maria Santos' all documents to hr@company.com
Send Pedro's medical certificates to manager@company.com
Email John's expiring training docs to supervisor@company.com
```

**Document Filters:**
- `expired` - Only expired documents
- `expiring` - Documents expiring within 90 days
- `valid` - Valid documents
- `all` - All documents (default)

**Category Filters:**
- `training` - Only training documents
- `medical` - Only medical documents
- `license` - Only licenses
- `document` - Only general documents
- `all` - All categories (default)

**Combining Filters:**
```
Send Juan's expired training documents to admin@company.com
Email Maria's expiring medical certificates to hr@company.com
```

---

## Understanding Responses

### Inline Results

**For small results (10 items or less)**, the assistant shows data directly in Telegram:

```
📊 CREW STATISTICS

• Total Crew: 450
• On-Board: 180
• Line-up: 45
• On Pool: 120
• Expired Training: 12
• Expired Licenses: 8
```

### Email Reports

**For large results (more than 10 items)**, you'll receive:

1. **Summary in Telegram:**
   ```
   ✓ I've sent a detailed report with 125 results to admin@company.com
   ```

2. **Detailed HTML email** with:
   - Formatted tables
   - Sortable data
   - Download links (for documents)
   - Full crew information

### Document Emails

When you request documents via email, you'll receive:

- **Email subject:** Documents for [Crew Name] - [Filter Type]
- **Email body:** 
  - Crew information
  - Document list organized by category
  - Direct download links for each document
  - Document status indicators

**Example Email Structure:**
```
📋 License Documents (3)
✓ License Name 1 - Valid (Expires: Jan 15, 2026) [Download]
⏰ License Name 2 - Expiring Soon (Expires: Feb 20, 2025) [Download]
⚠️ License Name 3 - Expired (Expired: Dec 10, 2024) [Download]
```

---

## Email Features

### Requesting Your Email

You can save your email address for quick access:

```
You: My email is admin@company.com
Bot: I've saved your email address

You: Send Juan's documents to my email
Bot: ✓ Sent documents to admin@company.com
```

### Multiple Recipients

For multiple recipients, use standard email format:

```
Send reports to admin@company.com, hr@company.com
```

### Email Requirements

- Must be a valid email format (contains @)
- Cannot be just a number
- Cannot be an applicant_id

---

## Best Practices

### Searching for Crew

**✓ Good:**
```
Juan Dela Cruz
Maria Santos
Pedro Reyes
```

**✓ Also works:**
```
Juan Cruz (first + last name)
Dela Cruz (last name only)
Maria (first name - if unique)
```

**✗ Avoid:**
```
JDC (initials)
Juan D. (abbreviations)
```

### Asking Questions

**✓ Clear and specific:**
```
Show me Juan Dela Cruz's expired documents
How many crew are on-board?
List crew disembarking this month
```

**✓ Natural language:**
```
What documents does Maria have?
When is Pedro's contract ending?
Who has expired medical certificates?
```

**✗ Too vague:**
```
Show stuff
What about that guy?
Documents
```

### Getting Better Results

1. **Be specific** - Use full names when possible
2. **Use quotes** for exact names with special characters
3. **Specify filters** when requesting documents
4. **Provide email** for large data requests
5. **Ask follow-up questions** for clarification

---

## Limitations

### Result Limits

- Maximum **500 results** per query
- If more records exist, you'll be notified
- For complete data, use the IRIS web interface

### Search Limitations

- Names must match database entries
- Nicknames may not work
- Special characters might affect search

### Document Access

- Only documents with attached files can be emailed
- Some documents may be stored externally
- Download links expire after 7 days

### Query Scope

- **Read-only access** - cannot modify data
- **No file uploads** through the bot
- **No deletion** of records

### Time Zones

- All dates and times shown in **Philippine Time (PHT, UTC+8)**
- Expiry calculations based on Philippine timezone

---

## Troubleshooting

### "I couldn't find anyone with that name"

**Possible causes:**
- Name misspelled
- Person not in database
- Using nickname instead of legal name

**Solutions:**
1. Check spelling
2. Try last name only
3. Try first name + last name
4. Ask administrator to verify if person exists

### "Access Denied"

**Cause:** Your Telegram account is not authorized

**Solution:** 
1. Note your Telegram user ID (@userinfobot)
2. Contact your administrator
3. Request access authorization

### "No documents found"

**Possible causes:**
- No documents match your filter
- All documents archived
- Documents not uploaded yet

**Solutions:**
1. Try different filter (expired → all)
2. Try different category (training → all)
3. Verify crew member has documents

### "Email delivery failed"

**Possible causes:**
- Invalid email address
- Email server blocking
- Mailjet quota exceeded

**Solutions:**
1. Verify email address is correct
2. Check spam folder
3. Try different email address
4. Contact administrator if persistent

### Bot not responding

**Quick checks:**
1. Check internet connection
2. Verify bot is online (status in Telegram)
3. Try simple query: "Hello"
4. Contact administrator if no response

### Wrong information returned

**Steps:**
1. Verify you asked the right question
2. Check crew name spelling
3. Try rephrasing your query
4. Report issue to administrator with:
   - What you asked
   - What you expected
   - What you received

---

## Example Conversations

### Example 1: Getting Crew Information

```
You: Show me Juan Dela Cruz's information

Bot: 📋 Juan Dela Cruz (ID: 12345)

PERSONAL INFORMATION
• Status: On-Board
• State: Active
• Primary Position: Chief Engineer
• Email: juan.delacruz@email.com
• Phone: +639123456789
• Birthday: Jan 15, 1985
• Nationality: Filipino

🚢 DEPLOYMENT INFORMATION
• Vessel: MV Ocean Star
• Principal: Global Shipping Inc.
• Position: Chief Engineer
• Contract: 9 months
• Embarkation: Oct 01, 2024
• Disembarkation: Jul 01, 2025

📄 DOCUMENT STATUS
• Total: 15 documents
• ⚠️ Expired: 2
• ⏰ Expiring Soon: 3
• ✓ Valid: 10
```

### Example 2: Email Documents

```
You: Send Juan Dela Cruz's expired documents to admin@company.com

Bot: I'll send Juan Dela Cruz's expired documents to admin@company.com

[Processing...]

Bot: ✓ Sent 2 expired documents for Juan Dela Cruz to admin@company.com

Email includes:
• Expired Training: Basic Safety Training (Expired: Nov 10, 2024)
• Expired License: GMDSS Certificate (Expired: Dec 15, 2024)
```

### Example 3: Statistics Query

```
You: How many crew are on-board?

Bot: 📊 CREW STATISTICS

• Total Crew: 450
• On-Board: 180
• Line-up: 45
• On Pool: 120
• ACTIVE - on vacation: 15
• Crew on Leave: 8
• Expired Training: 12
• Expired Licenses: 8
• Expired Medical: 5
```

### Example 4: Bulk Query with Email

```
You: Show me all crew with expired medical certificates

Bot: Found 45 crew members with expired medical certificates.

I've sent a detailed report to your email (admin@company.com)

Summary:
• Total affected crew: 45
• Critical (expired >30 days): 12
• Recently expired (<30 days): 33
```

---

## Quick Reference

### Common Commands

| What you want | Say this |
|---------------|----------|
| Crew info | "Show me [name]" |
| Statistics | "How many crew on-board?" |
| Expired docs | "Show expired documents" |
| Expiring docs | "Show expiring documents" |
| Email docs | "Send [name]'s docs to [email]" |
| Status list | "List all on-board crew" |
| Disembarking | "Who's disembarking this month?" |

### Document Types

- `training` - Training certificates
- `medical` - Medical certificates  
- `license` - Licenses
- `document` - General documents
- `all` - Everything

### Status Types

- `On-Board` - Currently deployed
- `Line-up` - Ready for deployment
- `On Pool` - Available pool
- `ACTIVE - on vacation` - On vacation
- `Crew on Leave` - On leave

### Filter Types

- `expired` - Already expired
- `expiring` - Expires within 90 days
- `valid` - Valid beyond 90 days
- `all` - All documents

---

## Support

### Getting Help

**For assistance:**
1. Ask the bot: "Help" or "What can you do?"
2. Contact your administrator
3. Refer to this guide

### Reporting Issues

When reporting problems, provide:
- Your exact question
- Error message (if any)
- Expected result
- Actual result
- Screenshot (if possible)

### Training

New users should:
1. Read this guide
2. Try sample queries
3. Practice with known crew names
4. Start with simple queries before complex ones

---

**Document Information**

- Version: 1.0
- Last Updated: October 2025
- For questions or feedback, contact your system administrator
