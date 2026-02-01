# Authentication

Manage your Base44 CLI authentication.

## Login

Authenticate with Base44:

```bash
base44 login
```

This opens your browser for OAuth authentication. After successful login, credentials are stored locally.

### Login Options

```bash
# Login with specific email
base44 login --email user@example.com

# Login with API key (for CI/CD)
base44 login --api-key YOUR_API_KEY

# Login to specific environment
base44 login --env staging
```

## Logout

End your current session:

```bash
base44 logout
```

This removes local credentials for the current device.

### Logout Options

```bash
# Logout from all devices
base44 logout --all

# Logout from specific environment
base44 logout --env staging
```

## Check Current User

Verify your authentication status:

```bash
base44 whoami
```

Output:

```
Logged in as: user@example.com
User ID: usr_abc123
Organization: My Company
Environment: production
```

## Credential Storage

Credentials are stored in:

- **macOS**: Keychain
- **Linux**: Secret Service / encrypted file
- **Windows**: Credential Manager

Location of config: `~/.base44/config.json`

## API Keys

For CI/CD and automated workflows, use API keys:

### Generate API Key

1. Go to Base44 dashboard
2. Navigate to **Settings > API Keys**
3. Click **Generate New Key**
4. Copy and store securely

### Use API Key

```bash
# Set via environment variable
export BASE44_API_KEY=your_api_key
base44 deploy

# Or pass directly
base44 deploy --api-key your_api_key
```

### API Key in CI/CD

```yaml
# GitHub Actions example
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Install Base44 CLI
        run: npm install -g base44

      - name: Deploy
        env:
          BASE44_API_KEY: ${{ secrets.BASE44_API_KEY }}
        run: base44 deploy
```

## Multiple Environments

Manage authentication for different environments:

```bash
# Login to staging
base44 login --env staging

# Login to production
base44 login --env production

# Switch active environment
base44 env use production

# Check current environment
base44 env current
```

## Session Management

### Session Expiry

Sessions expire after 30 days of inactivity. Re-authenticate with:

```bash
base44 login
```

### Refresh Token

Tokens are automatically refreshed. If issues occur:

```bash
# Force token refresh
base44 auth refresh
```

## Troubleshooting

### "Not authenticated"

```bash
# Check status
base44 whoami

# Re-authenticate
base44 login
```

### "Session expired"

```bash
base44 login
```

### "Invalid API key"

1. Verify key in dashboard
2. Check for typos
3. Ensure key hasn't been revoked
4. Generate new key if needed

### "Permission denied"

Your account may lack permissions for the action. Contact your organization admin.

### Browser Doesn't Open

```bash
# Manual login URL
base44 login --no-browser
# Follow printed URL manually
```

## Security Best Practices

1. **Never commit credentials** - Use environment variables
2. **Rotate API keys** - Periodically regenerate keys
3. **Use scoped keys** - Create keys with minimal permissions
4. **Audit access** - Review active sessions in dashboard
5. **Logout from shared machines** - Always `base44 logout`

## Organization Access

If you belong to multiple organizations:

```bash
# List organizations
base44 orgs list

# Switch organization
base44 orgs switch org_abc123

# Check current org
base44 whoami
```
