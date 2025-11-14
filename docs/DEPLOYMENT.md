# Production Deployment Guide

Complete guide for deploying IRIS Assistant to production environment.

---

## Overview

This guide covers deploying IRIS Assistant from development (localhost) to a production server environment with proper security, monitoring, and backup procedures.

---

## Production Architecture

**Components:**

### Server Layer
- **Nginx Reverse Proxy:** Handles SSL/TLS termination, routes traffic to n8n
- **n8n Container:** Docker container running the workflow automation engine
- **Workflows:** 6 imported workflows handling various functions

### External Connections
- **Telegram Users:** End users interacting via Telegram bot
- **MySQL Database:** Cloud-hosted IRIS crew management database (read-only)
- **Anthropic API:** Claude Sonnet 4 for AI language processing
- **Mailjet API:** Email delivery service
- **Google Sheets:** Audit logging and reporting


---

## Prerequisites

### Server Requirements

**Minimum Specifications:**
- **OS:** Ubuntu 20.04 LTS or newer (or similar Linux distribution)
- **CPU:** 2 cores
- **RAM:** 4 GB
- **Storage:** 20 GB SSD
- **Network:** Static IP address or domain name

**Recommended Specifications:**
- **CPU:** 4 cores
- **RAM:** 8 GB
- **Storage:** 50 GB SSD
- **Network:** Static IP with domain name and SSL certificate

### Software Requirements

- Docker Engine 20.10+
- Docker Compose 1.29+
- Nginx (for reverse proxy)
- Certbot (for SSL certificates)
- Git (for repository cloning)

### Access Requirements

- SSH access to production server
- Root or sudo privileges
- Domain name pointed to server IP (for SSL)
- Firewall access to required ports

---

## Pre-Deployment Checklist

Before deploying to production, verify:

- [ ] All workflows tested and working in development
- [ ] All credentials obtained and validated
- [ ] Database connection tested from production server IP
- [ ] Domain name DNS configured correctly
- [ ] SSL certificate can be obtained (domain verified)
- [ ] Firewall rules configured to allow required ports
- [ ] Backup strategy planned
- [ ] Monitoring tools selected
- [ ] Team trained on system usage

---

## Deployment Steps

### Step 1: Server Setup

**1.1: Connect to your server**
```
ssh username@your-production-server.com
```

**1.2: Update system packages**
```
sudo apt update
sudo apt upgrade -y
```

**1.3: Install required software**

**Install Docker:**
Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

Set up stable repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

Install Docker Engine
```
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
```
Add your user to docker group
```
sudo usermod -aG docker $USER
```
Log out and back in for group changes to take effect


**Install Docker Compose:**
```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

**Install Nginx:**
```
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
```

**Install Certbot (for SSL):**
```
sudo apt install certbot python3-certbot-nginx -y
```

### Step 2: Clone Repository

**2.1: Create application directory**
```
sudo mkdir -p /opt/iris-assistant
sudo chown $USER:$USER /opt/iris-assistant
cd /opt/iris-assistant
```

**2.2: Clone the repository**
```
git clone https://github.com/Team-IOL/iris-assistant-docker.git .
```

**2.3: Verify files**
```
ls -la
```
Should see: docker-compose.yml, .env.example, workflows/, docs/, etc.


### Step 3: Configure Environment

**3.1: Create production environment file**
```
cp .env.example .env
```

**3.2: Edit environment variables**
```
nano .env
```

Update these critical values:
```
n8n Configuration
N8N_PASSWORD=your-strong-production-password-here
N8N_HOST=iris.yourdomain.com
N8N_PORT=5678
N8N_PROTOCOL=https

Database Configuration
MYSQL_HOST=your-production-mysql-host.com
MYSQL_PORT=3306
MYSQL_DATABASE=iris_production_db
MYSQL_USER=readonly_prod_user
MYSQL_PASSWORD=your-secure-mysql-password

Telegram Bot
TELEGRAM_BOT_TOKEN=your-production-bot-token

Anthropic API
ANTHROPIC_API_KEY=your-anthropic-api-key

