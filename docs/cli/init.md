# init Command

Initialize a new Shoehorn configuration.

## Synopsis

```bash
shoehorn init [flags]
```

## Description

The `init` command creates a new Shoehorn configuration with sensible defaults. It generates:

- Configuration file (`config/config.yml`)
- Environment file (`.env`)
- Database schema
- Example entity manifests

## Usage

### Basic Initialization

```bash
shoehorn init
```

Creates default configuration in current directory:
```
./config/
  ├── config.yml          # Main configuration
  └── rbac-policy.csv     # Access control policies
.env                      # Environment variables (ignored by git)
.env.example              # Example environment file
```

### Interactive Mode

```bash
shoehorn init --interactive
```

Prompts for configuration values:

```
Welcome to Shoehorn setup!

? Enter database host: localhost
? Enter database port: 5432
? Enter database name: shoehorn
? Enter GitHub token: ghp_xxxxxxxxxxxx
? Enable crawler? (Y/n) Y
? Enter API port: 8080

✓ Configuration created at ./config/config.yml
✓ Environment file created at ./.env
✓ Database schema initialized
```

### Environment-Specific Init

```bash
# Development environment
shoehorn init --env development

# Staging environment
shoehorn init --env staging

# Production environment
shoehorn init --env production
```

Each environment creates environment-specific defaults.

## Flags

### Required Flags

None - all configuration can be provided interactively or use defaults.

### Optional Flags

| Flag | Type | Description | Default |
|------|------|-------------|---------|
| `--env` | string | Environment (development, staging, production) | `development` |
| `--interactive` | bool | Interactive configuration mode | `false` |
| `--force` | bool | Overwrite existing configuration | `false` |
| `--config-dir` | string | Configuration directory path | `./config` |
| `--db-host` | string | Database hostname | `localhost` |
| `--db-port` | int | Database port | `5432` |
| `--db-name` | string | Database name | `shoehorn` |
| `--github-token` | string | GitHub token | (required for GitHub integration) |
| `--no-examples` | bool | Skip creating example files | `false` |

### Global Flags

| Flag | Type | Description |
|------|------|-------------|
| `--verbose` | bool | Verbose output |
| `--quiet` | bool | Suppress output |
| `--help` | bool | Show help |

## Examples

### Example 1: Basic Init

```bash
shoehorn init
```

**Output:**
```
Initializing Shoehorn configuration...
✓ Created config directory: ./config
✓ Created config file: ./config/config.yml
✓ Created environment file: ./.env
✓ Created example entities: ./examples/

Configuration initialized successfully!

Next steps:
1. Edit configuration: vim ./config/config.yml
2. Set environment variables: vim ./.env
3. Run migrations: shoehorn migrate up
4. Start server: shoehorn serve
```

### Example 2: Production Init

```bash
shoehorn init --env production --config-dir /etc/shoehorn
```

Creates production-ready configuration in `/etc/shoehorn/` with:
- SSL enabled
- Connection pooling
- Rate limiting
- Security headers

### Example 3: Custom Database

```bash
shoehorn init \
  --db-host postgres.internal \
  --db-port 5432 \
  --db-name shoehorn_prod \
  --env production
```

### Example 4: Interactive Setup

```bash
shoehorn init --interactive
```

**Interactive Prompts:**
```
? Select environment: production
? Enter database host: db.company.com
? Enter database port: 5432
? Database name: shoehorn
? Database user: shoehorn_app
? Database password: ****************
? Enable SSL for database? Yes
? Enter Redis host: redis.company.com
? Enable crawler? Yes
? Crawler schedule (cron): 0 * * * *
? Enter GitHub token: ghp_******************
? Enable authentication? Yes
? OAuth2 provider URL: https://oauth.company.com

✓ Configuration saved to ./config/config.yml
```

### Example 5: Minimal Setup (No Examples)

```bash
shoehorn init --no-examples --quiet
```

Creates only essential configuration files, no example entities.

## Generated Files

### config/config.yml

Main configuration file:

```yaml
server:
  host: "0.0.0.0"
  port: 8080

database:
  host: localhost
  port: 5432
  name: shoehorn
  user: shoehorn
  password: ${DB_PASSWORD}
  ssl_mode: disable

cache:
  enabled: true
  host: localhost
  port: 6379

search:
  host: localhost
  port: 8108
  protocol: http

crawler:
  enabled: true
  schedule: "0 * * * *"

github:
  token: ${GITHUB_TOKEN}
```

