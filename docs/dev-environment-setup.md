# Setting Up the Complete fwdslsh Development Environment

This guide will help you set up a complete development environment for working with all fwdslsh tools. Whether you're an individual developer or setting up for a team, we'll cover everything you need to get productive quickly.

## 🎯 Choose Your Setup Method

### Option 1: Docker Toolkit (Recommended for Teams)
**Best for**: Team consistency, enterprise environments, trying all tools

### Option 2: Native Installation (Recommended for Power Users)
**Best for**: Individual developers, custom configurations, performance optimization

### Option 3: Hybrid Approach (Recommended for Flexibility)
**Best for**: Teams that want native performance with container consistency

---

## 🐳 Option 1: Docker Toolkit Setup

The Docker Toolkit provides a complete, consistent environment with all tools pre-installed and configured.

### Prerequisites
- Docker Desktop (Windows/Mac) or Docker Engine (Linux)
- 4GB+ RAM available for containers
- 10GB+ disk space

### Quick Start

```bash
# Pull the latest toolkit
docker pull fwdslsh/toolkit:latest

# Run interactively
docker run -it --rm -v $(pwd):/workspace fwdslsh/toolkit

# You now have access to:
# - unify (static site generator)
# - inform (web crawler)
# - giv (AI git assistant)
# - All supporting tools (bun, git, curl, etc.)
```

### Persistent Setup with Volume Mounting

```bash
# Create a workspace directory
mkdir ~/fwdslsh-workspace
cd ~/fwdslsh-workspace

# Run with persistent workspace
docker run -it --rm \
  -v $(pwd):/workspace \
  -v fwdslsh-cache:/root/.cache \
  -p 3000:3000 \
  --name fwdslsh-dev \
  fwdslsh/toolkit
```

### Team Configuration with docker-compose

Create `docker-compose.yml` in your project:

```yaml
version: '3.8'

services:
  fwdslsh-dev:
    image: fwdslsh/toolkit:latest
    volumes:
      - .:/workspace
      - fwdslsh-cache:/root/.cache
      - fwdslsh-config:/root/.config
    ports:
      - "3000:3000"      # Unify dev server
      - "8080:8080"      # Additional services
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - GIV_CONFIG_PATH=/workspace/.giv.yaml
    working_dir: /workspace
    stdin_open: true
    tty: true

volumes:
  fwdslsh-cache:
  fwdslsh-config:
```

```bash
# Start the development environment
docker-compose up -d

# Access the container
docker-compose exec fwdslsh-dev bash

# Stop when done
docker-compose down
```

### Toolkit Configuration

Create `.env` file for team settings:
```bash
# AI Provider Configuration
OPENAI_API_KEY=your-openai-key
ANTHROPIC_API_KEY=your-anthropic-key
GIV_API_MODEL=gpt-4

# Development Settings
UNIFY_PORT=3000
INFORM_DELAY=1000
INFORM_CONCURRENCY=3

# Team Preferences
GIV_COMMIT_TEMPLATE=conventional
UNIFY_LIVE_RELOAD=true
```

---

## 💻 Option 2: Native Installation

For maximum performance and customization, install tools directly on your system.

### Prerequisites Installation

#### 1. Install Bun (Required for Unify and Inform)
```bash
# Install Bun
curl -fsSL https://bun.sh/install | bash

# Add to PATH (add to ~/.bashrc or ~/.zshrc)
export PATH="$HOME/.bun/bin:$PATH"

# Verify installation
bun --version
```

#### 2. Install Python (Required for Giv)
```bash
# macOS with Homebrew
brew install python@3.11

# Ubuntu/Debian
sudo apt update && sudo apt install python3.11 python3-pip

# Windows (use official installer from python.org)
# Download and install Python 3.11+

# Verify installation
python3 --version
pip3 --version
```

#### 3. Install Git (Required for all tools)
```bash
# macOS
brew install git

# Ubuntu/Debian  
sudo apt install git

# Windows
# Download from git-scm.com or use Git for Windows

# Configure Git (if not already done)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Core Tools Installation

#### Install Unify
```bash
# Global installation
bun add -g @fwdslsh/unify

# Verify installation
unify --version

# Test with a quick site
npm create unify-site test-site
cd test-site && bun run dev
# Visit http://localhost:3000
```

#### Install Inform  
```bash
# Global installation
bun add -g @fwdslsh/inform

