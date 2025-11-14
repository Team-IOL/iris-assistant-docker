# Troubleshooting Guide

Comprehensive guide for diagnosing and resolving common issues with IRIS Assistant.

---

## Quick Diagnostics

Before diving into specific issues, run these quick checks:

```bash
# Check Docker containers are running
docker-compose ps

# Check n8n logs
docker-compose logs -f n8n

# Check system resources
docker stats

# Test database connection
docker-compose exec n8n mysql -h <MYSQL_HOST> -u <MYSQL_USER> -p
```

---

## Issue Categories

- [Installation Issues](#installation-issues)
- [Credential Problems](#credential-problems)
- [Workflow Import Errors](#workflow-import-errors)
- [Telegram Bot Issues](#telegram-bot-issues)
- [Database Connection Problems](#database-connection-problems)
- [Email Delivery Issues](#email-delivery-issues)
- [Google Sheets Errors](#google-sheets-errors)
- [Performance Issues](#performance-issues)
- [Docker Container Issues](#docker-container-issues)

---

## Installation Issues

### Issue: Docker Compose fails to start

**Symptoms:**
```
ERROR: Couldn't connect to Docker daemon
```

**Solutions:**

1. **Start Docker service:**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

2. **Check Docker is running:**
   ```bash
   docker ps
   ```

3. **Verify user permissions:**
   ```bash
   sudo usermod -aG docker $USER
   # Log out and back in
   ```

4. **Check Docker Compose version:**
   ```bash
   docker-compose --version
   # Should be 1.29+ or newer
   ```

---

### Issue: Port 5678 already in use

**Symptoms:**
```
Error: bind: address already in use
```

**Solutions:**

1. **Check what's using port 5678:**
   ```bash
   sudo lsof -i :5678
   # or
   sudo netstat -tulpn | grep 5678
   ```

2. **Stop conflicting service:**
   ```bash
   # If another n8n instance
   docker stop <container_id>

   # If another application
   sudo systemctl stop <service_name>
   ```

3. **Change n8n port (if needed):**
   - Edit `docker-compose.yml`
   - Change `5678:5678` to `8080:5678`
   - Access at `http://localhost:8080`

---

### Issue: Cannot access n8n interface

**Symptoms:**
- Browser shows "Connection refused"
- Page not loading at localhost:5678

**Solutions:**

1. **Wait for container to fully start:**
   ```bash
   docker-compose logs -f n8n
   # Wait for "Server ready on port 5678"
   ```

2. **Check container is running:**
   ```bash
   docker-compose ps
   # Should show "Up" status
   ```

3. **Verify port mapping:**
   ```bash
   docker port <container_name>
   # Should show 5678/tcp -> 0.0.0.0:5678
   ```

4. **Check firewall:**
   ```bash
   sudo ufw status
   # Add rule if needed:
   sudo ufw allow 5678/tcp
   ```

5. **Try different browser or incognito mode:**
   - Clear browser cache
   - Disable browser extensions
   - Try Chrome/Firefox if using Safari

---

## Credential Problems

### Issue: "Credential not found" error in workflow

**Symptoms:**
- Red warning triangle on nodes
- Error: "Credential with id 'XXX' could not be found"

**Solutions:**

1. **Verify credential exists:**
   - Settings → Credentials
   - Check if credential is listed

2. **Check credential name matches exactly:**
   - Expected names:
     - `IRIS_Database`
     - `Telegram_Bot`
     - `Anthropic_Claude`
     - `Mailjet_Email`
     - `Google_Sheets`

3. **Re-select credential in node:**
   - Click on the node with red triangle
   - Select credential from dropdown
   - Click "Save"

4. **Re-import workflow if needed:**
   - Delete problematic workflow
   - Re-import from JSON file
   - Configure credentials again

---

### Issue: MySQL credential test fails

**Symptoms:**
```
Error: connect ECONNREFUSED
Error: Access denied for user
```

**Solutions:**

1. **Verify connection details:**
   - Host: Correct hostname or IP
   - Port: Usually 3306
   - Database: Correct database name
   - User: Correct username
   - Password: Check for typos

2. **Test from command line:**
   ```bash
   mysql -h <host> -P <port> -u <user> -p<password> <database>
   ```

3. **Check database server firewall:**
   - Verify your IP is whitelisted
   - Contact database administrator

4. **Verify user permissions:**
   ```sql
   SHOW GRANTS FOR 'username'@'hostname';
   ```

5. **Try with SSL disabled:**
   - In MySQL credential settings
   - Toggle "SSL" to OFF (for testing only)

---

### Issue: Telegram credential not working

**Symptoms:**
- Bot doesn't respond
- Invalid token error

**Solutions:**

1. **Verify token format:**
   - Should be: `123456789:ABCdefGHIjklMNOpqrsTUVwxyz`
   - No spaces or extra characters

2. **Test token directly:**
   ```bash
   curl https://api.telegram.org/bot<YOUR_TOKEN>/getMe
   ```
   - Should return bot information

3. **Check bot is not deleted:**
   - Message @BotFather
   - Send `/mybots`
   - Verify bot exists

4. **Regenerate token if compromised:**
   - Message @BotFather
   - Select your bot
   - Click "API Token" → "Revoke current token"
   - Update credential in n8n

---

### Issue: Anthropic API authentication fails

**Symptoms:**
```
Error: Authentication failed
Error: Invalid API key
```

**Solutions:**

1. **Verify API key format:**
   - Should start with: `sk-ant-api03-`
   - Check for typos or missing characters

2. **Check API key status:**
   - Login to console.anthropic.com
   - Navigate to "API Keys"
   - Verify key is active (not revoked)

3. **Verify account has credits:**
   - Check "Billing" section
   - Add credits if balance is $0

4. **Generate new API key:**
   - Create new key in Anthropic console
   - Update credential in n8n

---

### Issue: Google Sheets OAuth fails

**Symptoms:**
- "Redirect URI mismatch" error
- "Access blocked" message
- OAuth popup doesn't appear

**Solutions:**

1. **Verify redirect URI in Google Console:**
   - Go to Google Cloud Console → Credentials
   - Click on your OAuth client
   - Check "Authorized redirect URIs"
   - Should exactly match n8n's redirect URL
   - Format: `http://localhost:5678/rest/oauth2-credential/callback`

2. **Add test user for development:**
   - Google Cloud Console → OAuth consent screen
   - Add your Gmail to "Test users"

3. **Enable Google Sheets API:**
   - Google Cloud Console → APIs & Services → Library
   - Search "Google Sheets API"
   - Click "Enable"

4. **Clear browser cookies and retry:**
   - Clear cookies for google.com
   - Try OAuth flow again

5. **Check OAuth consent screen configuration:**
   - Must be configured before OAuth works
   - Add required scopes: `https://www.googleapis.com/auth/spreadsheets`

---

## Workflow Import Errors

### Issue: "Invalid JSON" when importing workflow

**Symptoms:**
```
Error: Unexpected token in JSON
Error: Invalid workflow format
```

**Solutions:**

1. **Verify file is not corrupted:**
   - Open JSON file in text editor
   - Check it starts with `{` and ends with `}`
   - Verify no extra characters

2. **Re-download workflow file:**
   - Download fresh copy from GitHub
   - Don't copy-paste from browser (may add formatting)

3. **Check file encoding:**
   - Should be UTF-8
   - Re-save with UTF-8 encoding if needed

---

### Issue: Workflows imported but not visible

**Symptoms:**
- Import succeeds but workflow doesn't appear
- Empty workflow list

**Solutions:**

1. **Refresh browser:**
   - Hard refresh: Ctrl+Shift+R (Windows/Linux) or Cmd+Shift+R (Mac)

2. **Check workflow list filter:**
   - Make sure no filters are applied
   - Check "Show all" is selected

3. **Check Docker volume:**
   ```bash
   docker volume ls
   # Verify n8n volume exists
   ```

4. **Restart n8n container:**
   ```bash
   docker-compose restart n8n
   ```

---

### Issue: "Execute Workflow" node can't find sub-workflow

**Symptoms:**
```
Error: Could not find workflow with id/name "XXX"
```

**Solutions:**

1. **Verify sub-workflow is imported:**
   - Check workflow list
   - Ensure all 6 workflows are imported

2. **Import in correct order:**
   - Tool workflows MUST be imported BEFORE main workflow
   - See: docs/WORKFLOW_IMPORT.md

3. **Re-select workflow in node:**
   - Open main workflow
   - Click "Execute Workflow" node
   - Select sub-workflow from dropdown
   - Click "Save"

---

## Telegram Bot Issues

### Issue: Bot doesn't respond to messages

**Symptoms:**
- Messages sent to bot show "delivered" but no response
- Bot appears online but silent

**Solutions:**

1. **Check workflow is active:**
   - Open "IRIS AI Assistant" workflow
   - Toggle should show "Active" (green)

2. **Verify webhook is set:**
   ```bash
   curl https://api.telegram.org/bot<TOKEN>/getWebhookInfo
   ```
   - Should show your webhook URL
   - If empty, set webhook:
   ```bash
   curl -X POST "https://api.telegram.org/bot<TOKEN>/setWebhook" \
     -H "Content-Type: application/json" \
     -d '{"url": "https://your-domain.com/webhook/iris-webhook"}'
   ```

3. **Check execution logs:**
   - n8n interface → Executions
   - Look for errors in recent executions

4. **Test webhook directly:**
   ```bash
   curl -X POST http://localhost:5678/webhook/iris-webhook \
     -H "Content-Type: application/json" \
     -d '{"message": {"text": "test"}}'
   ```

5. **Restart workflow:**
   - Deactivate workflow
   - Wait 5 seconds
   - Activate workflow again

---

### Issue: "Not authorized" message from bot

**Symptoms:**
- Bot responds but says "You are not authorized"

**Solutions:**

1. **Update authorized user IDs:**
   - Open "IRIS AI Assistant" workflow
   - Find "Authorization Check" node
   - Update `authorizedUsers` array with your Telegram user ID
   - Get your ID from @userinfobot

2. **Verify user ID format:**
   ```javascript
   const authorizedUsers = [123456789, 987654321]; // Numbers, not strings
   ```

3. **Check for typos in user IDs:**
   - User IDs are numeric only
   - No quotes, no spaces

---

### Issue: Bot responses are cut off or incomplete

**Symptoms:**
- Long messages appear truncated
- Missing document lists

**Solutions:**

1. **Check Telegram message length limit:**
   - Telegram has 4096 character limit per message
   - Workflow should handle this automatically

2. **Verify "Split message" option:**
   - In Telegram node
   - Enable message splitting if available

3. **Review workflow logic:**
   - Check if response formatting is correct
   - Look for truncation in execution logs

---

## Database Connection Problems

### Issue: "Connection timeout" errors

**Symptoms:**
```
Error: connect ETIMEDOUT
Error: Connection lost: The server closed the connection
```

**Solutions:**

1. **Check database server is accessible:**
   ```bash
   ping <database_host>
   telnet <database_host> 3306
   ```

2. **Verify IP whitelist:**
   - Contact database administrator
   - Add your server's IP to whitelist
   - Check for IP changes (if using dynamic IP)

3. **Increase connection timeout:**
   - MySQL credential settings
   - Set timeout to 30000 ms or higher

4. **Check network connectivity:**
   ```bash
   traceroute <database_host>
   # Look for network issues
   ```

---

### Issue: "Too many connections" error

**Symptoms:**
```
Error: ER_TOO_MANY_CONNECTIONS
Error: Too many connections
```

**Solutions:**

1. **Check MySQL connection limits:**
   ```sql
   SHOW VARIABLES LIKE 'max_connections';
   ```

2. **Close unused connections:**
   ```sql
   SHOW PROCESSLIST;
   -- Kill idle connections
   ```

3. **Optimize workflow execution:**
   - Reduce concurrent workflow runs
   - Add delays between database queries

4. **Request higher connection limit:**
   - Contact database administrator
   - Request increased `max_connections`

---

### Issue: "Table doesn't exist" error

**Symptoms:**
```
Error: Table 'database.personal' doesn't exist
```

**Solutions:**

1. **Verify database name:**
   - Check credential configuration
   - Confirm database name is correct

2. **Check table names:**
   ```sql
   SHOW TABLES;
   ```
   - Required tables: `personal`, `applicant_documents`, `applicant_licenses`, `training`, `medical`, etc.

3. **Verify user has access:**
   ```sql
   SHOW GRANTS FOR 'user'@'host';
   ```

4. **Check for case sensitivity:**
   - Linux MySQL is case-sensitive
   - Verify exact table name spelling

---

## Email Delivery Issues

### Issue: Emails not being sent

**Symptoms:**
- Workflow executes successfully
- No email received

**Solutions:**

1. **Check Mailjet dashboard:**
   - Login to Mailjet
   - Check "Statistics" for recent sends
   - Look for bounces or blocks

2. **Verify sender is validated:**
   - Mailjet → Account Settings → Sender addresses
   - Sender must be verified

3. **Check spam folder:**
   - Emails might be filtered
   - Add sender to safe list

4. **Test Mailjet credential:**
   - Create test workflow
   - Send simple test email
   - Check for errors

5. **Verify recipient email:**
   - Check for typos
   - Ensure domain is whitelisted
   - Allowed domains: `adamsonphil.com`, `iol.ph`, `duck.com`, `tidal-solutions.com`

---

### Issue: "Sender not validated" error

**Symptoms:**
```
Error: Sender email address is not validated
```

**Solutions:**

1. **Validate sender in Mailjet:**
   - Mailjet → Account Settings → Sender Domains & Addresses
   - Add sender email
   - Click verification link sent to email

2. **Use validated sender:**
   - Update workflow email nodes
   - Use only validated sender addresses

---

### Issue: Emails missing attachments

**Symptoms:**
- Email received but no CSV/document attached

**Solutions:**

1. **Check attachment generation:**
   - Review execution logs
   - Verify data is being prepared correctly

2. **Check file size limits:**
   - Mailjet free plan: 15 MB per email
   - Large attachments may be rejected

3. **Verify attachment encoding:**
   - Should be base64 encoded
   - Check node configuration

---

## Google Sheets Errors

### Issue: "Insufficient permissions" error

**Symptoms:**
```
Error: The caller does not have permission
Error: 403 Forbidden
```

**Solutions:**

1. **Re-authenticate credential:**
   - Settings → Credentials → Google_Sheets
   - Click "Reconnect"
   - Grant all requested permissions

2. **Check OAuth scopes:**
   - Should include: `https://www.googleapis.com/auth/spreadsheets`
   - Re-create OAuth client if needed

3. **Share sheet with OAuth account:**
   - Open Google Sheet
   - Click "Share"
   - Add the Google account you used for OAuth
   - Give "Editor" access

---

### Issue: "Spreadsheet not found" error

**Symptoms:**
```
Error: Requested entity was not found
```

**Solutions:**

1. **Verify Sheet ID:**
   - Check URL: `https://docs.google.com/spreadsheets/d/SHEET_ID/edit`
   - Copy the SHEET_ID part
   - Verify it's correct in workflow

2. **Check sheet sharing:**
   - Sheet must be accessible to OAuth account
   - Make sure it's not deleted

3. **Verify sheet name/tab:**
   - Check exact spelling of sheet tab name
   - Case-sensitive

---

## Performance Issues

### Issue: Slow workflow execution

**Symptoms:**
- Responses take > 10 seconds
- Timeout errors

**Solutions:**

1. **Check database query performance:**
   ```sql
   EXPLAIN SELECT ...
   -- Analyze query execution plan
   ```
   - Add indexes if needed
   - Optimize complex queries

2. **Monitor system resources:**
   ```bash
   docker stats
   # Check CPU and memory usage
   ```

3. **Increase container resources:**
   - Edit `docker-compose.yml`
   - Add resource limits:
   ```yaml
   deploy:
     resources:
       limits:
         cpus: '2'
         memory: 4G
   ```

4. **Reduce concurrent executions:**
   - Workflow settings → Execution
   - Limit concurrent runs

---

### Issue: High memory usage

**Symptoms:**
- Container using > 2GB RAM
- Server becoming unresponsive

**Solutions:**

1. **Restart container:**
   ```bash
   docker-compose restart n8n
   ```

2. **Check for memory leaks:**
   - Review execution history
   - Look for workflows running too frequently

3. **Increase server RAM:**
   - Upgrade server to 8GB+ RAM

4. **Clean up old executions:**
   - n8n interface → Settings → Execution Data
   - Delete old execution data

---

## Docker Container Issues

### Issue: Container keeps restarting

**Symptoms:**
```
docker-compose ps
# Shows "Restarting" status
```

**Solutions:**

1. **Check container logs:**
   ```bash
   docker-compose logs n8n
   # Look for crash reason
   ```

2. **Verify environment variables:**
   ```bash
   cat .env
   # Check for syntax errors
   ```

3. **Remove and recreate container:**
   ```bash
   docker-compose down
   docker-compose up -d
   ```

4. **Check Docker version:**
   ```bash
   docker --version
   docker-compose --version
   ```

---

### Issue: "No space left on device" error

**Symptoms:**
```
Error: ENOSPC: no space left on device
```

**Solutions:**

1. **Check disk space:**
   ```bash
   df -h
   ```

2. **Clean up Docker:**
   ```bash
   docker system prune -a
   # Warning: This removes unused images and containers
   ```

3. **Remove old volumes:**
   ```bash
   docker volume prune
   ```

4. **Increase disk space:**
   - Add more storage to server
   - Move Docker data directory

---

## Getting Help

### When to Contact Support

If issues persist after troubleshooting:

1. **Gather diagnostic information:**
   ```bash
   # System info
   docker --version
   docker-compose --version

   # Container status
   docker-compose ps

   # Recent logs
   docker-compose logs --tail=100 n8n

   # Workflow execution logs
   # Screenshot from n8n interface
   ```

2. **Document the issue:**
   - What were you trying to do?
   - What happened instead?
   - What troubleshooting steps did you try?
   - Any error messages (full text)

3. **Check existing resources:**
   - GitHub repository issues
   - n8n community forum
   - Official documentation

### Useful Links

- **n8n Documentation:** https://docs.n8n.io
- **n8n Community Forum:** https://community.n8n.io
- **Docker Documentation:** https://docs.docker.com
- **Telegram Bot API:** https://core.telegram.org/bots/api
- **Anthropic Documentation:** https://docs.anthropic.com
- **Mailjet Documentation:** https://dev.mailjet.com

---

## Preventive Measures

### Regular Maintenance

**Weekly:**
- Check execution logs for errors
- Monitor disk space
- Review system resource usage

**Monthly:**
- Update Docker images
- Rotate API keys (security best practice)
- Test backup restoration
- Review and clean old executions

**Quarterly:**
- Update all credentials
- Security audit
- Performance optimization review
- Documentation updates

### Best Practices

1. **Always test in development first**
2. **Keep backups of working workflows**
3. **Monitor execution logs regularly**
4. **Document any custom changes**
5. **Use version control for configuration**
6. **Keep credentials secure**
7. **Regular system updates**

---

## Emergency Procedures

### System Down

1. **Check container status:**
   ```bash
   docker-compose ps
   ```

2. **Restart services:**
   ```bash
   docker-compose restart
   ```

3. **If restart fails:**
   ```bash
   docker-compose down
   docker-compose up -d
   ```

### Data Recovery

1. **Stop containers:**
   ```bash
   docker-compose down
   ```

2. **Restore from backup:**
   ```bash
   # Extract backup
   tar -xzf backup.tar.gz
   # Import workflows
   ```

3. **Restart services:**
   ```bash
   docker-compose up -d
   ```

---

## Appendix: Common Error Codes

| Error Code | Meaning | Common Cause |
|------------|---------|--------------|
| ECONNREFUSED | Connection refused | Service not running or wrong port |
| ETIMEDOUT | Connection timeout | Firewall blocking or network issue |
| ENOTFOUND | Host not found | Wrong hostname or DNS issue |
| 401 | Unauthorized | Invalid credentials or token |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not found | Wrong URL or resource deleted |
| 500 | Internal server error | Server-side issue |
| 502 | Bad gateway | Proxy issue (check Nginx) |
| 503 | Service unavailable | Service overloaded or down |

---

**Last Updated:** November 2025  
**Version:** 2.0.0
