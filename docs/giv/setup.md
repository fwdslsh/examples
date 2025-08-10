# Giv Setup & Configuration Guide

Giv is an AI-powered Git assistant that transforms your Git history into professional commit messages, changelogs, release notes, and more. This guide will help you get up and running quickly.

## What is Giv?

Giv (pronounced "give") analyzes your Git changes and uses AI to generate:

- **Commit Messages**: Professional, descriptive commit messages from staged changes
- **Changelogs**: Structured summaries of changes between versions
- **Release Notes**: Marketing-friendly announcements for new releases
- **Summaries**: High-level overviews of development activity

## Quick Installation

### Option 1: One-Line Install (Recommended)

```bash
curl -fsSL https://raw.githubusercontent.com/fwdslsh/giv/main/install.sh | sh
```

This script automatically:
- Detects your operating system
- Downloads the appropriate binary
- Installs to `/usr/local/bin` (or `~/.local/bin` if no sudo)
- Verifies the installation

### Option 2: Manual Binary Install

Download from [GitHub Releases](https://github.com/fwdslsh/giv/releases):

```bash
# Linux
curl -L https://github.com/fwdslsh/giv/releases/latest/download/giv-linux -o giv
chmod +x giv
sudo mv giv /usr/local/bin/

# macOS
curl -L https://github.com/fwdslsh/giv/releases/latest/download/giv-macos -o giv
chmod +x giv
sudo mv giv /usr/local/bin/

# Windows (PowerShell)
Invoke-WebRequest -Uri "https://github.com/fwdslsh/giv/releases/latest/download/giv-windows.exe" -OutFile "giv.exe"
```

### Option 3: Python Package

```bash
# Install via pip
pip install giv

# Or with pipx (recommended for CLI tools)
pipx install giv
```

### Option 4: Docker

```bash
# Pull the image
docker pull fwdslsh/giv:latest

# Run giv commands
docker run --rm -v $(pwd):/workspace fwdslsh/giv message
```

## Initial Setup

### 1. Verify Installation

```bash
giv --version
# Should output: giv version x.x.x
```

### 2. Initialize in Your Project

```bash
# Navigate to your Git repository
cd your-project

# Initialize giv (creates .giv/ directory with templates)
giv init

# This creates:
# .giv/
# ├── config.yaml
# ├── templates/
# │   ├── commit_message_prompt.md
# │   ├── changelog_prompt.md
# │   └── release_notes_prompt.md
```

### 3. Configure AI Provider

Giv supports multiple AI providers. Choose one:

#### OpenAI (Recommended)

```bash
# Set API key
export OPENAI_API_KEY="your-api-key-here"

# Configure model (optional, defaults to gpt-4)
giv config set api.model "gpt-4"
giv config set api.url "https://api.openai.com/v1/chat/completions"
```

#### Anthropic (Claude)

```bash
# Set API key
export ANTHROPIC_API_KEY="your-api-key-here"

# Configure for Claude
giv config set api.url "https://api.anthropic.com/v1/messages"
giv config set api.model "claude-3-5-sonnet-20241022"
giv config set api.headers.x-api-key "$ANTHROPIC_API_KEY"
giv config set api.headers.anthropic-version "2023-06-01"
```

#### Local (Ollama)

```bash
# Install and start Ollama
curl -fsSL https://ollama.ai/install.sh | sh
ollama serve &

# Pull a model
ollama pull llama3.2

# Configure giv
giv config set api.url "http://localhost:11434/v1/chat/completions"
giv config set api.model "llama3.2"
giv config set api.headers.Authorization ""  # No auth needed for local
```

#### Custom Endpoint

```bash
# For any OpenAI-compatible API
giv config set api.url "https://your-api-endpoint.com/v1/chat/completions"
giv config set api.model "your-model-name"
giv config set api.headers.Authorization "Bearer your-token"
```

## Basic Usage

### Generate Commit Messages

```bash
# Stage some changes
git add .

# Generate commit message
giv message

# Example output:
# "feat: Add user authentication system
# 
# - Implement JWT-based authentication
# - Add login and registration endpoints
# - Include password hashing with bcrypt
# - Add middleware for protected routes"

# Use the generated message
git commit -m "$(giv message)"
```

### Create Changelogs

```bash
# Generate changelog for version range
giv changelog v1.0.0..HEAD

# Generate since last tag
giv changelog $(git describe --tags --abbrev=0)..HEAD

# Generate for specific commits
giv changelog abc123..def456
```

### Generate Release Notes

```bash
# Release notes for current version
giv release-notes v1.1.0..HEAD

# With custom template
giv release-notes v1.1.0..HEAD --template-file custom-release.md
```

### Summarize Activity

```bash
# Summarize recent activity
giv summary --since "1 week ago"

# Summarize specific range
giv summary v1.0.0..HEAD

# Summarize by author
giv summary --author "your-name" --since "1 month ago"
```

## Configuration

### Configuration Files

Giv uses a hierarchy of configuration files:

1. **Global**: `~/.giv/config.yaml`
2. **Project**: `./.giv/config.yaml`
3. **Environment Variables**: `GIV_*` variables
4. **Command Line**: `--config-*` flags

### Configuration Options

#### API Settings

```yaml
# .giv/config.yaml
api:
  url: "https://api.openai.com/v1/chat/completions"
  model: "gpt-4"
  headers:
    Authorization: "Bearer ${OPENAI_API_KEY}"
    Content-Type: "application/json"
  timeout: 30
  max_tokens: 1000
```

#### Default Behavior

```yaml
defaults:
  max_commits: 50          # Maximum commits to analyze
  include_diff: true       # Include code diff in prompts
  include_files: true      # Include changed file names
  template_style: "conventional"  # conventional, descriptive, or minimal
```

#### Git Settings

```yaml
git:
  ignore_merge_commits: true
  ignore_paths:
    - "*.lock"
    - "package-lock.json"
    - "yarn.lock"
  include_paths:
    - "src/**"
    - "docs/**"
```

### Environment Variables

You can override any configuration with environment variables:

```bash
# API configuration
export GIV_API_URL="https://api.openai.com/v1/chat/completions"
export GIV_API_MODEL="gpt-4"
export OPENAI_API_KEY="your-key"

# Behavior settings
export GIV_MAX_COMMITS="25"
export GIV_INCLUDE_DIFF="false"
```

### Command-Line Configuration

```bash
# Set configuration values
giv config set api.model "gpt-3.5-turbo"
giv config set defaults.max_commits 25
giv config set git.ignore_merge_commits false

# Get configuration values
giv config get api.model
giv config get defaults

# List all configuration
giv config list

# Reset to defaults
giv config reset
```

## Templates

### Understanding Templates

Giv uses Markdown templates with variables to generate AI prompts. Templates are stored in `.giv/templates/`:

- `commit_message_prompt.md`: For commit messages
- `changelog_prompt.md`: For changelogs
- `release_notes_prompt.md`: For release notes
- `summary_prompt.md`: For summaries

### Template Variables

Available variables in templates:

- `{git_diff}`: The Git diff of changes
- `{file_list}`: List of changed files
- `{commit_count}`: Number of commits
- `{commit_range}`: Git revision range (e.g., "v1.0.0..HEAD")
- `{commits}`: Formatted list of commits
- `{repo_name}`: Repository name
- `{branch_name}`: Current branch name

### Customizing Templates

#### Commit Message Template

Edit `.giv/templates/commit_message_prompt.md`:

```markdown
You are an expert developer writing Git commit messages.

Based on the following changes, write a clear, concise commit message following the Conventional Commits format:

**Changed Files:**
{file_list}

**Git Diff:**
```
{git_diff}
```

Requirements:
- Use conventional commit format: type(scope): description
- Keep the subject line under 50 characters
- Include a body if the change is complex
- Use present tense ("add" not "added")
- Be specific and descriptive

Types to use: feat, fix, docs, style, refactor, test, chore
```

#### Changelog Template

Edit `.giv/templates/changelog_prompt.md`:

```markdown
Create a changelog section for the following Git commits.

**Commits ({commit_count} total):**
{commits}

**Commit Range:** {commit_range}

Format as markdown with these sections:
- ### Added (new features)
- ### Changed (modifications to existing features)
- ### Deprecated (features marked for removal)
- ### Removed (deleted features)
- ### Fixed (bug fixes)
- ### Security (security-related changes)

Group related changes together and use bullet points.
Be concise but descriptive.
```

### Using Custom Templates

```bash
# Use custom template file
giv message --template-file my-commit-template.md

# Use different built-in style
giv message --style descriptive  # or conventional, minimal

# Override template variables
giv changelog v1.0.0..HEAD --var "version=2.0.0"
```

## Advanced Usage

### Working with Branches

```bash
# Generate changelog for feature branch
giv changelog main..feature-branch

# Compare branches
giv summary main..develop

# Release notes for merge
giv release-notes $(git merge-base main feature-branch)..feature-branch
```

### Filtering Content

```bash
# Only include specific file types
giv message --include-paths "*.js" "*.ts"

# Exclude certain paths
giv changelog v1.0.0..HEAD --exclude-paths "package-lock.json" "*.lock"

# Filter by author
giv summary --author "john@example.com" --since "2 weeks ago"
```

### Automation and CI/CD

#### GitHub Actions

```yaml
# .github/workflows/changelog.yml
name: Generate Changelog
on:
  release:
    types: [published]

jobs:
  changelog:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - name: Install Giv
        run: curl -fsSL https://raw.githubusercontent.com/fwdslsh/giv/main/install.sh | sh
      
      - name: Generate Changelog
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: |
          PREV_TAG=$(git describe --tags --abbrev=0 HEAD~1)
          giv changelog $PREV_TAG..HEAD > CHANGELOG.md
      
      - name: Commit Changelog
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          git add CHANGELOG.md
          git commit -m "docs: Update changelog for ${{ github.event.release.tag_name }}"
          git push
```

#### Pre-commit Hook

```bash
# .git/hooks/prepare-commit-msg
#!/bin/sh
# Generate commit message if none provided

if [ -z "$2" ]; then
    # Only if there's no existing message
    giv message > "$1"
fi
```

Make it executable:
```bash
chmod +x .git/hooks/prepare-commit-msg
```

### Batch Operations

```bash
# Generate multiple outputs
giv message > commit.txt
giv changelog v1.0.0..HEAD > changelog.md
giv release-notes v1.0.0..HEAD > release-notes.md

# Process multiple repositories
for repo in repo1 repo2 repo3; do
    cd $repo
    giv changelog v1.0.0..HEAD > ../changelogs/$repo-changelog.md
    cd ..
done
```

## Troubleshooting

### Common Issues

#### API Key Not Found

```bash
# Check if environment variable is set
echo $OPENAI_API_KEY

# Set it in your shell profile
echo 'export OPENAI_API_KEY="your-key"' >> ~/.bashrc
source ~/.bashrc
```

#### Binary Compatibility (Linux)

```bash
# Check GLIBC version
ldd --version

# If too old, use pip install instead
pip install giv
```

#### Git Repository Not Found

```bash
# Ensure you're in a Git repository
git status

# Initialize if needed
git init
```

#### Empty or Poor Quality Output

```bash
# Check if changes are staged
git status

# Stage changes first
git add .

# Verify configuration
giv config list

# Try with more context
giv message --include-diff --max-commits 10
```

### Performance Issues

```bash
# Reduce analysis scope
giv config set defaults.max_commits 10
giv config set defaults.include_diff false

# Use faster model
giv config set api.model "gpt-3.5-turbo"

# Increase timeout for slow networks
giv config set api.timeout 60
```

### Getting Help

```bash
# Show help for any command
giv --help
giv message --help
giv changelog --help

# Check version and configuration
giv --version
giv config list

# Enable debug output
giv --debug message
```

## Best Practices

### Commit Messages

1. **Stage changes first**: Always `git add` before running `giv message`
2. **Review generated messages**: Edit if needed before committing
3. **Use meaningful chunks**: Commit related changes together
4. **Customize templates**: Adapt to your team's style

### Changelogs

1. **Use semantic versioning**: Tag releases properly (v1.0.0, v1.1.0)
2. **Regular generation**: Generate changelogs for each release
3. **Review and edit**: AI-generated content may need human touch
4. **Keep history**: Archive changelogs for each version

### Team Usage

1. **Shared configuration**: Commit `.giv/config.yaml` to your repository
2. **Template consistency**: Standardize templates across projects
3. **API key management**: Use environment variables, not config files
4. **Documentation**: Document your giv workflow in project README

## Next Steps

- Explore [Advanced Examples](../examples/giv/) for complex workflows
- Read about [CI/CD Integration](../integrations/giv-cicd.md)
- Check the [Troubleshooting Guide](../troubleshooting/giv.md)
- Join the [community discussions](https://github.com/orgs/fwdslsh/discussions)

Happy generating! 🤖