# Verify installation
inform --version

# Test with a quick extraction
inform https://example.com --max-pages 1
```

#### Install Giv
```bash
# Install script (recommended)
curl -fsSL https://raw.githubusercontent.com/fwdslsh/giv/main/install.sh | sh

# Or with pip
pip3 install giv

# Verify installation
giv --version

# Set up AI provider
export OPENAI_API_KEY="your-api-key"
# Test (in a git repository)
giv summary
```

### Supporting Tools Installation

#### Install VS Code Extensions
```bash
# Install VS Code if not already installed
# Then install the Unify extension:

# Open VS Code
code .

# Install extension via marketplace:
# 1. Open Extensions (Ctrl+Shift+X)
# 2. Search "Unify fwdslsh"
# 3. Install the official extension
```

#### Install Additional Utilities
```bash
# GitHub CLI (for Giv integration)
# macOS
brew install gh

# Ubuntu/Debian
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update && sudo apt install gh

# Windows
# Download from https://cli.github.com/

# Authenticate with GitHub
gh auth login
```

---

## 🔄 Option 3: Hybrid Approach

Use Docker for consistency but native tools for performance-critical workflows.

### Setup Strategy

```bash
# Use Docker for exploration and team sync
alias fwdslsh-toolkit='docker run -it --rm -v $(pwd):/workspace fwdslsh/toolkit'

# Install native tools for daily development
bun add -g @fwdslsh/unify @fwdslsh/inform
pip3 install giv

# Use toolkit for:
# - New team member onboarding
# - Complex multi-tool workflows  
# - CI/CD environments
# - Testing new versions

# Use native for:
# - Daily development
# - Performance-critical tasks
# - Custom configurations
# - Editor integrations
```

---

## ⚙️ Configuration & Customization

### Global Configuration Files

Create these configuration files in your home directory for consistent behavior across projects:

#### ~/.giv.yaml (Giv Configuration)
```yaml
# Global Giv configuration
api:
  provider: "openai"  # or "anthropic", "local"
  model: "gpt-4"
  temperature: 0.7
  max_tokens: 1000

commit:
  template: |
    {{ type }}({{ scope }}): {{ description }}
    
    {{ body }}
    
    {{ footer }}
  
  types:
    - feat: "New features"
    - fix: "Bug fixes"
    - docs: "Documentation"
    - style: "Code style"
    - refactor: "Refactoring"
    - test: "Tests"
    - chore: "Maintenance"

changelog:
  format: "keepachangelog"
  include_authors: true
  group_by_type: true

release_notes:
  highlight_breaking: true
  include_stats: true
  include_contributors: true
```

#### ~/.inform.yaml (Inform Configuration)
```yaml
# Global Inform configuration
defaults:
  delay: 1000
  concurrency: 3
  max_pages: 100
  timeout: 30
  user_agent: "fwdslsh-inform/1.0 (+https://github.com/fwdslsh/inform)"

output:
  format: "markdown"
  include_frontmatter: true
  preserve_images: true
  
extraction:
  content_selectors:
    - "main"
    - ".content"
    - "article"
    - ".post-content"
    - ".entry-content"
  
  exclude_selectors:
    - "nav"
    - ".navigation"
    - "footer"
    - ".sidebar"
    - ".advertisement"
    - ".social-media"

llms:
  generate_summary: false  # Enable per-project
  max_chunk_size: 4000
  overlap_size: 200
```

#### ~/.unify/config.json (Unify Configuration)
```json
{
  "defaults": {
    "port": 3000,
    "liveReload": true,
    "prettyUrls": true,
    "minify": false
  },
  "build": {
    "outputDir": "dist",
    "publicPath": "/",
    "generateSitemap": true,
    "generateRobots": true
  },
  "dev": {
    "watchIgnore": [
      "node_modules",
      ".git",
      "dist",
      "*.log"
    ]
  }
}
```

### Environment Variables Setup

Add these to your shell profile (`.bashrc`, `.zshrc`, etc.):

```bash
# AI Provider Keys
export OPENAI_API_KEY="your-openai-api-key"
export ANTHROPIC_API_KEY="your-anthropic-api-key"