### .env

Environment variables:

```bash
# Database
DB_PASSWORD=your_secure_password
DB_SSL_MODE=disable

# GitHub
GITHUB_TOKEN=ghp_your_token_here

# Redis
REDIS_PASSWORD=

# Typesense
TYPESENSE_API_KEY=dev_key

# Logging
LOG_LEVEL=info
```

### .env.example

Template for other developers:

```bash
# Database
DB_PASSWORD=
DB_SSL_MODE=disable

# GitHub
GITHUB_TOKEN=

# Redis
REDIS_PASSWORD=

# Typesense
TYPESENSE_API_KEY=

# Logging
LOG_LEVEL=info
```

## Environment Defaults

### Development

```yaml
logging:
  level: debug
database:
  ssl_mode: disable
crawler:
  concurrent_repos: 5
features:
  hot_reload: true
```

### Staging

```yaml
logging:
  level: info
database:
  ssl_mode: require
crawler:
  concurrent_repos: 10
features:
  hot_reload: false
```

### Production

```yaml
logging:
  level: warn
  format: json
database:
  ssl_mode: verify-full
  max_connections: 50
crawler:
  concurrent_repos: 20
security:
  rate_limit:
    enabled: true
monitoring:
  metrics:
    enabled: true
  tracing:
    enabled: true
```

## Post-Initialization Steps

After running `init`, complete these steps:

### 1. Update Configuration

```bash
vim ./config/config.yml
```

Review and customize:
- Database credentials
- GitHub token
- Feature flags
- Security settings

### 2. Set Environment Variables

```bash
vim ./.env
```

Add required secrets:
```bash
DB_PASSWORD=your_secure_password
GITHUB_TOKEN=ghp_your_actual_token
REDIS_PASSWORD=redis_password
TYPESENSE_API_KEY=search_api_key
```

### 3. Validate Configuration

```bash
shoehorn config validate
```

### 4. Run Database Migrations

```bash
shoehorn migrate up
```

### 5. Start Services

```bash
# Start dependencies
docker-compose up -d postgres redis typesense

# Start Shoehorn
shoehorn serve
```

## Common Issues

### Config Directory Exists

```bash
ERROR: Config directory already exists: ./config
Use --force to overwrite
```

**Solution:**
```bash
shoehorn init --force
```

### Permission Denied

```bash
ERROR: Permission denied: /etc/shoehorn
```

**Solution:**
```bash
sudo shoehorn init --config-dir /etc/shoehorn
# Or use user-accessible directory
shoehorn init --config-dir ~/.shoehorn
```

### Invalid Environment

```bash
ERROR: Invalid environment: prod
Valid options: development, staging, production
```

**Solution:**
```bash
shoehorn init --env production
```

## Best Practices

1. **Version Control**
   - Commit `config.yml` and `.env.example`
   - Add `.env` to `.gitignore`

2. **Security**
   - Never commit secrets
   - Use strong passwords
   - Enable SSL in production

3. **Documentation**
   - Document custom configuration
   - Keep `.env.example` updated
   - Add comments to `config.yml`

4. **Validation**
   - Always validate after init: `shoehorn config validate`
   - Test database connection: `shoehorn config test-db`

5. **Environment Separation**
   - Use separate configs per environment
   - Different databases per environment
   - Separate API keys

## Advanced Usage

### Custom Config Template

```bash
# Use custom template
shoehorn init --template /path/to/template.yml
```

### Skip Interactive Prompts

```bash
# Provide all values via flags
shoehorn init \
  --env production \
  --db-host db.prod.internal \
  --db-port 5432 \
  --db-name shoehorn \
  --github-token ghp_token \
  --force
```

### Generate Only Specific Files

```bash
# Only config file
shoehorn init --only config

# Only environment file
shoehorn init --only env

# Multiple specific files
shoehorn init --only config,env,rbac
```

## See Also

- **[Configuration Files](../config/files.md)** - Configuration reference
- **[Environment Variables](../config/env.md)** - Environment variable reference
- **[deploy Command](deploy.md)** - Deploy to production
