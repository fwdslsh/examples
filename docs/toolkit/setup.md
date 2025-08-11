# Setting Up the Toolkit Docker Environment

The fwdslsh Toolkit provides a complete, containerized development environment with all fwdslsh tools pre-installed and configured. This guide covers everything from basic setup to advanced team configurations.

## What is the Toolkit?

The Toolkit is a Docker container that includes:
- **All fwdslsh tools**: Unify, Inform, Giv, and Lift
- **Development utilities**: Git, GitHub CLI, curl, wget, and more
- **Text processing tools**: Glow for beautiful Markdown rendering
- **Language runtimes**: Bun, Node.js, Python pre-configured
- **Consistent environment**: Same setup across all team members

Perfect for teams, CI/CD pipelines, and getting started quickly!

## Quick Start (5 minutes)

### Prerequisites
- Docker Desktop (Windows/Mac) or Docker Engine (Linux)
- 4GB+ available RAM
- 10GB+ disk space

### Basic Setup

```bash
# Pull the latest toolkit
docker pull fwdslsh/toolkit:latest

# Run interactively
docker run -it --rm fwdslsh/toolkit

# You now have access to all fwdslsh tools!
unify --version
inform --version
giv --version
```

### With Your Project

```bash
# Run with your project mounted
docker run -it --rm \
  -v $(pwd):/workspace \
  -p 3000:3000 \
  fwdslsh/toolkit

# Inside the container
cd /workspace
# All your files are available here
```

## Detailed Setup Options

### Option 1: Interactive Development

Perfect for development and exploration:

```bash
# Create a dedicated workspace
mkdir ~/fwdslsh-projects
cd ~/fwdslsh-projects

# Run with persistent workspace and port forwarding
docker run -it --rm \
  --name fwdslsh-dev \
  -v $(pwd):/workspace \
  -v fwdslsh-cache:/root/.cache \
  -p 3000:3000 \
  -p 8080:8080 \
  fwdslsh/toolkit
```

**What this does:**
- Mounts your current directory as `/workspace`
- Creates a persistent cache volume for faster rebuilds
- Forwards ports 3000 (Unify dev server) and 8080 (additional services)
- Names the container for easy reference

### Option 2: Docker Compose Setup

Best for team consistency and complex setups:

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  fwdslsh:
    image: fwdslsh/toolkit:latest
    container_name: fwdslsh-dev
    volumes:
      - .:/workspace
      - fwdslsh-cache:/root/.cache
      - fwdslsh-config:/root/.config
      - fwdslsh-npm:/root/.npm
    ports:
      - "3000:3000"  # Unify dev server
      - "8080:8080"  # Additional services
      - "9000:9000"  # Optional: custom services
    environment:
      # AI Configuration
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - GIV_API_MODEL=${GIV_API_MODEL:-gpt-4}
      
      # Tool Defaults
      - UNIFY_PORT=3000
      - INFORM_DELAY=1000
      - INFORM_CONCURRENCY=3
      
      # Git Configuration
      - GIT_AUTHOR_NAME=${GIT_AUTHOR_NAME}
      - GIT_AUTHOR_EMAIL=${GIT_AUTHOR_EMAIL}
      - GIT_COMMITTER_NAME=${GIT_AUTHOR_NAME}
      - GIT_COMMITTER_EMAIL=${GIT_AUTHOR_EMAIL}
    working_dir: /workspace
    stdin_open: true
    tty: true
    command: bash

volumes:
  fwdslsh-cache:
  fwdslsh-config:
  fwdslsh-npm:
```

Create `.env` file:
```bash
# AI Provider Configuration
OPENAI_API_KEY=your-openai-key
ANTHROPIC_API_KEY=your-anthropic-key
GIV_API_MODEL=gpt-4

# Git Configuration
GIT_AUTHOR_NAME=Your Name
GIT_AUTHOR_EMAIL=your.email@example.com
```

Run with:
```bash
# Start the environment
docker-compose up -d

# Access the container
docker-compose exec fwdslsh bash

