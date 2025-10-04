# How to Get Your API Keys

## 1. Telegram Bot Token

1. Open Telegram
2. Search for: @BotFather
3. Send message: /newbot
4. Give it a name: "IRIS Assistant"
5. Give it a username: "iris_adamson_bot"
6. Copy the long token it gives you
7. Save this token - you'll need it later

## 2. Get Telegram User IDs

Have each person who should use the bot do this:
1. Open Telegram
2. Search for: @userinfobot
3. Send message: /start
4. Copy their ID number
5. Save all the ID numbers

## 3. Mailjet Account

1. Go to: https://www.mailjet.com
2. Click "Sign Up Free"
3. Create account
4. After login, go to: Account Settings > API Key Management
5. Click "Create a New API Key"
6. Copy both:
   - API Key
   - Secret Key
7. Save these

## 4. OpenAI API Key

1. Go to: https://platform.openai.com
2. Create account or login
3. Add a payment method
4. Go to: API Keys section
5. Click "Create new secret key"
6. Name it: "IRIS Assistant"
7. Copy the key (starts with sk-)
8. Save this (you can't see it again!)

## 5. Google Sheets

1. Create a new Google Sheet
2. Name it: "IRIS Audit Log"
3. Create two tabs:
   - Sheet1
   - Unauthorize_Access
4. In Sheet1, add these column headers:
   timestamp, telegram_user_id, telegram_first_name, user_message, action_type, crew_searched, applicant_id, recipient_email, filter_type, categories, status, bot_response

5. In Unauthorize_Access tab, add these headers:
   timestamp, telegram_user_id, telegram_first_name, user_message, attempted_action, chat_id

6. Copy the Spreadsheet ID from the URL
   Example URL: https://docs.google.com/spreadsheets/d/ABC123XYZ/edit
   Your ID is: ABC123XYZ

## 6. Adding Credentials to n8n

After Docker is running and you're logged into n8n:

1. Click the gear icon (Settings)
2. Click "Credentials"
3. Click "Add Credential"

For each credential type, fill in the information you collected above.

### MySQL Credential
- Type: MySQL
- Name: IRIS_Database
- Host: 44.233.4.43
- Database: adamsondb
- User: adamson_readonly
- Password: (ask your database admin)

### Telegram Credential
- Type: Telegram API
- Name: Telegram_Bot
- Access Token: (paste from @BotFather)

### Mailjet Credential
- Type: Mailjet Email API
- Name: Mailjet Email account
- API Key: (from Mailjet)
- Secret Key: (from Mailjet)

### OpenAI Credential
- Type: OpenAI API
- Name: OpenAi account
- API Key: (from OpenAI)

### Google Sheets Credential
- Type: Google Sheets OAuth2 API
- Name: Google Sheets account
- Click "Connect to Google"
- Login with your Google account
- Allow access

Done! Now activate the workflows.