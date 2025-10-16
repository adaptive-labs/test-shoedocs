# Configuration Files

Learn how to configure Shoedocs using YAML configuration files.

## Overview

In addition to environment variables, Shoedocs supports configuration via YAML files. This provides a more structured approach for complex configurations.

## Configuration File Locations

Shoedocs searches for configuration files in the following order (first match wins):

1. `./config/config.yml` - Current directory
2. `./config.yml` - Current directory
3. `~/.shoehorn/config.yml` - User home directory
4. `/etc/shoehorn/config.yml` - System-wide (Linux/macOS)
5. Environment variable: `SHOEHORN_CONFIG` - Custom path

**Example:**
```bash
export SHOEHORN_CONFIG=/opt/shoehorn/prod-config.yml
./shoehorn
```

## Basic Configuration

### config.yml

```yaml
# Shoehorn Configuration
# Version: 1.0

# Server Configuration
server:
  host: "0.0.0.0"
  port: 8080
  read_timeout: 30s
  write_timeout: 30s
  idle_timeout: 120s

# Logging
logging:
  level: info  # debug, info, warn, error
  format: json # json, text
  output: stdout # stdout, stderr, file
  file_path: /var/log/shoehorn/app.log # if output: file

# Database
database:
  host: localhost
  port: 5432
  name: shoehorn
  user: shoehorn
  password: ${DB_PASSWORD} # From environment
  ssl_mode: require
  max_connections: 25
  max_idle_connections: 5
  connection_lifetime: 5m

# Redis/Valkey Cache
cache:
  enabled: true
  host: localhost
  port: 6379
  db: 0
  password: ${REDIS_PASSWORD}
  ttl: 1h
  max_entries: 10000

# Search (Typesense)
search:
  host: localhost
  port: 8108
  protocol: http
  api_key: ${TYPESENSE_API_KEY}
  timeout: 30s
  collections:
    entities: catalog_entities
    documents: documents

# Event Bus (Kafka/Redpanda)
eventbus:
  enabled: true
  brokers:
    - localhost:9092
  topic_prefix: shoehorn
  consumer_group: shoehorn-workers
  auto_commit: true

# GitHub Integration
github:
  token: ${GITHUB_TOKEN}
  base_url: https://api.github.com
  timeout: 30s
  rate_limit:
    enabled: true
    requests_per_hour: 5000

# Crawler Configuration
crawler:
  enabled: true
  schedule: "0 * * * *" # Hourly
  batch_size: 100
  concurrent_repos: 10
  timeout: 5m
  retry_attempts: 3
  retry_delay: 30s

# Authentication
auth:
  enabled: true
  provider: oauth2-proxy
  admin_groups:
    - /admins
    - /platform-team
```

## Advanced Configuration

### Feature Flags

```yaml
features:
  techdocs:
    enabled: true
    auto_index: true
    cache_ttl: 6h

  search:
    enabled: true
    fuzzy_matching: true
    typo_tolerance: 2

  favorites:
    enabled: true
    max_per_user: 100

  recent_activity:
    enabled: true
    retention_days: 90
```

### Storage Configuration

```yaml
storage:
  # Object storage for rendered docs
  provider: s3 # s3, gcs, azure, local

  s3:
    bucket: shoehorn-docs
    region: us-west-2
    access_key_id: ${AWS_ACCESS_KEY_ID}
    secret_access_key: ${AWS_SECRET_ACCESS_KEY}
    endpoint: https://s3.amazonaws.com

  local:
    path: /var/lib/shoehorn/storage
```

### CDN Configuration

```yaml
cdn:
  enabled: true
  provider: cloudflare
  base_url: https://cdn.idp.company.com
  cache_ttl: 24h
  purge_on_update: true
```

## Crawler Configuration

### Repository Patterns

