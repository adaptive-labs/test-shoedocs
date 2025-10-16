# deploy Command

Deploy Shoehorn to production, staging, or other environments.

## Synopsis

```bash
shoehorn deploy [environment] [flags]
```

## Description

The `deploy` command handles deployment of Shoehorn to various environments. It supports:

- Kubernetes deployments (via Helm)
- Docker Compose deployments
- Binary deployments
- Rolling updates with zero downtime
- Health checks and rollback

## Usage

### Basic Deployment

```bash
# Deploy to production
shoehorn deploy production

# Deploy to staging
shoehorn deploy staging

# Deploy to development
shoehorn deploy development
```

### Deploy Specific Version

```bash
# Deploy tagged version
shoehorn deploy production --version v1.2.3

# Deploy from branch
shoehorn deploy staging --branch feature/new-ui

# Deploy specific commit
shoehorn deploy development --commit abc123
```

### Deployment Methods

```bash
# Kubernetes deployment (default)
shoehorn deploy production --method kubernetes

# Docker Compose deployment
shoehorn deploy staging --method compose

# Binary deployment
shoehorn deploy production --method binary
```

## Flags

### Required Flags

| Flag | Type | Description |
|------|------|-------------|
| `environment` | string | Target environment (positional argument) |

### Optional Flags

| Flag | Type | Description | Default |
|------|------|-------------|---------|
| `--version` | string | Version to deploy | `latest` |
| `--branch` | string | Git branch to deploy | - |
| `--commit` | string | Specific commit to deploy | - |
| `--method` | string | Deployment method (kubernetes, compose, binary) | `kubernetes` |
| `--replicas` | int | Number of replicas | `3` |
| `--config` | string | Config file path | `./config/{env}.yml` |
| `--namespace` | string | Kubernetes namespace | `shoehorn` |
| `--dry-run` | bool | Simulate deployment | `false` |
| `--wait` | bool | Wait for deployment to complete | `true` |
| `--timeout` | duration | Deployment timeout | `10m` |
| `--rollback` | bool | Rollback to previous version | `false` |
| `--force` | bool | Force deployment (skip checks) | `false` |

## Examples

### Example 1: Production Deployment

```bash
shoehorn deploy production \
  --version v1.2.3 \
  --config /etc/shoehorn/prod-config.yml \
  --replicas 5 \
  --wait
```

**Output:**
```
Deploying Shoehorn v1.2.3 to production...
✓ Validating configuration
✓ Checking prerequisites
✓ Building deployment manifest
✓ Deploying to Kubernetes (namespace: shoehorn)
  - Updating deployment: shoehorn-api (5 replicas)
  - Updating deployment: shoehorn-crawler (2 replicas)
  - Updating deployment: shoehorn-worker (3 replicas)
✓ Waiting for rollout to complete... (2m 34s)
✓ Running health checks
✓ Deployment successful!

Deployed services:
- API: https://idp.company.com
- Metrics: https://idp.company.com/metrics
- Health: https://idp.company.com/health

Next steps:
- Monitor logs: kubectl logs -n shoehorn -l app=shoehorn --tail=100
- Check metrics: https://grafana.company.com/d/shoehorn
```

### Example 2: Staging Deployment with Dry Run

```bash
shoehorn deploy staging --dry-run
```

Shows what would be deployed without actually deploying.

### Example 3: Docker Compose Deployment

```bash
shoehorn deploy development \
  --method compose \
  --config ./config/dev-config.yml
```

**Output:**
```
Deploying Shoehorn to development using Docker Compose...
✓ Validating docker-compose.yml
✓ Pulling images
✓ Starting services
  - postgres: started
  - redis: started
  - typesense: started
  - api: started
  - crawler: started
  - web: started
✓ Running health checks
✓ All services healthy

Services available at:
- API: http://localhost:8080
- Web: http://localhost:3000
- Typesense: http://localhost:8108
```

### Example 4: Rollback

```bash
# Rollback to previous version
shoehorn deploy production --rollback

# Rollback to specific version
shoehorn deploy production --version v1.2.2
```

### Example 5: Binary Deployment