# Stop when done
docker-compose down
```

### Option 3: VS Code Dev Container

For VS Code users, create `.devcontainer/devcontainer.json`:

```json
{
  "name": "fwdslsh Development",
  "image": "fwdslsh/toolkit:latest",
  "workspaceMount": "source=${localWorkspaceFolder},target=/workspace,type=bind",
  "workspaceFolder": "/workspace",
  
  "forwardPorts": [3000, 8080],
  "portsAttributes": {
    "3000": {
      "label": "Unify Dev Server",
      "onAutoForward": "notify"
    },
    "8080": {
      "label": "Additional Services",
      "onAutoForward": "silent"
    }
  },
  
  "containerEnv": {
    "UNIFY_PORT": "3000",
    "INFORM_DELAY": "1000"
  },
  
  "customizations": {
    "vscode": {
      "extensions": [
        "fwdslsh.unify-vscode",
        "ms-vscode.vscode-typescript-next",
        "bradlc.vscode-tailwindcss",
        "esbenp.prettier-vscode"
      ],
      "settings": {
        "terminal.integrated.defaultProfile.linux": "bash",
        "files.watcherExclude": {
          "**/node_modules/**": true,
          "**/dist/**": true
        }
      }
    }
  },
  
  "postCreateCommand": "echo 'fwdslsh Toolkit ready!' && unify --version && inform --version && giv --version"
}
```

## Tool Configuration

### Git Setup

Configure Git for commit signing and user information:

```bash
# Inside the container
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Optional: Set up GPG signing
git config --global commit.gpgsign true
git config --global user.signingkey YOUR_GPG_KEY
```

### AI Provider Setup

Configure your preferred AI provider for Giv:

```bash
# OpenAI (recommended for beginners)
export OPENAI_API_KEY="your-api-key-here"
export GIV_API_MODEL="gpt-4"

# Anthropic Claude
export ANTHROPIC_API_KEY="your-api-key-here"
export GIV_API_MODEL="claude-3-sonnet"

# Test the configuration
giv --version
echo "API configuration test:" && giv summary --dry-run
```

### Persistent Configuration

Create configuration files that persist across container restarts:

#### ~/.giv.yaml (Global Giv config)
```yaml
api:
  provider: "openai"
  model: "gpt-4"
  temperature: 0.7

commit:
  template: |
    {{ type }}({{ scope }}): {{ description }}
    
    {{ body }}
    
    {{ footer }}

changelog:
  format: "keepachangelog"
  include_authors: true
```

#### ~/.inform.yaml (Global Inform config)
```yaml
defaults:
  delay: 1000
  concurrency: 3
  max_pages: 100
  timeout: 30

extraction:
  content_selectors:
    - "main"
    - ".content"
    - "article"
  exclude_selectors:
    - "nav"
    - "footer"
    - ".sidebar"
```

## Common Workflows

### 1. Creating a New Unify Site

```bash
# Inside the container
cd /workspace

# Create new site
npm create unify-site my-site
cd my-site

# Start development
bun run dev
# Visit http://localhost:3000
```

### 2. Content Extraction with Inform

```bash
# Extract documentation
inform https://docs.example.com \
  --output-dir extracted-content \
  --max-pages 50 \
  --delay 1000

# View results with Glow
glow extracted-content/index.md
```

### 3. AI-Powered Git Workflow

```bash
# Make changes to your project
echo "New feature" >> README.md

# Stage changes
git add .

# Generate AI commit message
giv message

# Review and commit
git commit -F .giv-message

# Generate changelog
giv changelog > CHANGELOG.md
```

### 4. Complete Development Workflow

```bash
# 1. Create site
npm create unify-site project
cd project

