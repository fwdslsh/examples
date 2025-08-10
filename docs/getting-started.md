# Getting Started with fwdslsh Tools

Welcome to the fwdslsh ecosystem! This guide will help you get up and running with our suite of tools designed to streamline web development, content management, and Git workflows.

## Overview

fwdslsh provides three core tools that work together to create a complete development workflow:

1. **Unify** - Build fast, maintainable static sites
2. **Inform** - Extract and manage web content  
3. **Giv** - Generate professional Git documentation

## Quick Installation

### Option 1: Docker Toolkit (Recommended for New Users)

The easiest way to get started is with our all-in-one Docker toolkit:

```bash
# Pull the toolkit
docker pull fwdslsh/toolkit:latest

# Run interactively
docker run -it fwdslsh/toolkit

# All tools are now available: unify, inform, giv
```

### Option 2: Individual Tool Installation

Install only the tools you need:

```bash
# Unify (requires Bun)
curl -fsSL https://bun.sh/install | bash
bun add -g @fwdslsh/unify

# Inform (requires Bun)
bun add -g @fwdslsh/inform

# Giv (standalone binary)
curl -fsSL https://raw.githubusercontent.com/fwdslsh/giv/main/install.sh | sh
```

## Your First fwdslsh Workflow

Let's create a complete workflow using all three tools:

### 1. Create a New Site with Unify

```bash
# Create a new site
npm create unify-site my-docs-site
cd my-docs-site

# The starter includes:
# - src/ directory with templates
# - Basic site structure
# - Example components
```

### 2. Extract Content with Inform

```bash
# Extract documentation from a website
inform https://docs.example.com \
  --output-dir src/content \
  --max-pages 50 \
  --delay 1000

# This creates markdown files in src/content/
# maintaining the original site structure
```

### 3. Build Your Site

```bash
# Start development server
unify serve

# Your site is now live at http://localhost:3000
# Changes auto-reload as you edit files
```

### 4. Generate Professional Git Messages

```bash
# Make some changes to your site
echo "New content" >> src/index.html

# Generate AI-powered commit message
giv message

# Example output:
# "docs: Add new content section to homepage
# 
# - Added informational content to main index page
# - Improves user experience and site completeness"
```

### 5. Build for Production

```bash
# Build optimized site
unify build --pretty-urls --base-url https://yourdomain.com

# Deploy the dist/ folder to your hosting provider
```

## Common Workflows

### Documentation Site Workflow

Perfect for technical documentation, API docs, and knowledge bases:

```bash
# 1. Extract existing docs
inform https://old-docs.com --output-dir content

# 2. Create Unify site
npm create unify-site docs-site
cd docs-site

# 3. Organize content
mv ../content/* src/pages/

# 4. Customize templates
edit src/.components/header.html
edit src/.components/sidebar.html

# 5. Build and deploy
unify build --pretty-urls
```

### Blog/Marketing Site Workflow

Ideal for blogs, marketing sites, and content-driven websites:

```bash
# 1. Create site from blog template
npm create unify-site my-blog -- --template blog

# 2. Extract content inspiration
inform https://competitor-blog.com --max-pages 20

# 3. Create your content
mkdir src/posts
# Write your posts in Markdown with frontmatter

# 4. Customize design
edit src/styles.css
edit src/.components/layout.html

# 5. Generate release notes for launches
giv release-notes v1.0.0..HEAD
```

## Next Steps

Now that you have the basics down, explore these resources:

### Learn Individual Tools
- [Unify Quick Start](unify/quick-start.md) - Deep dive into static site generation
- [Inform User Guide](inform/user-guide.md) - Master web content extraction
- [Giv Setup Guide](giv/setup.md) - Configure AI-powered Git workflows

### Explore Advanced Features
- [Integration Guides](../integrations/) - Connect with GitHub Pages, CI/CD, and more
- [Best Practices](../best-practices/) - Learn proven patterns and techniques
- [Examples](../examples/) - See real-world implementations

### Get Help
- [Troubleshooting](../troubleshooting/common-issues.md) - Solve common problems
- [Community Discussions](https://github.com/orgs/fwdslsh/discussions) - Connect with other users
- [Contributing](../contributing/contributing.md) - Help improve the tools

## Configuration Tips

### Environment Setup

Create a `.env` file for consistent configuration:

```bash
# AI configuration for Giv
OPENAI_API_KEY=your-api-key
GIV_API_MODEL=gpt-4

# Unify defaults
UNIFY_PORT=3000
UNIFY_BASE_URL=https://yourdomain.com

# Inform settings
INFORM_DELAY=1000
INFORM_CONCURRENCY=3
```

### VS Code Integration

Install the Unify VS Code extension for better development experience:

1. Open VS Code
2. Search for "Unify" in extensions
3. Install the official fwdslsh Unify extension
4. Enjoy syntax highlighting and IntelliSense

## Support

If you need help:

1. Check the [troubleshooting guide](../troubleshooting/common-issues.md)
2. Search [existing issues](https://github.com/fwdslsh/examples/issues)
3. Join the [community discussions](https://github.com/orgs/fwdslsh/discussions)
4. Create a [new issue](https://github.com/fwdslsh/examples/issues/new) if needed

Welcome to the fwdslsh community! 🚀