```yaml
crawler:
  enabled: true

  # GitHub organization repositories
  repositories:
    - pattern: "org:my-company"
      priority: high
      crawl_interval: 1h

    - pattern: "org:my-company topic:platform"
      priority: critical
      crawl_interval: 15m

    - pattern: "user:external-contributor"
      priority: low
      crawl_interval: 24h

  # File patterns to include
  include_patterns:
    - "**/*.md"
    - "**/*.mdx"
    - "**/README.*"
    - "**/docs/**"

  # File patterns to exclude
  exclude_patterns:
    - "**/node_modules/**"
    - "**/vendor/**"
    - "**/.git/**"
    - "**/dist/**"

  # Maximum file size to crawl (bytes)
  max_file_size: 5242880 # 5MB
```

### Crawl Scheduling

```yaml
crawler:
  # Default schedule (cron format)
  schedule: "0 * * * *" # Every hour

  # Per-priority schedules
  schedules:
    critical: "*/15 * * * *" # Every 15 minutes
    high: "0 * * * *"        # Every hour
    normal: "0 */6 * * *"    # Every 6 hours
    low: "0 0 * * *"         # Daily

  # Webhook-triggered immediate crawls
  webhooks:
    enabled: true
    secret: ${GITHUB_WEBHOOK_SECRET}
```

## Search Configuration

### Index Settings

```yaml
search:
  typesense:
    host: localhost
    port: 8108
    protocol: http
    api_key: ${TYPESENSE_API_KEY}

  # Collection configurations
  collections:
    documents:
      name: documents
      fields:
        - name: id
          type: string
        - name: title
          type: string
        - name: content
          type: string
        - name: url
          type: string
        - name: type
          type: string
          facet: true
        - name: tags
          type: string[]
          facet: true
        - name: updated_at
          type: int64

      # Search settings
      default_sorting_field: updated_at
      token_separators: ["-", "_"]
      symbols_to_index: ["#", "@"]
```

### Search Ranking

```yaml
search:
  ranking:
    # Field weights for relevance scoring
    weights:
      title: 10
      description: 5
      content: 1
      tags: 3

    # Boost factors
    boost:
      recent_docs: 1.2 # Boost docs updated in last 7 days
      popular_docs: 1.5 # Boost frequently accessed docs
      favorite_docs: 2.0 # Boost user's favorites
```

## Security Configuration

### Access Control

```yaml
security:
  # Role-based access control
  rbac:
    enabled: true
    model_path: ./config/rbac-model.conf
    policy_path: ./config/rbac-policy.csv

  # Authentication
  auth:
    provider: oauth2-proxy
    oauth2_proxy_url: http://oauth2-proxy:4180

    # Admin users/groups
    admins:
      groups:
        - /admins
        - /platform-team
      users:
        - admin@company.com

  # API rate limiting
  rate_limit:
    enabled: true
    requests_per_minute: 60
    burst: 100

    # Exemptions
    exempt_paths:
      - /health
      - /metrics
    exempt_ips:
      - 10.0.0.0/8 # Internal network
```

### CORS Configuration

```yaml
server:
  cors:
    enabled: true
    allowed_origins:
      - https://idp.company.com
      - https://app.company.com
    allowed_methods:
      - GET
      - POST
      - PUT
      - DELETE
    allowed_headers:
      - Authorization
      - Content-Type
    expose_headers:
      - X-Request-ID
    max_age: 3600
```

## Monitoring & Observability

### Metrics

```yaml
monitoring:
  # Prometheus metrics
  metrics:
    enabled: true
    endpoint: /metrics
    port: 9090

    # Custom metrics
    custom_metrics:
      - name: crawler_repos_total
        type: counter
        help: Total repositories crawled

      - name: search_queries_duration
        type: histogram
        help: Search query duration
        buckets: [0.1, 0.5, 1.0, 2.0, 5.0]

  # Health checks
  health:
    enabled: true
    endpoint: /health
    checks:
      - name: database
        timeout: 5s
      - name: redis
        timeout: 2s
      - name: typesense
        timeout: 3s
```