# 2. Extract content
inform https://source.com --output-dir content
cp -r content/* src/pages/

# 3. Develop
bun run dev  # In one terminal

# 4. Make changes and commit
git add .
giv message
git commit -F .giv-message

# 5. Build for production
bun run build

# 6. Generate documentation
giv release-notes > RELEASE_NOTES.md
```

## Advanced Features

### Custom Scripts and Automation

Create project-specific scripts in your workspace:

#### extract-and-build.sh
```bash
#!/bin/bash
# Complete content extraction and site build

set -e

echo "🔍 Extracting content..."
inform ${SOURCE_URL:-https://docs.example.com} \
  --output-dir extracted \
  --max-pages ${MAX_PAGES:-100} \
  --delay ${DELAY:-1000}

echo "📁 Organizing content..."
mkdir -p src/pages
cp -r extracted/* src/pages/

echo "🏗️ Building site..."
bun run build

echo "📝 Generating documentation..."
giv changelog > CHANGELOG.md
giv release-notes > RELEASE_NOTES.md

echo "✅ Build complete!"
```

Make it executable and use:
```bash
chmod +x extract-and-build.sh
./extract-and-build.sh
```

### Team Collaboration

#### Shared Configuration

Create team configurations in your repository:

**team-config/docker-compose.override.yml:**
```yaml
version: '3.8'

services:
  fwdslsh:
    environment:
      # Team-specific defaults
      - GIV_COMMIT_TEMPLATE=conventional
      - INFORM_USER_AGENT=OurTeam-Crawler/1.0
      - UNIFY_LIVE_RELOAD=true
    volumes:
      # Mount team configurations
      - ./team-config/.giv.yaml:/root/.giv.yaml:ro
      - ./team-config/.inform.yaml:/root/.inform.yaml:ro
```

**team-config/.giv.yaml:**
```yaml
commit:
  scope_prompts: true
  require_scope: ["feat", "fix", "docs"]
  
team:
  reviewers: ["@tech-lead", "@senior-dev"]
  auto_assign: true

templates:
  commit: ".giv-templates/team-commit.txt"
```

### GitHub Actions Integration

Use the Toolkit in CI/CD:

**.github/workflows/build-and-deploy.yml:**
```yaml
name: Build and Deploy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for Giv
      
      - name: Build with fwdslsh Toolkit
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:/workspace \
            -e OPENAI_API_KEY=${{ secrets.OPENAI_API_KEY }} \
            -e GIT_AUTHOR_NAME="${{ github.actor }}" \
            -e GIT_AUTHOR_EMAIL="${{ github.actor }}@users.noreply.github.com" \
            fwdslsh/toolkit:latest \
            bash -c "
              cd /workspace
              
              # Extract content if needed
              if [ -f 'extract.sh' ]; then
                echo '🔍 Extracting content...'
                ./extract.sh
              fi
              
              # Build site
              if [ -f 'package.json' ]; then
                echo '🏗️ Building site...'
                bun install
                bun run build
              fi
              
              # Generate documentation
              echo '📝 Generating documentation...'
              giv changelog > CHANGELOG.md
              giv release-notes > RELEASE_NOTES.md
              
              echo '✅ Build complete!'
            "
      
      - name: Deploy to GitHub Pages
        if: github.ref == 'refs/heads/main'
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

## Troubleshooting

### Common Issues

#### Container Won't Start
```bash
# Check Docker status
docker info

# Check available resources
docker system df

# Clean up unused containers/images
docker system prune -f
```

#### Permission Issues (Linux)
```bash
# Fix ownership of mounted files
sudo chown -R $USER:$USER ~/fwdslsh-projects

# Or run container with user mapping
docker run -it --rm \
  -u $(id -u):$(id -g) \
  -v $(pwd):/workspace \
  fwdslsh/toolkit
```

#### Port Conflicts
```bash
# Check what's using the port
lsof -i :3000

# Use different ports
docker run -it --rm \
  -p 3001:3000 \
  -p 8081:8080 \
  fwdslsh/toolkit
```

#### Out of Disk Space
```bash
# Clean up Docker
docker system prune -a -f

# Remove unused volumes
docker volume prune -f

# Check space usage
docker system df
```

### Performance Optimization

#### Memory Usage
```bash
# Limit container memory
docker run -it --rm \
  --memory="2g" \
  --memory-swap="2g" \
  fwdslsh/toolkit

# Monitor resource usage
docker stats fwdslsh-dev
```

#### Faster Builds
```bash
# Use BuildKit for faster builds
export DOCKER_BUILDKIT=1

# Pre-pull images
docker pull fwdslsh/toolkit:latest

# Use specific version for consistency
docker pull fwdslsh/toolkit:v1.2.3
```

### Debug Mode

```bash
# Run with debug output
docker run -it --rm \
  -e DEBUG=1 \
  -e VERBOSE=1 \
  fwdslsh/toolkit

# Access logs
docker logs fwdslsh-dev

# Interactive debugging
docker exec -it fwdslsh-dev bash
```

## Best Practices

### Security
- Never include API keys in Dockerfiles
- Use environment variables for secrets
- Regularly update the base image
- Scan images for vulnerabilities

### Performance  
- Use persistent volumes for caches
- Limit resource usage appropriately
- Clean up unused containers regularly
- Use multi-stage builds for custom images

### Team Collaboration
- Document custom configurations
- Use version tags for consistency
- Share common scripts and workflows
- Establish clear naming conventions

## Summary

The fwdslsh Toolkit provides:

✅ **Complete Environment** - All tools pre-configured and ready
✅ **Team Consistency** - Same setup for everyone
✅ **Easy CI/CD** - Drop-in container for automation
✅ **Flexible Configuration** - Customize for your needs
✅ **Development Ready** - VS Code and editor integration
✅ **Production Tested** - Used by fwdslsh team and community

### Next Steps

- [Complete Development Environment Setup](../dev-environment-setup.md) - Full setup guide
- [Team Collaboration Workflows](../guides/team-workflows.md) - Working together
- [CI/CD Integration](../integrations/cicd-integration.md) - Automated workflows
- [Custom Tool Development](../guides/custom-tools.md) - Extend the toolkit

**Happy developing with the fwdslsh Toolkit!** 🚀

---

*This guide is part of the [fwdslsh examples repository](https://github.com/fwdslsh/examples). For more documentation and examples, visit the [complete guide](../getting-started.md).*