Mailjet
MAILJET_API_KEY=your-mailjet-api-key
MAILJET_API_SECRET=your-mailjet-secret-key
MAILJET_SENDER_EMAIL=noreply@adamsonphil.com

Google Sheets
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
GOOGLE_SHEET_AUDIT_LOG_ID=your-production-sheet-id

AWS S3
AWS_S3_BUCKET=adamson-production-bucket
AWS_S3_REGION=us-west-2

Application Settings
APP_ENV=production
DEBUG_MODE=false
TZ=Asia/Manila
```

**3.3: Secure the .env file**
```
chmod 600 .env
```

### Step 4: Configure Nginx Reverse Proxy

**4.1: Create Nginx configuration**
```
sudo nano /etc/nginx/sites-available/iris-assistant
```

**4.2: Add this configuration**
```
server {
listen 80;
server_name iris.yourdomain.com;

# Redirect to HTTPS (will be configured after SSL)
return 301 https://$server_name$request_uri;
}

server {
listen 443 ssl http2;
server_name iris.yourdomain.com;

# SSL certificates (will be added by Certbot)
# ssl_certificate /etc/letsencrypt/live/iris.yourdomain.com/fullchain.pem;
# ssl_certificate_key /etc/letsencrypt/live/iris.yourdomain.com/privkey.pem;

# SSL configuration

ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers HIGH:!aNULL:!MD5;
ssl_prefer_server_ciphers on;

# Security headers

add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;

# Proxy to n8n

location / {
    proxy_pass http://localhost:5678;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    
    # Increase timeouts for long-running workflows
    proxy_read_timeout 300s;
    proxy_connect_timeout 75s;
}

# Webhook endpoint (important for Telegram)

location /webhook {
    proxy_pass http://localhost:5678/webhook;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}

# Logging

access_log /var/log/nginx/iris-assistant-access.log;
error_log /var/log/nginx/iris-assistant-error.log;
}
```

**4.3: Enable the site**
```
sudo ln -s /etc/nginx/sites-available/iris-assistant /etc/nginx/sites-enabled/
sudo nginx -t # Test configuration
sudo systemctl reload nginx
```

### Step 5: Obtain SSL Certificate

**5.1: Get Let's Encrypt certificate**
```
sudo certbot --nginx -d iris.yourdomain.com
```

Follow the prompts:
- Enter email address
- Agree to terms
- Choose to redirect HTTP to HTTPS (recommended)

**5.2: Verify certificate**
```
sudo certbot certificates

```

**5.3: Set up auto-renewal**
```
Certbot auto-renewal is set up automatically
Test renewal:
sudo certbot renew --dry-run
```

### Step 6: Start Docker Containers

**6.1: Start the application**
```
cd /opt/iris-assistant
docker-compose up -d
```

**6.2: Check container status**
```
docker-compose ps
```
Should show n8n container running

**6.3: View logs**
```
docker-compose logs -f n8n
```
Press Ctrl+C to exit logs


### Step 7: Configure n8n

**7.1: Access n8n interface**
- Open browser: `https://iris.yourdomain.com`
- You should see n8n login page
- Login with credentials from .env file

**7.2: Set up credentials**
Follow the [CREDENTIAL_SETUP.md](CREDENTIAL_SETUP.md) guide to configure all 5 credentials.

**Important for production:**
- Use production database credentials
- Use production Telegram bot token
- Update Google OAuth redirect URL to: `https://iris.yourdomain.com/rest/oauth2-credential/callback`

**7.3: Import workflows**
Follow the [WORKFLOW_IMPORT.md](WORKFLOW_IMPORT.md) guide to import all workflows in the correct order.

**7.4: Configure production-specific settings**

**Update webhook URLs:**
- In each workflow with webhooks, update production URLs
- Format: `https://iris.yourdomain.com/webhook/...`

**Update authorized users:**
- In IRIS AI Assistant workflow
- Update Telegram user IDs for authorized team members

**Update Google Sheet IDs:**
- Use production audit log sheet IDs
- Test sheet access from production

### Step 8: Configure Telegram Bot Webhook

**8.1: Get webhook URL from n8n**
1. Open IRIS AI Assistant workflow
2. Click on the Webhook node
3. Copy the **Production URL**
4. Example: `https://iris.yourdomain.com/webhook/iris-webhook`

**8.2: Set Telegram webhook**
```
Option A - Using curl:
curl -X POST "https://api.telegram.org/bot<YOUR_BOT_TOKEN>/setWebhook"
-H "Content-Type: application/json"
-d '{"url": "https://iris.yourdomain.com/webhook/iris-webhook"}'

Option B - Using browser:
https://api.telegram.org/bot<YOUR_BOT_TOKEN>/setWebhook?url=https://iris.yourdomain.com/webhook/iris-webhook
```

**8.3: Verify webhook**
```
curl https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getWebhookInfo
```

Should show:
```
{
"ok": true,
"result": {
"url": "https://iris.yourdomain.com/webhook/iris-webhook",
"has_custom_certificate": false,
"pending_update_count": 0
}
}
```

---

## Post-Deployment Configuration

### Firewall Setup

**Configure UFW (Ubuntu):**
```
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status
```

### Database Connection

**Whitelist production server IP:**
- Contact database administrator
- Add production server IP to MySQL whitelist
- Test connection from server

### Monitoring Setup

**Set up basic monitoring:**
```
Install monitoring tools
sudo apt install htop -y

Check system resources
htop

Monitor Docker containers
docker stats
```

### Backup Configuration

**Create backup script:**
```
sudo nano /opt/iris-assistant/backup.sh

Add this script:
#!/bin/bash
IRIS Assistant Backup Script
BACKUP_DIR="/opt/iris-assistant/backups"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

Create backup directory
mkdir -p $BACKUP_DIR

Backup workflows
docker exec iris-assistant_n8n_1 n8n export:workflow --all --output=/tmp/workflows_$TIMESTAMP.json
docker cp iris-assistant_n8n_1:/tmp/workflows_$TIMESTAMP.json $BACKUP_DIR/

Backup credentials (encrypted)
docker exec iris-assistant_n8n_1 n8n export:credentials --all --output=/tmp/credentials_$TIMESTAMP.json
docker cp iris-assistant_n8n_1:/tmp/credentials_$TIMESTAMP.json $BACKUP_DIR/

Backup environment file
cp /opt/iris-assistant/.env $BACKUP_DIR/env_$TIMESTAMP.backup

Compress backups
tar -czf $BACKUP_DIR/iris_backup_$TIMESTAMP.tar.gz -C $BACKUP_DIR workflows_$TIMESTAMP.json credentials_$TIMESTAMP.json env_$TIMESTAMP.backup

Remove individual files
rm $BACKUP_DIR/workflows_$TIMESTAMP.json $BACKUP_DIR/credentials_$TIMESTAMP.json $BACKUP_DIR/env_$TIMESTAMP.backup

Keep only last 7 days of backups

find $BACKUP_DIR -name "iris_backup_*.tar.gz" -mtime +7 -delete
echo "Backup completed: iris_backup_$TIMESTAMP.tar.gz"

```

**Make script executable:**
```
chmod +x /opt/iris-assistant/backup.sh

```

**Schedule daily backups:**
```
crontab -e
```
Add this line:
```
0 2 * * * /opt/iris-assistant/backup.sh >> /opt/iris-assistant/backup.log 2>&1
```

---

## Testing Production Deployment

### Functional Tests

**1. Test Telegram bot:**
- Open Telegram
- Search for your production bot
- Send: "Hello"
- Verify response

**2. Test crew search:**
- Send: "Find [crew member name]"
- Verify search results

**3. Test document retrieval:**
- Send: "Get documents for [crew member] to [email]"
- Verify email delivery

**4. Test database connection:**
- Check n8n execution logs
- Verify queries execute successfully

**5. Test Google Sheets logging:**
- Check audit log sheet
- Verify new entries are being added

### Performance Tests

**Monitor resource usage:**
```
CPU and memory
docker stats

Disk usage
df -h

Network connections
netstat -tuln
```

**Check response times:**
- Telegram bot response time should be < 3 seconds
- Database queries should complete < 1 second
- Email delivery should trigger < 5 seconds

---

## Maintenance Procedures

### Regular Updates

**Update n8n version:**
```
cd /opt/iris-assistant
docker-compose pull
docker-compose up -d
```

**Update workflows:**
```
git pull origin main
```
Re-import updated workflows in n8n interface


### Log Management

**View logs:**
```
n8n application logs
docker-compose logs -f n8n

Nginx access logs

sudo tail -f /var/log/nginx/iris-assistant-access.log

Nginx error logs

sudo tail -f /var/log/nginx/iris-assistant-error.log
```


**Rotate logs:**
```
Configure logrotate
sudo nano /etc/logrotate.d/iris-assistant

Add:
/var/log/nginx/iris-assistant-*.log {
daily
rotate 14
compress
delaycompress
notifempty
create 0640 www-data adm
sharedscripts
postrotate
[ -f /var/run/nginx.pid ] && kill -USR1 cat /var/run/nginx.pid
endscript
}
```

### Restart Procedures

**Restart n8n only:**
```
docker-compose restart n8n
```

**Restart entire stack:**
```
docker-compose down
docker-compose up -d
```

**Restart Nginx:**
```
sudo systemctl restart nginx
```

---

## Troubleshooting Production Issues

### Common Issues

**Issue: Bot not responding**
1. Check container status: `docker-compose ps`
2. Check logs: `docker-compose logs n8n`
3. Verify webhook: `curl https://api.telegram.org/bot<TOKEN>/getWebhookInfo`
4. Restart container: `docker-compose restart n8n`

**Issue: SSL certificate errors**
1. Check certificate status: `sudo certbot certificates`
2. Renew if needed: `sudo certbot renew`
3. Reload Nginx: `sudo systemctl reload nginx`

**Issue: Database connection timeout**
1. Verify server IP is whitelisted
2. Test connection: `mysql -h HOST -u USER -p`
3. Check firewall rules

**Issue: High memory usage**
1. Check container stats: `docker stats`
2. Review workflow efficiency
3. Consider increasing server resources

---

## Security Hardening

### Essential Security Measures

**1. Change default passwords:**
- n8n admin password
- Database passwords
- SSH keys instead of passwords

**2. Keep software updated:**
```
sudo apt update && sudo apt upgrade -y
docker-compose pull
```

**3. Enable fail2ban:**
```
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
```

**4. Regular security audits:**
- Review access logs weekly
- Monitor failed login attempts
- Check for suspicious activity

**5. Backup encryption:**
- Encrypt sensitive backups
- Store backups off-site
- Test restore procedures regularly

---

## Rollback Procedures

If deployment fails or issues arise:

**1. Stop containers:**
```
docker-compose down
```

**2. Restore from backup:**
```
cd /opt/iris-assistant/backups
tar -xzf iris_backup_TIMESTAMP.tar.gz
```
Import workflows back into n8n


**3. Revert to previous version:**
```
git log --oneline
git checkout <previous-commit-hash>
docker-compose up -d
```

---

## Production Checklist

Before marking deployment complete:

- [ ] All workflows imported and activated
- [ ] All credentials configured and tested
- [ ] Telegram bot webhook configured
- [ ] SSL certificate installed and auto-renewal working
- [ ] Firewall rules configured
- [ ] Backups scheduled and tested
- [ ] Monitoring tools set up
- [ ] Log rotation configured
- [ ] Documentation updated with production URLs
- [ ] Team trained on production access
- [ ] Emergency contacts documented
- [ ] Rollback procedure tested

---

## Support & Escalation

For production issues:

**Level 1:** Check logs and restart services
**Level 2:** Review troubleshooting guide
**Level 3:** Contact system administrator
**Level 4:** Contact development team

**Emergency contacts:**
- System Admin: [contact info]
- Database Admin: [contact info]
- Development Team: [contact info]

---

## Next Steps

After successful deployment:
- Monitor system for 48 hours
- Gather user feedback
- Plan for future enhancements
- Schedule regular maintenance windows