```bash
shoehorn deploy production \
  --method binary \
  --version v1.2.3 \
  --target-host app01.prod.internal
```

## Deployment Methods

### Kubernetes (Helm)

Default deployment method for production.

```bash
shoehorn deploy production \
  --method kubernetes \
  --namespace shoehorn \
  --replicas 5
```

**What happens:**
1. Validates Helm chart
2. Updates ConfigMaps and Secrets
3. Applies Helm chart
4. Waits for rollout
5. Runs health checks

**Requirements:**
- `kubectl` configured
- `helm` installed
- Access to Kubernetes cluster

### Docker Compose

Suitable for staging and development.

```bash
shoehorn deploy staging \
  --method compose \
  --config ./compose/staging.yml
```

**What happens:**
1. Validates docker-compose.yml
2. Pulls latest images
3. Starts/restarts services
4. Runs health checks

**Requirements:**
- `docker` and `docker-compose` installed
- Access to Docker registry

### Binary Deployment

Direct binary deployment to servers.

```bash
shoehorn deploy production \
  --method binary \
  --target-host app01.prod.internal
```

**What happens:**
1. Downloads binary
2. Stops running instance
3. Replaces binary
4. Starts new instance
5. Runs health checks

**Requirements:**
- SSH access to target host
- `systemd` for service management

## Pre-Deployment Checks

Before deploying, the command runs these checks:

1. **Configuration Validation**
   ```bash
   ✓ Config file syntax valid
   ✓ Required variables set
   ✓ Database connection successful
   ✓ Redis connection successful
   ✓ Typesense connection successful
   ```

2. **Infrastructure Checks**
   ```bash
   ✓ Kubernetes cluster accessible
   ✓ Namespace exists
   ✓ Docker registry accessible
   ✓ Sufficient resources available
   ```

3. **Version Checks**
   ```bash
   ✓ Version exists in registry
   ✓ No conflicting deployments
   ✓ Migration compatibility
   ```

### Skip Checks

```bash
# Skip all checks (dangerous!)
shoehorn deploy production --force --skip-checks

# Skip specific checks
shoehorn deploy production --skip-checks=resources,migrations
```

## Post-Deployment

After deployment, the command:

1. **Runs Health Checks**
   ```bash
   ✓ Health endpoint: HTTP 200
   ✓ Database connectivity: OK
   ✓ Cache connectivity: OK
   ✓ Search service: OK
   ```

2. **Verifies Metrics**
   ```bash
   ✓ Metrics endpoint: HTTP 200
   ✓ All replicas ready: 5/5
   ✓ No error logs
   ```

3. **Smoke Tests** (if enabled)
   ```bash
   ✓ Can list entities: OK
   ✓ Can search: OK
   ✓ Can authenticate: OK
   ```

## Rollout Strategies

### Rolling Update (Default)

Gradually replaces old pods with new ones.

```bash
shoehorn deploy production \
  --strategy rolling \
  --max-unavailable 1 \
  --max-surge 1
```

**Zero downtime:** ✅

### Blue-Green Deployment

Deploys new version alongside old, then switches traffic.

```bash
shoehorn deploy production \
  --strategy blue-green \
  --color green
```

**Zero downtime:** ✅
**Rollback:** Instant (switch back to blue)

### Canary Deployment

Gradually shifts traffic to new version.

```bash
shoehorn deploy production \
  --strategy canary \
  --canary-weight 10%
```

**Zero downtime:** ✅
**Risk mitigation:** ✅ (incremental)

### Recreate

Stops all old pods before starting new ones.

```bash
shoehorn deploy staging --strategy recreate
```

**Zero downtime:** ❌
**Use case:** Staging environments

## Health Checks

The deploy command runs comprehensive health checks:

```bash
# Default health checks
✓ HTTP health endpoint: /health
✓ Database connection
✓ Cache connection
✓ Search service

# Extended health checks (--extended)
✓ Can query database
✓ Can write to cache
✓ Can index document
✓ Can perform search
✓ Can authenticate user
```

### Custom Health Checks

Define custom checks in config:

