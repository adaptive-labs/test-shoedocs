# Environment Variables

Configure your Shoedocs deployment using environment variables.

## Overview

Shoedocs respects standard environment variables for configuration. This guide covers the most commonly used variables.

## Required Variables

### SHOEHORN_API_URL

The base URL for the Shoehorn API.

**Example:**
```bash
SHOEHORN_API_URL=https://idp.company.com/api/v1
```

**Default:** `http://localhost:8080/api/v1`

**Usage:**
- Used by the crawler to communicate with the API
- Used by the frontend for data fetching
- Must be accessible from both crawler and web services

### GITHUB_TOKEN

GitHub Personal Access Token for API access.

**Example:**
```bash
GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
```

**Permissions Required:**
- `repo` - Full control of private repositories
- `read:org` - Read organization membership

**Security Notes:**
- Store securely in secrets manager (Vault, AWS Secrets Manager, etc.)
- Rotate regularly (every 90 days recommended)
- Use fine-grained tokens when possible
- Never commit to Git

## Optional Variables

### TYPESENSE_API_KEY

API key for Typesense search engine.

**Example:**
```bash
TYPESENSE_API_KEY=xyz123abc456def789
```

**Default:** Generated automatically for local development

**When Required:**
- Production deployments
- Remote Typesense instances
- Multi-tenant setups

### REDIS_URL

Connection string for Redis/Valkey cache.

**Example:**
```bash
REDIS_URL=redis://localhost:6379
```

**Default:** `redis://localhost:6379`

**Format:**
```
redis://[user:password@]host[:port][/database]
redis://localhost:6379/0
rediss://user:pass@secure-redis.com:6380/1  # SSL
```

### LOG_LEVEL

Controls logging verbosity.

**Example:**
```bash
LOG_LEVEL=debug
```

**Options:**
- `debug` - Verbose logging (development)
- `info` - Standard logging (default)
- `warn` - Warnings and errors only
- `error` - Errors only (production)

### PORT

HTTP server port.

**Example:**
```bash
PORT=8080
```

**Default:** `8080`

**Notes:**
- API service listens on this port
- Ensure port is not already in use
- Use `0.0.0.0` for listening on all interfaces

## Search & Indexing Variables

### TYPESENSE_HOST

Typesense server hostname.

**Example:**
```bash
TYPESENSE_HOST=localhost
```

**Default:** `localhost`

### TYPESENSE_PORT

Typesense server port.

**Example:**
```bash
TYPESENSE_PORT=8108
```

**Default:** `8108`

### TYPESENSE_PROTOCOL

Protocol for Typesense connection.

**Example:**
```bash
TYPESENSE_PROTOCOL=https
```

**Options:**
- `http` - Unencrypted (development)
- `https` - Encrypted (production)

**Default:** `http`

## Database Variables

### DB_HOST

PostgreSQL database hostname.

**Example:**
```bash
DB_HOST=postgres.internal.company.com
```

**Default:** `localhost`

### DB_PORT

PostgreSQL database port.

**Example:**
```bash
DB_PORT=5432
```

**Default:** `5432`

### DB_NAME

Database name.

**Example:**
```bash
DB_NAME=shoehorn
```

**Default:** `shoehorn`

### DB_USER

Database username.

**Example:**
```bash
DB_USER=shoehorn_app
```

**Default:** `postgres`

### DB_PASSWORD

Database password.

**Example:**
```bash
DB_PASSWORD=secure_password_here
```

**Security:**
- Store in secrets manager
- Use strong passwords (16+ characters)
- Rotate regularly
- Never commit to Git

### DB_SSL_MODE

SSL mode for database connection.

**Example:**
```bash
DB_SSL_MODE=require
```

**Options:**
- `disable` - No SSL (local dev only)
- `require` - Require SSL (recommended)
- `verify-ca` - Verify certificate authority
- `verify-full` - Full certificate verification

**Default:** `disable`

## Event Bus Variables

### KAFKA_BROKERS

Comma-separated list of Kafka/Redpanda brokers.