# fwdslsh Tool Defaults
export GIV_CONFIG_PATH="$HOME/.giv.yaml"
export GIV_API_MODEL="gpt-4"
export INFORM_CONFIG_PATH="$HOME/.inform.yaml"
export UNIFY_CONFIG_PATH="$HOME/.unify/config.json"

# Development Environment
export UNIFY_DEFAULT_PORT=3000
export INFORM_DEFAULT_DELAY=1000
export INFORM_DEFAULT_CONCURRENCY=3

# Optional: Custom paths
export FWDSLSH_TEMPLATES_PATH="$HOME/.fwdslsh/templates"
export FWDSLSH_CACHE_PATH="$HOME/.cache/fwdslsh"
```

### VS Code Workspace Configuration

Create `.vscode/settings.json` in your project:

```json
{
  "unify.enable": true,
  "unify.livePreview": true,
  "unify.autoFormat": true,
  
  "files.associations": {
    "*.unify": "html",
    "*.giv": "yaml"
  },
  
  "emmet.includeLanguages": {
    "unify": "html"
  },
  
  "search.exclude": {
    "**/node_modules": true,
    "**/dist": true,
    "**/.cache": true,
    "**/llms*.txt": true
  },
  
  "files.watcherExclude": {
    "**/node_modules/**": true,
    "**/dist/**": true,
    "**/.cache/**": true
  }
}
```

---

## 🔄 Development Workflows

### Daily Development Workflow

```bash
# Start a new project
npm create unify-site my-project
cd my-project

# Set up version control
git init
git add .
giv message  # "feat: Initialize new Unify project"
git commit -F .giv-message

# Start development
bun run dev  # http://localhost:3000

# Extract content (if needed)
inform https://source-site.com --output-dir content

# Make changes, then commit
git add .
giv message  # AI-generated commit message
git commit -F .giv-message

# Generate release notes
giv changelog > CHANGELOG.md
giv release-notes > RELEASE_NOTES.md

# Build for production
bun run build
```

### Team Workflow Setup

#### 1. Project Configuration

Create project-level configs:

**`.giv.yaml`** (Project-specific Giv settings):
```yaml
commit:
  scope_prompts: true
  require_scope: ["feat", "fix"]
  
team:
  reviewers: ["@team-lead", "@senior-dev"]
  auto_assign: true
  
templates:
  commit: ".giv-templates/commit.txt"
  changelog: ".giv-templates/changelog.md"
```

**`unify.config.js`** (Project Unify settings):
```javascript
export default {
  build: {
    baseUrl: process.env.BASE_URL || '/',
    outputDir: 'dist',
    prettyUrls: true
  },
  
  dev: {
    port: process.env.PORT || 3000,
    liveReload: true
  },
  
  components: {
    globalComponents: [
      'src/.components/header.html',
      'src/.components/footer.html'
    ]
  }
}
```

#### 2. Git Hooks Setup

Create `.githooks/pre-commit`:
```bash
#!/bin/bash
# Ensure consistent formatting and documentation

# Run Unify build check
if [ -f "unify.config.js" ]; then
  echo "Checking Unify build..."
  bun run build --dry-run || exit 1
fi

# Generate updated documentation if needed
if command -v giv &> /dev/null; then
  echo "Updating project documentation..."
  giv summary --output README.md --update-only
fi

echo "Pre-commit checks passed!"
```

```bash
# Make executable and install
chmod +x .githooks/pre-commit
git config core.hooksPath .githooks
```

#### 3. CI/CD Integration

Create `.github/workflows/fwdslsh.yml`:
```yaml
name: fwdslsh Workflow

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for Giv
      
      - name: Setup fwdslsh Toolkit
        run: |
          docker pull fwdslsh/toolkit:latest
          
      - name: Extract and Build
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:/workspace \
            -e OPENAI_API_KEY=${{ secrets.OPENAI_API_KEY }} \
            fwdslsh/toolkit \
            bash -c "
              cd /workspace
              if [ -f 'extract.sh' ]; then ./extract.sh; fi
              if [ -f 'unify.config.js' ]; then bun run build; fi
            "
      
      - name: Generate Documentation
        if: github.event_name == 'push' && github.ref == 'refs/heads/main'
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:/workspace \
            -e OPENAI_API_KEY=${{ secrets.OPENAI_API_KEY }} \
            fwdslsh/toolkit \
            bash -c "
              cd /workspace
              giv changelog > CHANGELOG.md
              giv release-notes > RELEASE_NOTES.md
              git config user.name 'fwdslsh-bot'
              git config user.email 'bot@fwdslsh.dev'
              git add CHANGELOG.md RELEASE_NOTES.md
              if ! git diff --cached --quiet; then
                giv message
                git commit -F .giv-message
                git push
              fi
            "
      
      - name: Deploy to GitHub Pages
        if: github.event_name == 'push' && github.ref == 'refs/heads/main'
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

