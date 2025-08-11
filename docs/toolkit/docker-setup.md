# Setting Up the Toolkit Docker Environment

Master the fwdslsh Toolkit for a complete, containerized development environment with all tools pre-configured. This guide covers installation, configuration, customization, and advanced usage patterns for teams and individuals.

## Overview

The fwdslsh Toolkit provides:
- **All fwdslsh tools** (Unify, Inform, Giv) in one container
- **Pre-configured environment** with optimal settings
- **Development utilities** (git, curl, editors, build tools)
- **Team collaboration** features with shared configurations
- **CI/CD integration** for automated workflows
- **Custom extensions** and plugin support

## Quick Start

### Basic Installation

Get started with the Toolkit in under 2 minutes:

```bash
# Pull the latest toolkit
docker pull fwdslsh/toolkit:latest

# Run interactively
docker run -it --rm \
  -v $(pwd):/workspace \
  -p 3000:3000 \
  fwdslsh/toolkit

# You now have access to all fwdslsh tools:
# unify --version
# inform --version  
# giv --version
```

### Project Setup

Set up a new project with the Toolkit:

```bash
# Create project directory
mkdir my-fwdslsh-project
cd my-fwdslsh-project

# Initialize with Toolkit
docker run -it --rm \
  -v $(pwd):/workspace \
  fwdslsh/toolkit \
  bash -c "
    # Create new Unify site
    npm create unify-site . --template starter
    
    # Initialize Giv
    giv init
    
    # Create initial commit
    git init
    git add .
    giv message
    git commit -F .giv-message
    
    echo 'Project initialized with fwdslsh Toolkit!'
  "

# Start development server
docker run -it --rm \
  -v $(pwd):/workspace \
  -p 3000:3000 \
  fwdslsh/toolkit \
  bun run dev
```

## Toolkit Configuration

### Environment Variables

Configure the Toolkit with environment variables:

**`.env`**:
```bash
# fwdslsh Toolkit Configuration

# AI Provider Settings
OPENAI_API_KEY=your-openai-api-key
ANTHROPIC_API_KEY=your-anthropic-api-key
GIV_API_MODEL=gpt-4-turbo-preview

# Development Settings
UNIFY_PORT=3000
INFORM_DELAY=1000
INFORM_CONCURRENCY=3

# Git Configuration
GIT_USER_NAME="Your Name"
GIT_USER_EMAIL="your.email@example.com"

# Toolkit Preferences
TOOLKIT_TIMEZONE=America/New_York
TOOLKIT_LOCALE=en_US.UTF-8
TOOLKIT_EDITOR=nano

# Performance Settings
TOOLKIT_MEMORY_LIMIT=4g
TOOLKIT_CPU_LIMIT=2
```

### Docker Compose Setup

Create a complete development environment:

**`docker-compose.yml`**:
```yaml
version: '3.8'

services:
  fwdslsh-dev:
    image: fwdslsh/toolkit:latest
    container_name: fwdslsh-development
    
    # Port mappings
    ports:
      - "3000:3000"    # Unify dev server
      - "8080:8080"    # Additional services
      - "9000:9000"    # Custom applications
    
    # Volume mounts
    volumes:
      - .:/workspace:cached
      - fwdslsh-cache:/root/.cache
      - fwdslsh-config:/root/.config
      - fwdslsh-npm:/root/.npm
      - fwdslsh-git:/root/.gitconfig
    
    # Environment configuration
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - GIV_API_MODEL=${GIV_API_MODEL:-gpt-4-turbo-preview}
      - UNIFY_PORT=3000
      - INFORM_DELAY=1000
      - TOOLKIT_TIMEZONE=${TIMEZONE:-UTC}
    
    # Working directory
    working_dir: /workspace
    
    # Keep container running
    stdin_open: true
    tty: true
    
    # Resource limits
    deploy:
      resources:
        limits:
          memory: 4G
          cpus: '2.0'
        reservations:
          memory: 1G
          cpus: '0.5'
    
    # Health check
    healthcheck:
      test: ["CMD", "which", "unify", "inform", "giv"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    
    # Restart policy
    restart: unless-stopped

  # Optional: Database for larger projects
  postgres:
    image: postgres:15-alpine
    container_name: fwdslsh-postgres
    environment:
      POSTGRES_DB: fwdslsh_dev
      POSTGRES_USER: developer
      POSTGRES_PASSWORD: development
    volumes:
      - postgres-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    profiles:
      - database

  # Optional: Redis for caching
  redis:
    image: redis:7-alpine
    container_name: fwdslsh-redis
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    profiles:
      - cache

volumes:
  fwdslsh-cache:
  fwdslsh-config:
  fwdslsh-npm:
  fwdslsh-git:
  postgres-data:
  redis-data:

networks:
  default:
    name: fwdslsh-network
```