```yaml
deploy:
  health_checks:
    - name: database_query
      endpoint: /health/db
      method: GET
      timeout: 5s

    - name: search_query
      endpoint: /health/search
      method: GET
      timeout: 3s
```

## Monitoring Deployment

### Real-Time Logs

```bash
# Follow deployment logs
shoehorn deploy production --follow-logs

# Output:
2025-10-16 10:30:00 | INFO | Starting deployment...
2025-10-16 10:30:05 | INFO | Rolling out pod 1/5...
2025-10-16 10:30:15 | INFO | Pod 1/5 ready
2025-10-16 10:30:20 | INFO | Rolling out pod 2/5...
```

### Deployment Status

```bash
# Check deployment status
shoehorn deploy status production

# Output:
Deployment: shoehorn-api
Environment: production
Version: v1.2.3
Status: Running
Replicas: 5/5 ready
Last deployed: 2 minutes ago
```

## Rollback

### Automatic Rollback

If health checks fail, automatically rollback:

```bash
shoehorn deploy production --auto-rollback
```

### Manual Rollback

```bash
# Rollback to previous version
shoehorn deploy production --rollback

# Rollback to specific version
shoehorn deploy production --rollback --version v1.2.2
```

## Environment-Specific Configuration

### Production

```yaml
deploy:
  replicas: 5
  resources:
    cpu: 1000m
    memory: 2Gi
  health_checks:
    initial_delay: 30s
    period: 10s
  strategy:
    type: rolling
    max_unavailable: 1
    max_surge: 1
```

### Staging

```yaml
deploy:
  replicas: 2
  resources:
    cpu: 500m
    memory: 1Gi
  health_checks:
    initial_delay: 10s
    period: 30s
  strategy:
    type: recreate
```

## Troubleshooting

### Deployment Fails

```bash
ERROR: Deployment failed: health checks failed after 10m
```

**Debug steps:**
```bash
# Check pod status
kubectl get pods -n shoehorn

# Check logs
kubectl logs -n shoehorn -l app=shoehorn --tail=50

# Describe pod
kubectl describe pod -n shoehorn shoehorn-api-xxx

# Rollback
shoehorn deploy production --rollback
```

### Timeout

```bash
ERROR: Deployment timed out after 10m
```

**Solution:**
```bash
# Increase timeout
shoehorn deploy production --timeout 30m

# Or check what's blocking
kubectl get pods -n shoehorn -w
```

### Configuration Invalid

```bash
ERROR: Configuration validation failed
```

**Solution:**
```bash
# Validate config
shoehorn config validate --config /etc/shoehorn/prod-config.yml

# Test connections
shoehorn config test-db
shoehorn config test-redis
shoehorn config test-search
```

## Best Practices

1. **Always Use Dry Run First**
   ```bash
   shoehorn deploy production --dry-run
   ```

2. **Tag Releases**
   ```bash
   git tag v1.2.3
   git push --tags
   shoehorn deploy production --version v1.2.3
   ```

3. **Monitor During Deployment**
   ```bash
   shoehorn deploy production --follow-logs
   ```

4. **Keep Rollback Ready**
   ```bash
   shoehorn deploy production --auto-rollback
   ```

5. **Test in Staging First**
   ```bash
   shoehorn deploy staging --version v1.2.3
   # Verify it works
   shoehorn deploy production --version v1.2.3
   ```

## CI/CD Integration

### GitHub Actions

```yaml
name: Deploy to Production

on:
  push:
    tags:
      - 'v*'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Deploy to production
        run: |
          shoehorn deploy production \
            --version ${{ github.ref_name }} \
            --config ./config/prod.yml \
            --wait \
            --auto-rollback
```

### GitLab CI

```yaml
deploy:production:
  stage: deploy
  script:
    - shoehorn deploy production
        --version $CI_COMMIT_TAG
        --config ./config/prod.yml
        --wait
  only:
    - tags
  environment:
    name: production
    url: https://idp.company.com
```

## See Also

- **[init Command](init.md)** - Initialize configuration
- **[CLI Overview](overview.md)** - All CLI commands
- **[Configuration](../config/files.md)** - Configuration reference
