# IRIS Assistant - Docker Deployment

AI-powered Telegram assistant for crew management.

## What You Need Before Starting

- Docker installed on your computer
- MySQL database access (already provided)
- Telegram bot token (from @BotFather)
- Mailjet email account
- OpenAI API key
- Google account

## Installation Steps

### 1. Setup Environment

Copy the template file:
- Make a copy of `.env.example`
- Rename the copy to `.env`
- Open `.env` and change the password for N8N_PASSWORD

### 2. Start Docker

Open Terminal (Mac) or Command Prompt (Windows) and type:
cd Desktop/IRIS_Assistant_Docker
docker-compose up -d

Wait 30 seconds for it to start.

### 3. Access n8n

Open your web browser and go to: http://localhost:5678

Login with:
- Username: admin
- Password: (whatever you set in .env file)

### 4. Configure Credentials

Click Settings (gear icon) > Credentials > Add Credential

Add each of these 5 credentials:
1. MySQL (name it: IRIS_Database)
2. Telegram API (name it: Telegram_Bot)
3. Mailjet Email API (name it: Mailjet Email account)
4. OpenAI API (name it: OpenAi account)
5. Google Sheets OAuth2 API (name it: Google Sheets account)

See CREDENTIAL_SETUP.md for detailed instructions.

### 5. Update Authorized Users

1. Open the "IRIS AI Assistant" workflow
2. Find the "Authorization Check" node
3. Update the user IDs with your team's Telegram IDs
4. Save

### 6. Activate

Click the "Active" toggle on the IRIS AI Assistant workflow.

Done! Send a message to your Telegram bot to test.

## Stopping
docker-compose down

## Starting Again
docker-compose up -d

## Help

See the docs/ folder for detailed guides.