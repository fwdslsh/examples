# First Steps with Giv

Welcome to Giv, the AI-powered Git assistant that transforms your development workflow! This guide will get you started with intelligent commit messages, changelogs, and release notes in minutes.

## What is Giv?

Giv is a command-line tool that uses AI to analyze your Git repositories and generate:
- **Commit messages** that actually describe what changed
- **Changelogs** that highlight important changes for users
- **Release notes** that tell a compelling story about your releases
- **Project summaries** for documentation and reports

## Quick Installation

### Option 1: Install Script (Recommended)
```bash
curl -fsSL https://raw.githubusercontent.com/fwdslsh/giv/main/install.sh | sh
```

### Option 2: Python Package Manager
```bash
pip install giv
```

### Option 3: Download Binary
Visit [Giv Releases](https://github.com/fwdslsh/giv/releases) and download the binary for your platform.

## Configure Your AI Provider

Giv needs an AI provider to generate content. Set up your preferred option:

### OpenAI (Recommended for beginners)
```bash
export OPENAI_API_KEY="your-api-key-here"
export GIV_API_MODEL="gpt-4"  # or gpt-3.5-turbo
```

### Anthropic Claude
```bash
export ANTHROPIC_API_KEY="your-api-key-here"
export GIV_API_MODEL="claude-3-sonnet"
```

### Local Models (Advanced)
```bash
export GIV_API_BASE="http://localhost:11434"  # For Ollama
export GIV_API_MODEL="llama2"
```

💡 **Tip**: Add these to your `.bashrc` or `.zshrc` for permanent configuration.

## Your First Giv Commands

Let's walk through the most common Giv workflows:

### 1. Generate a Commit Message

Make some changes to your project, then:

```bash
# Stage your changes as usual
git add .

# Let Giv generate the commit message
giv message

# Example output:
# feat(auth): Add password reset functionality
# 
# - Implement forgot password flow with email verification
# - Add password reset form with validation
# - Update user model to support reset tokens
# - Add email templates for password reset notifications
```

The generated message follows [Conventional Commits](https://www.conventionalcommits.org/) format automatically!

### 2. Create a Changelog

Generate a changelog for all changes since your last release:

```bash
# Generate changelog from last tag to HEAD
giv changelog

# Generate changelog for specific version range  
giv changelog v1.0.0..v1.1.0

# Output goes to CHANGELOG.md by default
```

Example output:
```markdown
# Changelog

## [1.1.0] - 2024-01-15

### Added
- Password reset functionality with email verification
- User profile editing capabilities
- Dark mode theme support

### Changed  
- Improved login form validation
- Updated API documentation

### Fixed
- Memory leak in image processing
- Mobile responsiveness issues
```

### 3. Generate Release Notes

Create compelling release notes for your latest version:

```bash
# Generate release notes for the latest tag
giv release-notes

# Generate for specific version
giv release-notes v1.1.0

# Include comparison with previous version
giv release-notes v1.0.0..v1.1.0
```

### 4. Summarize Your Project

Get an AI-generated overview of your entire project:

```bash
# Analyze your project structure and recent changes
giv summary

# Focus on specific directory
giv summary --path src/

# Include specific time range
giv summary --since "2024-01-01"
```

## Customizing Giv for Your Team

### Create a Configuration File

Create `.giv.yaml` in your project root:

```yaml
# .giv.yaml
api:
  model: "gpt-4"
  temperature: 0.7

commit:
  template: |
    {{ type }}({{ scope }}): {{ description }}
    
    {{ body }}
    
    {{ footer }}
  
  types:
    - feat: New features
    - fix: Bug fixes  
    - docs: Documentation changes
    - style: Code style changes
    - refactor: Code refactoring
    - test: Adding or updating tests
    - chore: Maintenance tasks

changelog:
  format: "keepachangelog"  # or "conventional"
  sections:
    - Added
    - Changed
    - Deprecated
    - Removed
    - Fixed
    - Security

release_notes:
  include_contributors: true
  include_stats: true
  highlight_breaking_changes: true
```

### Team-Specific Templates

Create custom templates for your organization:

```bash
# Create a templates directory
mkdir .giv-templates

# Create custom commit template
cat > .giv-templates/commit.txt << 'EOF'
{{ type }}{% if scope %}({{ scope }}){% endif %}: {{ description }}

{% if body %}
{{ body }}
{% endif %}

{% if breaking %}
BREAKING CHANGE: {{ breaking }}
{% endif %}

Resolves: #{{ issue_number }}
Reviewed-by: {{ reviewer }}
EOF
```

Update your `.giv.yaml`:
```yaml
commit:
  template_file: ".giv-templates/commit.txt"
```

## Advanced Workflows

### 1. Automated Release Process

Create a script for automated releases:

```bash
#!/bin/bash
# release.sh

VERSION=$1
if [ -z "$VERSION" ]; then
    echo "Usage: ./release.sh <version>"
    exit 1
fi

# Update version in package.json, setup.py, etc.
# ... your version bump logic ...

# Generate changelog
giv changelog > CHANGELOG.md

# Create release notes
giv release-notes > release-notes.md

# Commit changes
git add .
giv message --template "chore: Release version $VERSION"
git commit -F .giv-message

# Tag release
git tag -a "v$VERSION" -F release-notes.md

# Push changes
git push origin main --tags

echo "Release $VERSION completed!"
```

### 2. CI/CD Integration

Add Giv to your GitHub Actions:

```yaml
# .github/workflows/release.yml
name: Release
on:
  push:
    tags: ['v*']

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for Giv

      - name: Install Giv
        run: |
          curl -fsSL https://raw.githubusercontent.com/fwdslsh/giv/main/install.sh | sh
          echo "$HOME/.local/bin" >> $GITHUB_PATH

      - name: Generate Release Notes
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: |
          giv release-notes ${{ github.ref_name }} > release-notes.md

      - name: Create GitHub Release
        uses: actions/create-release@v1
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
          body_path: release-notes.md
```

## Common Use Cases

### Documentation Teams
```bash
# Generate commit messages for doc updates
giv message --scope docs

# Create changelogs focusing on user-facing changes
giv changelog --filter "feat,fix,docs"

# Summarize documentation changes
giv summary --path docs/ --since "last month"
```

### Open Source Maintainers
```bash
# Generate contributor-friendly release notes
giv release-notes --include-contributors --highlight-first-time

# Create comprehensive changelogs
giv changelog --include-all --group-by-type

# Summarize project for new contributors
giv summary --include-stats --explain-structure
```

### Enterprise Teams
```bash
# Use team-specific templates
giv message --template .giv-templates/enterprise.txt

# Generate reports for stakeholders  
giv summary --format markdown --include-metrics

# Create release notes with security focus
giv release-notes --highlight-security --include-compliance
```

## Best Practices

### 1. Commit Message Guidelines
- Make meaningful commits before using `giv message`
- Review generated messages before committing
- Customize templates for your team's conventions
- Use scope prefixes for better organization

### 2. Changelog Management
- Generate changelogs regularly, not just at release time
- Keep CHANGELOG.md in version control
- Review and edit AI-generated content for accuracy
- Follow [Keep a Changelog](https://keepachangelog.com/) format

### 3. Security Considerations
- Store API keys securely (environment variables, not in code)
- Review generated content before publishing
- Use organization accounts for team access
- Consider local models for sensitive projects

## Troubleshooting

### Common Issues

**"No API key configured"**
```bash
# Set your API key
export OPENAI_API_KEY="your-key-here"

# Or add to .giv.yaml
echo "api:" >> .giv.yaml
echo "  key: your-key-here" >> .giv.yaml
```

**"Not a git repository"**
```bash
# Make sure you're in a git repository
git status

# Initialize if needed
git init
```

**"Too many changes to analyze"**
```bash
# Use --max-changes to limit analysis
giv message --max-changes 50

# Or focus on specific paths
giv message --path src/
```

### Getting Help

- 📖 [Full Giv Documentation](user-guide.md)
- 🔧 [Configuration Reference](../reference/giv-cli.md)
- 🐛 [Troubleshooting Guide](../troubleshooting/giv.md)
- 💬 [Community Support](https://github.com/orgs/fwdslsh/discussions)

## What's Next?

### Explore Advanced Features
- [Custom Prompt Engineering](custom-prompts.md) - Create specialized templates
- [Team Collaboration Workflows](team-workflows.md) - Set up shared configurations
- [AI Provider Comparison](ai-providers.md) - Choose the best model for your needs

### Integration Guides  
- [Giv + CI/CD Integration](../integrations/giv-cicd.md) - Automate your release process
- [Giv + GitHub Integration](../integrations/giv-github.md) - Enhanced GitHub workflows
- [Giv + Documentation Tools](../integrations/giv-docs.md) - Generate docs automatically

### Real-World Examples
- [Release Automation Examples](../examples/giv/release-automation/) - Complete automation setups
- [Team Configuration Examples](../examples/giv/team-configs/) - Proven team setups

**Happy committing with Giv!** 🚀