**Usage**:
```bash
# Start development environment
docker-compose up -d

# Access the development container
docker-compose exec fwdslsh-dev bash

# Start with database support
docker-compose --profile database up -d

# Start with full stack (including cache)
docker-compose --profile database --profile cache up -d

# Stop environment
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

## Advanced Configuration

### Custom Toolkit Image

Create a customized Toolkit for your team:

**`Dockerfile.custom`**:
```dockerfile
# Custom fwdslsh Toolkit
FROM fwdslsh/toolkit:latest

# Add team-specific tools
RUN apt-get update && apt-get install -y \
    postgresql-client \
    redis-tools \
    imagemagick \
    pandoc \
    && rm -rf /var/lib/apt/lists/*

# Install additional Node.js packages
RUN npm install -g \
    @playwright/test \
    eslint \
    prettier \
    typescript

# Install Python packages
RUN pip install \
    requests \
    beautifulsoup4 \
    pandas \
    jupyter

# Add custom scripts
COPY scripts/ /usr/local/bin/
RUN chmod +x /usr/local/bin/*

# Configure git hooks
COPY git-hooks/ /workspace/.git/hooks/
RUN chmod +x /workspace/.git/hooks/*

# Set up team configuration
COPY team-config/ /etc/fwdslsh/
COPY .giv.yaml /root/.giv.yaml
COPY .inform.yaml /root/.inform.yaml

# Add team aliases and functions
COPY bashrc-additions /tmp/bashrc-additions
RUN cat /tmp/bashrc-additions >> /root/.bashrc

# Set working directory
WORKDIR /workspace

# Default command
CMD ["bash"]
```

**Build custom image**:
```bash
# Build team image
docker build -f Dockerfile.custom -t myteam/fwdslsh-toolkit:latest .

# Push to registry
docker tag myteam/fwdslsh-toolkit:latest registry.company.com/fwdslsh-toolkit:latest
docker push registry.company.com/fwdslsh-toolkit:latest
```

### Team Scripts and Automation

Add custom automation scripts:

**`scripts/team-init`**:
```bash
#!/bin/bash
# Team project initialization script

set -e

echo "🚀 Initializing fwdslsh project for team..."

# Check if in workspace
if [ ! -d "/workspace" ]; then
    echo "❌ Error: Must be run inside Toolkit container"
    exit 1
fi

cd /workspace

# Project setup
echo "📁 Setting up project structure..."
mkdir -p {src,docs,tests,scripts,.github/workflows}

# Initialize tools
echo "🔧 Configuring fwdslsh tools..."

# Unify setup
if [ ! -f "package.json" ]; then
    npm create unify-site . --template ${TEMPLATE:-starter}
fi

# Giv setup  
if [ ! -f ".giv.yaml" ]; then
    giv init
    # Copy team configuration
    cp /etc/fwdslsh/.giv.yaml .giv.yaml
fi

# Inform setup
if [ ! -f ".inform.yaml" ]; then
    cp /etc/fwdslsh/.inform.yaml .inform.yaml
fi

# Git setup
if [ ! -d ".git" ]; then
    git init
    git config user.name "${GIT_USER_NAME:-Team Member}"
    git config user.email "${GIT_USER_EMAIL:-team@company.com}"
    
    # Add team gitignore
    cp /etc/fwdslsh/.gitignore .gitignore
    
    # Initial commit
    git add .
    giv message
    git commit -F .giv-message
fi

# Install dependencies
if [ -f "package.json" ]; then
    echo "📦 Installing dependencies..."
    bun install
fi

# Setup pre-commit hooks
echo "🔗 Setting up git hooks..."
cp -r /workspace/.git/hooks/* .git/hooks/ 2>/dev/null || true

echo "✅ Project initialized successfully!"
echo ""
echo "🎯 Next steps:"
echo "  1. Start development: bun run dev"
echo "  2. Extract content: inform https://example.com"
echo "  3. Generate commits: giv message"
echo "  4. Build for production: bun run build"
```

**`scripts/team-deploy`**:
```bash
#!/bin/bash
# Team deployment script

set -e

echo "🚀 Deploying fwdslsh project..."

# Build project
echo "🏗️ Building project..."
bun run build

# Generate release notes
echo "📝 Generating release notes..."
giv release-notes --output RELEASE_NOTES.md

# Update changelog
echo "📋 Updating changelog..."
giv changelog --output CHANGELOG.md

# Create deployment commit
echo "💾 Creating deployment commit..."
git add .
giv message --template deployment
git commit -F .giv-message

# Deploy based on environment
case "${DEPLOY_ENV:-staging}" in
    staging)
        echo "🧪 Deploying to staging..."
        # Add staging deployment logic
        ;;
    production)
        echo "🌍 Deploying to production..."
        # Add production deployment logic
        ;;
    *)
        echo "❓ Unknown environment: ${DEPLOY_ENV}"
        exit 1
        ;;
esac

echo "✅ Deployment complete!"
```

## Development Workflows

### Day-to-Day Development

Streamlined workflows for common tasks:

```bash
# Start development session
alias dev-start="docker-compose up -d && docker-compose exec fwdslsh-dev bash"

# Quick project commands
alias unify-dev="docker-compose exec fwdslsh-dev bun run dev"
alias unify-build="docker-compose exec fwdslsh-dev bun run build"
alias giv-commit="docker-compose exec fwdslsh-dev bash -c 'git add . && giv message && git commit -F .giv-message'"

# Content extraction
alias extract-docs="docker-compose exec fwdslsh-dev inform"

# Project maintenance
alias update-deps="docker-compose exec fwdslsh-dev bun update"
alias clean-cache="docker-compose exec fwdslsh-dev bash -c 'bun cache clean && giv cache clear'"
```

### Multi-Project Workflow

Manage multiple projects with the Toolkit:

**`project-manager.sh`**:
```bash
#!/bin/bash
# Multi-project management script

PROJECTS_DIR="$HOME/fwdslsh-projects"
TOOLKIT_IMAGE="fwdslsh/toolkit:latest"

function list_projects() {
    echo "📁 Available projects:"
    ls -1 "$PROJECTS_DIR" 2>/dev/null || echo "No projects found"
}

function create_project() {
    local name="$1"
    if [ -z "$name" ]; then
        echo "Usage: create_project <name>"
        return 1
    fi
    
    local project_dir="$PROJECTS_DIR/$name"
    mkdir -p "$project_dir"
    
    echo "🆕 Creating project: $name"
    docker run -it --rm \
        -v "$project_dir:/workspace" \
        "$TOOLKIT_IMAGE" \
        team-init
}

function switch_project() {
    local name="$1"
    if [ -z "$name" ]; then
        list_projects
        echo -n "Enter project name: "
        read name
    fi
    
    local project_dir="$PROJECTS_DIR/$name"
    if [ ! -d "$project_dir" ]; then
        echo "❌ Project '$name' not found"
        return 1
    fi
    
    echo "🔄 Switching to project: $name"
    cd "$project_dir"
    
    # Start development environment
    docker run -it --rm \
        -v "$project_dir:/workspace" \
        -p 3000:3000 \
        "$TOOLKIT_IMAGE"
}

function backup_project() {
    local name="$1"
    if [ -z "$name" ]; then
        echo "Usage: backup_project <name>"
        return 1
    fi
    
    local project_dir="$PROJECTS_DIR/$name"
    local backup_file="$HOME/backups/fwdslsh-$name-$(date +%Y%m%d).tar.gz"
    
    echo "💾 Backing up project: $name"
    mkdir -p "$(dirname "$backup_file")"
    tar -czf "$backup_file" -C "$project_dir" .
    echo "✅ Backup saved: $backup_file"
}

# Command handling
case "$1" in
    list)
        list_projects
        ;;
    create)
        create_project "$2"
        ;;
    switch)
        switch_project "$2"
        ;;
    backup)
        backup_project "$2"
        ;;
    *)
        echo "Usage: $0 {list|create|switch|backup} [project_name]"
        exit 1
        ;;
esac
```

## CI/CD Integration

### GitHub Actions

Integrate Toolkit with GitHub Actions:

**`.github/workflows/fwdslsh-ci.yml`**:
```yaml
name: fwdslsh CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test-and-build:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for Giv
      
      - name: Set up fwdslsh Toolkit
        run: |
          docker pull fwdslsh/toolkit:latest
      
      - name: Install dependencies and test
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:/workspace \
            -e OPENAI_API_KEY="${{ secrets.OPENAI_API_KEY }}" \
            fwdslsh/toolkit \
            bash -c "
              cd /workspace
              
              # Install dependencies
              if [ -f package.json ]; then
                bun install
              fi
              
              # Run tests
              if [ -f package.json ]; then
                bun test 2>/dev/null || echo 'No tests configured'
              fi
              
              # Validate configurations
              giv config validate
              inform config validate 2>/dev/null || echo 'No Inform config'
              
              # Build project
              if [ -f package.json ]; then
                bun run build
              fi
            "
      
      - name: Generate documentation
        if: github.event_name == 'push' && github.ref == 'refs/heads/main'
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:/workspace \
            -e OPENAI_API_KEY="${{ secrets.OPENAI_API_KEY }}" \
            fwdslsh/toolkit \
            bash -c "
              cd /workspace
              
              # Generate changelog
              giv changelog > CHANGELOG.md
              
              # Generate release notes if version changed
              if git diff HEAD~1 --name-only | grep -q package.json; then
                giv release-notes > RELEASE_NOTES.md
              fi
              
              # Commit documentation updates
              if [ -n \"\$(git status --porcelain)\" ]; then
                git config user.name 'fwdslsh-bot'
                git config user.email 'bot@fwdslsh.dev'
                git add CHANGELOG.md RELEASE_NOTES.md
                giv message --template automation
                git commit -F .giv-message
                git push
              fi
            "
      
      - name: Deploy to staging
        if: github.ref == 'refs/heads/develop'
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:/workspace \
            -e DEPLOY_ENV=staging \
            fwdslsh/toolkit \
            team-deploy
      
      - name: Deploy to production
        if: github.ref == 'refs/heads/main'
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:/workspace \
            -e DEPLOY_ENV=production \
            fwdslsh/toolkit \
            team-deploy
```

### GitLab CI

Configure for GitLab CI/CD:

**`.gitlab-ci.yml`**:
```yaml
# fwdslsh Toolkit CI/CD Pipeline

image: fwdslsh/toolkit:latest

variables:
  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: ""

stages:
  - validate
  - build
  - test
  - deploy

cache:
  paths:
    - node_modules/
    - .cache/

before_script:
  - cd /workspace || cd $CI_PROJECT_DIR

validate:
  stage: validate
  script:
    - giv config validate
    - inform config validate || echo "No Inform config"
    - if [ -f package.json ]; then bun install; fi
  only:
    - merge_requests
    - main
    - develop

build:
  stage: build
  script:
    - if [ -f package.json ]; then bun run build; fi
  artifacts:
    paths:
      - dist/
    expire_in: 1 hour
  only:
    - main
    - develop

test:
  stage: test
  script:
    - if [ -f package.json ]; then bun test || echo "No tests configured"; fi
  coverage: '/Coverage: \d+\.\d+%/'
  only:
    - merge_requests
    - main
    - develop

deploy:staging:
  stage: deploy
  script:
    - team-deploy
  environment:
    name: staging
    url: https://staging.yourproject.com
  only:
    - develop

deploy:production:
  stage: deploy
  script:
    - team-deploy
  environment:
    name: production
    url: https://yourproject.com
  only:
    - main
  when: manual
```

## Toolkit Extensions

### Custom Tool Integration

Add custom tools to the Toolkit:

**`extensions/install-custom-tools.sh`**:
```bash
#!/bin/bash
# Install custom tools in Toolkit

set -e

echo "🔧 Installing custom tools..."

# Install hugo for migration projects
echo "📄 Installing Hugo..."
wget https://github.com/gohugoio/hugo/releases/download/v0.120.4/hugo_extended_0.120.4_linux-amd64.tar.gz
tar -xzf hugo_extended_0.120.4_linux-amd64.tar.gz
mv hugo /usr/local/bin/
rm hugo_extended_0.120.4_linux-amd64.tar.gz

# Install playwright for testing
echo "🎭 Installing Playwright..."
npm install -g @playwright/test
npx playwright install chromium

# Install additional Python tools
echo "🐍 Installing Python tools..."
pip install \
    mkdocs \
    mkdocs-material \
    sphinx \
    jupyter

# Install Go tools
echo "🐹 Installing Go tools..."
wget https://go.dev/dl/go1.21.4.linux-amd64.tar.gz
tar -C /usr/local -xzf go1.21.4.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
echo 'export PATH=$PATH:/usr/local/go/bin' >> /root/.bashrc

# Install database tools
echo "🗄️ Installing database tools..."
apt-get update && apt-get install -y \
    postgresql-client \
    mysql-client \
    sqlite3

echo "✅ Custom tools installed successfully!"
```

### Plugin System

Create a plugin system for the Toolkit:

**`plugins/manager.sh`**:
```bash
#!/bin/bash
# Toolkit Plugin Manager

PLUGINS_DIR="/etc/fwdslsh/plugins"
ENABLED_PLUGINS_DIR="/etc/fwdslsh/plugins/enabled"

function list_plugins() {
    echo "📦 Available plugins:"
    for plugin in "$PLUGINS_DIR"/*.plugin; do
        if [ -f "$plugin" ]; then
            name=$(basename "$plugin" .plugin)
            if [ -L "$ENABLED_PLUGINS_DIR/$name.plugin" ]; then
                echo "  ✅ $name (enabled)"
            else
                echo "  ⏸️ $name (disabled)"
            fi
        fi
    done
}

function enable_plugin() {
    local name="$1"
    local plugin_file="$PLUGINS_DIR/$name.plugin"
    
    if [ ! -f "$plugin_file" ]; then
        echo "❌ Plugin '$name' not found"
        return 1
    fi
    
    mkdir -p "$ENABLED_PLUGINS_DIR"
    ln -sf "$plugin_file" "$ENABLED_PLUGINS_DIR/$name.plugin"
    
    # Source plugin to activate
    source "$plugin_file"
    
    echo "✅ Plugin '$name' enabled"
}

function disable_plugin() {
    local name="$1"
    rm -f "$ENABLED_PLUGINS_DIR/$name.plugin"
    echo "⏸️ Plugin '$name' disabled"
}

function load_plugins() {
    if [ -d "$ENABLED_PLUGINS_DIR" ]; then
        for plugin in "$ENABLED_PLUGINS_DIR"/*.plugin; do
            if [ -f "$plugin" ]; then
                source "$plugin"
            fi
        done
    fi
}

# Load plugins on shell start
load_plugins

# Command handling
case "$1" in
    list)
        list_plugins
        ;;
    enable)
        enable_plugin "$2"
        ;;
    disable)
        disable_plugin "$2"
        ;;
    load)
        load_plugins
        ;;
    *)
        echo "Usage: $0 {list|enable|disable|load} [plugin_name]"
        ;;
esac
```

**Example plugin** (`plugins/github-integration.plugin`):
```bash
#!/bin/bash
# GitHub Integration Plugin

# Add GitHub CLI aliases
alias gh-clone="gh repo clone"
alias gh-pr="gh pr create"
alias gh-issues="gh issue list"

# Function to create PR with Giv
function gh-pr-giv() {
    local title="${1:-$(git log -1 --pretty=%s)}"
    local body="$(giv pr-description --analyze-diff)"
    
    gh pr create --title "$title" --body "$body"
}

# Function to sync with upstream
function gh-sync-upstream() {
    gh repo sync --source upstream
}

echo "🐙 GitHub integration plugin loaded"
```

## Monitoring and Maintenance

### Container Health Monitoring

Monitor Toolkit container health:

**`scripts/health-check.sh`**:
```bash
#!/bin/bash
# Toolkit health check script

set -e

# Check if tools are available
echo "🔍 Checking fwdslsh tools..."

tools=("unify" "inform" "giv" "bun" "git")
for tool in "${tools[@]}"; do
    if command -v "$tool" >/dev/null 2>&1; then
        version=$(${tool} --version 2>/dev/null | head -1 || echo "unknown")
        echo "  ✅ $tool: $version"
    else
        echo "  ❌ $tool: not found"
        exit 1
    fi
done

# Check API connectivity
echo ""
echo "🌐 Checking API connectivity..."

if [ -n "$OPENAI_API_KEY" ]; then
    if giv test-api openai >/dev/null 2>&1; then
        echo "  ✅ OpenAI API: connected"
    else
        echo "  ⚠️ OpenAI API: connection failed"
    fi
fi

if [ -n "$ANTHROPIC_API_KEY" ]; then
    if giv test-api anthropic >/dev/null 2>&1; then
        echo "  ✅ Anthropic API: connected"
    else
        echo "  ⚠️ Anthropic API: connection failed"
    fi
fi

# Check disk space
echo ""
echo "💾 Checking disk space..."
disk_usage=$(df /workspace | tail -1 | awk '{print $5}' | sed 's/%//')
if [ "$disk_usage" -gt 90 ]; then
    echo "  ⚠️ Disk usage: ${disk_usage}% (high)"
else
    echo "  ✅ Disk usage: ${disk_usage}%"
fi

# Check memory usage
echo ""
echo "🧠 Checking memory usage..."
memory_usage=$(free | grep Mem | awk '{printf "%.1f", $3/$2 * 100.0}')
echo "  📊 Memory usage: ${memory_usage}%"

echo ""
echo "✅ Health check complete!"
```

### Performance Monitoring

Monitor Toolkit performance:

**`scripts/performance-monitor.sh`**:
```bash
#!/bin/bash
# Performance monitoring for Toolkit

LOG_FILE="/var/log/toolkit-performance.log"

function log_metrics() {
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    # CPU usage
    local cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
    
    # Memory usage
    local memory_usage=$(free | grep Mem | awk '{printf "%.1f", $3/$2 * 100.0}')
    
    # Disk usage
    local disk_usage=$(df /workspace | tail -1 | awk '{print $5}' | sed 's/%//')
    
    # Load average
    local load_avg=$(uptime | awk -F'load average:' '{print $2}' | awk '{print $1}' | sed 's/,//')
    
    echo "$timestamp,CPU:$cpu_usage,Memory:$memory_usage,Disk:$disk_usage,Load:$load_avg" >> "$LOG_FILE"
}

# Log metrics every 30 seconds
while true; do
    log_metrics
    sleep 30
done
```

## Troubleshooting

### Common Issues

Resolve frequent Toolkit problems:

```bash
# Permission issues
sudo chown -R $(id -u):$(id -g) ./project-directory

# Port conflicts
docker run -p 3001:3000 fwdslsh/toolkit  # Use different port

# Memory issues
docker run --memory=4g fwdslsh/toolkit    # Increase memory limit

# Cache issues  
docker system prune -a                    # Clean Docker cache
docker volume prune                       # Clean volumes

# Network issues
docker run --network=host fwdslsh/toolkit # Use host network
```

### Debugging Tools

Debug Toolkit issues:

```bash
# Check container logs
docker logs fwdslsh-development

# Inspect container
docker exec -it fwdslsh-development bash

# Check resource usage
docker stats fwdslsh-development

# Validate environment
docker exec fwdslsh-development env | grep -E "(OPENAI|ANTHROPIC|GIV|UNIFY|INFORM)"

# Test tool functionality
docker exec fwdslsh-development bash -c "cd /workspace && giv test"
```

## Next Steps

### Advanced Toolkit Usage
- [Custom Toolkit Images for Teams](../toolkit/custom-images.md)
- [Toolkit Plugin Development](../toolkit/plugin-development.md)
- [Enterprise Toolkit Deployment](../toolkit/enterprise-deployment.md)

### Integration Examples
- [VS Code Dev Container Integration](../integrations/vscode-devcontainer.md)
- [JetBrains IDE Integration](../integrations/jetbrains-toolkit.md)
- [CI/CD Pipeline Templates](../integrations/cicd-templates.md)

### Team Management
- [Multi-Team Toolkit Configuration](../toolkit/multi-team.md)
- [Toolkit Security and Compliance](../toolkit/security.md)
- [Performance Optimization at Scale](../toolkit/performance-optimization.md)

**Your development environment is now powered by the complete fwdslsh Toolkit!** 🚀

---

*This guide references the [fwdslsh Toolkit documentation](https://github.com/fwdslsh/toolkit) and [Docker best practices](https://docs.docker.com/develop/dev-best-practices/). For the latest Toolkit features and configurations, visit [fwdslsh.dev](https://fwdslsh.dev).*