### Tracing

```yaml
monitoring:
  # OpenTelemetry tracing
  tracing:
    enabled: true
    provider: jaeger
    endpoint: http://jaeger:14268/api/traces
    sample_rate: 0.1 # Sample 10% of traces

    # Trace all these operations
    trace_operations:
      - http_requests
      - database_queries
      - search_queries
      - crawler_runs
```

## Example Configurations

### Development Config

**config/development.yml**
```yaml
server:
  host: localhost
  port: 8080

logging:
  level: debug
  format: text

database:
  host: localhost
  ssl_mode: disable

cache:
  enabled: true
  host: localhost

search:
  host: localhost
  protocol: http

crawler:
  enabled: true
  concurrent_repos: 5

features:
  techdocs:
    enabled: true
    auto_index: true
```

### Production Config

**config/production.yml**
```yaml
server:
  host: 0.0.0.0
  port: 8080
  read_timeout: 30s
  write_timeout: 30s

logging:
  level: info
  format: json
  output: file
  file_path: /var/log/shoehorn/app.log

database:
  host: postgres.prod.internal
  ssl_mode: verify-full
  max_connections: 50

cache:
  enabled: true
  host: redis.prod.internal
  password: ${REDIS_PASSWORD}

search:
  host: search.prod.internal
  protocol: https

crawler:
  enabled: true
  concurrent_repos: 20
  schedule: "0 * * * *"

monitoring:
  metrics:
    enabled: true
  tracing:
    enabled: true
    sample_rate: 0.05

security:
  rbac:
    enabled: true
  rate_limit:
    enabled: true
```

## Configuration Validation

Use the CLI to validate your configuration:

```bash
# Validate config file
shoehorn config validate ./config/config.yml

# Print merged configuration (env + file)
shoehorn config print

# Test database connection
shoehorn config test-db

# Test search connection
shoehorn config test-search
```

## Configuration Precedence

Configuration values are loaded in this order (later overrides earlier):

1. Default values (built-in)
2. Configuration file (config.yml)
3. Environment variables
4. Command-line flags

**Example:**
```bash
# Config file sets: LOG_LEVEL=info
# Environment variable sets: LOG_LEVEL=debug
# CLI flag sets: --log-level=warn

# Result: LOG_LEVEL=warn (CLI wins)
```

## Best Practices

1. **Separate Configs Per Environment**
   - `config/development.yml`
   - `config/staging.yml`
   - `config/production.yml`

2. **Use Environment Variables for Secrets**
   - Never commit secrets to Git
   - Reference env vars: `${SECRET_NAME}`

3. **Validate Before Deployment**
   - Use `shoehorn config validate`
   - Test connections with `test-db`, `test-search`

4. **Version Control**
   - Commit config files (without secrets)
   - Document changes in commit messages
   - Use `.gitignore` for local overrides

5. **Keep It Simple**
   - Start with minimal config
   - Add complexity only when needed
   - Document custom settings

## Troubleshooting

### Config File Not Found

```bash
ERROR: Configuration file not found at ./config/config.yml
```

**Solution:** Specify custom path:
```bash
export SHOEHORN_CONFIG=/path/to/config.yml
```

### Invalid YAML Syntax

```bash
ERROR: Failed to parse config.yml: yaml: line 42: mapping values are not allowed in this context
```

**Solution:** Validate YAML syntax:
```bash
yamllint config.yml
```

### Environment Variable Not Expanded

```yaml
database:
  password: ${DB_PASSWORD}
```

**Problem:** Password shows as literal `${DB_PASSWORD}`

**Solution:** Ensure environment variable is exported:
```bash
export DB_PASSWORD=your_password_here
```

## Next Steps

- **[Environment Variables](env.md)** - Learn about environment configuration
- **[CLI Commands](../cli/overview.md)** - Explore CLI configuration commands