---

## 🛠️ Advanced Configuration

### Custom Tool Integration

#### Integrate with existing tools:

**With Hugo/Jekyll:**
```bash
# Extract content
inform https://source.com --output-dir hugo/content

# Convert Unify components to Hugo shortcodes
# (custom script based on your needs)
```

**With Gatsby/Next.js:**
```bash
# Use Inform for content extraction
inform https://cms.site.com --output-dir src/content

# Use Giv for better commit messages
giv message --scope="content"
```

### Performance Optimization

#### For large projects:
```yaml
# .inform.yaml
performance:
  delay: 500  # Faster crawling
  concurrency: 5  # More parallel requests
  cache_responses: true
  max_memory_mb: 2048

# .giv.yaml  
performance:
  max_files_analyzed: 1000
  chunk_size: 50
  cache_embeddings: true
```

#### System resource monitoring:
```bash
# Monitor Docker resource usage
docker stats fwdslsh-dev

# Monitor native tool performance
top -p $(pgrep -f "unify|inform|giv")
```

---

## 🔍 Troubleshooting

### Common Issues

#### Docker Issues
```bash
# Container won't start
docker logs fwdslsh-dev

# Permission issues (Linux)
sudo chown -R $USER:$USER ~/fwdslsh-workspace

# Port conflicts
docker run -p 3001:3000 fwdslsh/toolkit  # Use different port
```

#### Native Installation Issues
```bash
# Bun installation problems
curl -fsSL https://bun.sh/install | bash -s -- --force

# Python/pip issues
python3 -m pip install --user --upgrade pip
pip3 install --user giv

# Node.js conflicts with Bun
# Use nvm to manage Node versions
# Bun is independent and doesn't conflict
```

#### Tool-Specific Issues
```bash
# Unify build failures
bun run build --verbose  # Detailed output
bun run clean && bun run build  # Clean build

# Inform connection issues
inform https://site.com --delay 3000 --timeout 60

# Giv API issues  
giv --debug message  # Debug output
export GIV_API_BASE="https://api.openai.com/v1"  # Explicit API base
```

### Performance Issues

#### Slow Docker performance:
```bash
# Allocate more resources to Docker
# Docker Desktop: Settings > Resources > Advanced
# Recommended: 4GB+ RAM, 2+ CPUs

# Use native tools for performance-critical tasks
alias fast-unify='bun run'  # Use native Bun instead of Docker
```

#### Memory usage optimization:
```bash
# Limit Inform memory usage
inform --max-memory 1GB https://site.com

# Use smaller AI models for Giv
export GIV_API_MODEL="gpt-3.5-turbo"
```

---

## 📚 Next Steps

### Learning Resources
- [Unify Quick Start](unify/quick-start.md) - Build your first site
- [Inform User Guide](inform/user-guide.md) - Master content extraction  
- [Giv Advanced Usage](giv/advanced-usage.md) - AI-powered workflows
- [Integration Examples](../examples/) - Real-world configurations

### Advanced Topics
- [Performance Optimization](../guides/performance-optimization.md)
- [Enterprise Deployment](../guides/enterprise-deployment.md)
- [Custom Integrations](../guides/custom-integrations.md)
- [Security Best Practices](../guides/security-practices.md)

### Community Resources
- [GitHub Discussions](https://github.com/orgs/fwdslsh/discussions) - Community support
- [Example Projects](https://github.com/fwdslsh/examples) - Real implementations
- [Contributing Guide](../contributing/contributing.md) - Help improve the tools

**You're now ready to be productive with the complete fwdslsh ecosystem!** 🚀

---

*This guide is continuously updated. For the latest version and additional resources, visit the [fwdslsh examples repository](https://github.com/fwdslsh/examples).*