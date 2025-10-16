# CLI Commands Overview

The Shoehorn CLI provides powerful commands for managing your Internal Developer Portal.

## Installation

### From Releases

Download the latest release from GitHub:

```bash
# Linux/macOS
curl -L https://github.com/company/shoehorn/releases/latest/download/shoehorn-linux-amd64 -o shoehorn
chmod +x shoehorn
sudo mv shoehorn /usr/local/bin/

# Windows (PowerShell)
Invoke-WebRequest -Uri "https://github.com/company/shoehorn/releases/latest/download/shoehorn-windows-amd64.exe" -OutFile "shoehorn.exe"
```

### From Source

```bash
git clone https://github.com/company/shoehorn.git
cd shoehorn
go build -o shoehorn ./cmd/api
```

### Verify Installation

```bash
shoehorn version
# Output: shoehorn version 0.1.0
```

## Available Commands

The Shoehorn CLI provides these commands:

### Core Commands

| Command | Description |
|---------|-------------|
| `init` | Initialize a new Shoehorn configuration |
| `serve` | Start the API server |
| `crawl` | Run the catalog crawler |
| `migrate` | Run database migrations |

### Management Commands

| Command | Description |
|---------|-------------|
| `deploy` | Deploy Shoehorn to production |
| `config` | Manage configuration |
| `user` | Manage users |
| `entity` | Manage catalog entities |

### Utility Commands

| Command | Description |
|---------|-------------|
| `version` | Show version information |
| `health` | Check system health |
| `docs` | Generate API documentation |

## Command Structure

All commands follow this structure:

```bash
shoehorn [command] [subcommand] [flags]
```

**Examples:**
```bash
shoehorn init                      # Simple command
shoehorn config validate           # Command with subcommand
shoehorn serve --port 9090         # Command with flags
shoehorn entity list --type=service # Multiple flags
```

## Global Flags

These flags work with all commands:

| Flag | Short | Description | Example |
|------|-------|-------------|---------|
| `--config` | `-c` | Config file path | `--config /etc/shoehorn/config.yml` |
| `--verbose` | `-v` | Verbose output | `--verbose` |
| `--quiet` | `-q` | Suppress output | `--quiet` |
| `--help` | `-h` | Show help | `--help` |
| `--version` | | Show version | `--version` |

**Example:**
```bash
shoehorn serve --config /opt/config.yml --verbose
```

## Quick Start

### 1. Initialize Configuration

```bash
shoehorn init
# Creates: ./config/config.yml
```

### 2. Run Database Migrations

```bash
shoehorn migrate up
# Applies all pending migrations
```

### 3. Start the Server

```bash
shoehorn serve
# Server running on http://localhost:8080
```

### 4. Run a Crawl

```bash
shoehorn crawl run
# Crawling repositories...
```

## Common Workflows

### Development Workflow

```bash
# 1. Initialize project
shoehorn init --env development

# 2. Start services
docker-compose up -d postgres redis typesense

# 3. Run migrations
shoehorn migrate up

# 4. Start server in dev mode
shoehorn serve --port 8080 --reload

# 5. In another terminal, run crawler
shoehorn crawl run --watch
```

### Production Deployment

```bash
# 1. Validate configuration
shoehorn config validate --env production

# 2. Run health checks
shoehorn health check --all

# 3. Deploy
shoehorn deploy production \
  --config /etc/shoehorn/prod-config.yml \
  --replicas 3

# 4. Verify deployment
shoehorn health check --endpoint https://idp.company.com
```

### Maintenance Tasks

```bash
# Backup database
shoehorn db backup --output /backups/shoehorn-$(date +%Y%m%d).sql

# Clear cache
shoehorn cache clear --all

# Reindex search
shoehorn search reindex

# Run health checks
shoehorn health check --services postgres,redis,typesense
```

## Configuration

The CLI respects these configuration sources (in order):

1. Command-line flags
2. Environment variables
3. Config file (config.yml)
4. Defaults

**Example:**
```bash
# Config file: LOG_LEVEL=info
# Environment: export LOG_LEVEL=debug
# Flag: --log-level=warn
# Result: LOG_LEVEL=warn (flag wins)
```

## Environment Variables

Common environment variables:

```bash
# API Configuration
export SHOEHORN_API_URL=http://localhost:8080
export SHOEHORN_CONFIG=/path/to/config.yml

# Database
export DB_HOST=localhost
export DB_PORT=5432
export DB_NAME=shoehorn

# Logging
export LOG_LEVEL=debug
export LOG_FORMAT=json
```

## Output Formats

Most commands support multiple output formats:

```bash
# Default (human-readable)
shoehorn entity list

# JSON output
shoehorn entity list --output json

# YAML output
shoehorn entity list --output yaml

# Table format
shoehorn entity list --output table

# CSV format
shoehorn entity list --output csv
```

## Error Handling

The CLI uses standard exit codes:

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Invalid usage |
| 3 | Configuration error |
| 4 | Network error |
| 5 | Database error |

**Example:**
```bash
shoehorn entity list
echo $?  # Print exit code
# Output: 0 (success)

shoehorn entity list --invalid-flag
echo $?  # Print exit code
# Output: 2 (invalid usage)
```

## Autocomplete

Enable shell autocompletion:

### Bash

```bash
# Generate completion script
shoehorn completion bash > /etc/bash_completion.d/shoehorn

# Or add to .bashrc
source <(shoehorn completion bash)
```

### Zsh

```bash
# Generate completion script
shoehorn completion zsh > ~/.zsh/completions/_shoehorn

# Or add to .zshrc
source <(shoehorn completion zsh)
```

### Fish

```bash
shoehorn completion fish > ~/.config/fish/completions/shoehorn.fish
```

## Debugging

Enable debug output:

```bash
# Verbose mode
shoehorn serve --verbose

# Debug mode (very verbose)
shoehorn serve --debug

# Trace mode (extremely verbose)
shoehorn serve --trace

# Output to file
shoehorn serve --log-file /tmp/shoehorn.log
```

## Getting Help

### Command Help

```bash
# General help
shoehorn --help

# Command-specific help
shoehorn serve --help

# Subcommand help
shoehorn config validate --help
```

### Man Pages

```bash
# Generate man pages
shoehorn docs man --output /usr/local/man/man1/

# View man page
man shoehorn
man shoehorn-serve
```

### Online Documentation

- **Documentation**: https://docs.shoehorn.dev
- **API Reference**: https://api-docs.shoehorn.dev
- **GitHub**: https://github.com/company/shoehorn

## Command Reference

Explore individual commands:

- **[init](init.md)** - Initialize configuration
- **[deploy](deploy.md)** - Deploy to production

## Examples

### Example 1: Fresh Installation

```bash
# Install
curl -L https://get.shoehorn.dev | bash

# Initialize
shoehorn init

# Configure
vim ./config/config.yml

# Start
shoehorn serve
```

### Example 2: Production Deployment

```bash
# Pull latest version
docker pull company/shoehorn:latest

# Deploy with Helm
helm upgrade shoehorn ./charts/shoehorn \
  --namespace shoehorn \
  --values prod-values.yml

# Verify
shoehorn health check --endpoint https://idp.company.com
```

### Example 3: Development with Hot Reload

```bash
# Start with auto-reload
shoehorn serve --port 8080 --reload --watch ./config/

# In another terminal
shoehorn crawl run --watch --interval 5m

# Make changes to config - server auto-reloads
```

## Next Steps

- **[init Command](init.md)** - Learn about initialization
- **[deploy Command](deploy.md)** - Learn about deployment
- **[Configuration](../config/files.md)** - Configure Shoehorn