**Example:**
```bash
KAFKA_BROKERS=redpanda-1:9092,redpanda-2:9092,redpanda-3:9092
```

**Default:** `localhost:9092`

### KAFKA_TOPIC_PREFIX

Prefix for Kafka topics.

**Example:**
```bash
KAFKA_TOPIC_PREFIX=prod-shoehorn
```

**Default:** `shoehorn`

**Result Topics:**
```
prod-shoehorn.crawl.request.v1
prod-shoehorn.entity.updated.v1
prod-shoehorn.document.indexed.v1
```

## Configuration Files

For more complex configuration, see [Config Files](files.md).

## Environment-Specific Examples

### Development (.env.development)

```bash
# API
SHOEHORN_API_URL=http://localhost:8080/api/v1
PORT=8080

# Logging
LOG_LEVEL=debug

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=shoehorn_dev
DB_USER=postgres
DB_PASSWORD=dev_password
DB_SSL_MODE=disable

# Redis
REDIS_URL=redis://localhost:6379/0

# Typesense
TYPESENSE_HOST=localhost
TYPESENSE_PORT=8108
TYPESENSE_PROTOCOL=http
TYPESENSE_API_KEY=dev_api_key

# GitHub
GITHUB_TOKEN=ghp_dev_token_here

# Event Bus
KAFKA_BROKERS=localhost:9092
KAFKA_TOPIC_PREFIX=dev-shoehorn
```

### Production (.env.production)

```bash
# API
SHOEHORN_API_URL=https://idp.company.com/api/v1
PORT=8080

# Logging
LOG_LEVEL=info

# Database
DB_HOST=postgres.prod.internal
DB_PORT=5432
DB_NAME=shoehorn
DB_USER=shoehorn_prod
DB_PASSWORD=${SECRET_DB_PASSWORD}  # From secrets manager
DB_SSL_MODE=verify-full

# Redis
REDIS_URL=rediss://:${SECRET_REDIS_PASSWORD}@redis.prod.internal:6380/0

# Typesense
TYPESENSE_HOST=search.prod.internal
TYPESENSE_PORT=443
TYPESENSE_PROTOCOL=https
TYPESENSE_API_KEY=${SECRET_TYPESENSE_KEY}

# GitHub
GITHUB_TOKEN=${SECRET_GITHUB_TOKEN}

# Event Bus
KAFKA_BROKERS=kafka-1.prod:9092,kafka-2.prod:9092,kafka-3.prod:9092
KAFKA_TOPIC_PREFIX=prod-shoehorn
```

## Best Practices

1. **Use Secrets Management**
   - Vault, AWS Secrets Manager, Azure Key Vault
   - Rotate credentials regularly
   - Never commit secrets to Git

2. **Environment Separation**
   - Different credentials per environment
   - Dedicated databases per environment
   - Separate Kafka topics

3. **SSL/TLS in Production**
   - Enable SSL for all external connections
   - Use certificate verification
   - Keep certificates up to date

4. **Monitoring**
   - Log all environment variable loads
   - Alert on missing required variables
   - Monitor for credential expiration

## Troubleshooting

### Missing Variable Error

```
ERROR: Required environment variable GITHUB_TOKEN not set
```

**Solution:** Export the variable before starting:
```bash
export GITHUB_TOKEN=ghp_your_token_here
./shoehorn
```

### Connection Refused

```
ERROR: Failed to connect to database at postgres:5432
```

**Checklist:**
1. Is database running? `pg_isready -h localhost -p 5432`
2. Is host/port correct in `DB_HOST` and `DB_PORT`?
3. Is network accessible? `telnet postgres 5432`
4. Are credentials correct?

### SSL Certificate Error

```
ERROR: x509: certificate signed by unknown authority
```

**Solution:** Check `DB_SSL_MODE`:
```bash
# Development - disable SSL verification
DB_SSL_MODE=disable

# Production - verify certificates
DB_SSL_MODE=verify-full
```

## Next Steps

- **[Config Files](files.md)** - Learn about configuration files
- **[CLI Commands](../cli/overview.md)** - Explore CLI configuration options
