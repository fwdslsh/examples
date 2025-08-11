# fwdslsh Examples & Documentation

Welcome to the comprehensive documentation and examples repository for all [fwdslsh](https://github.com/fwdslsh) tools! This repository contains detailed guides, tutorials, usage examples, and integration documentation to help you get the most out of the fwdslsh ecosystem.

## 🛠️ fwdslsh Tools Overview

### Core Tools

#### [Unify](https://github.com/fwdslsh/unify) - Modern Static Site Generator
A lightweight, Bun-powered static site generator with server-side includes and live development features.
- **Language**: JavaScript (Bun)
- **Purpose**: Build maintainable static sites with component-based architecture
- **Key Features**: Zero learning curve, live reload, multi-format support

#### [Inform](https://github.com/fwdslsh/inform) - Web Content Crawler  
High-performance web crawler that extracts main content and converts pages to Markdown.
- **Language**: JavaScript (Bun)
- **Purpose**: Extract and convert web content to documentation
- **Key Features**: Concurrent crawling, LLMS.txt support, intelligent content extraction

#### [Giv](https://github.com/fwdslsh/giv) - AI-Powered Git Assistant
CLI tool that generates commit messages, changelogs, and release notes using AI.
- **Language**: Python
- **Purpose**: Transform Git history into professional documentation
- **Key Features**: Multiple AI backends, self-contained binaries, template system

### Supporting Tools & Resources

#### [Toolkit](https://github.com/fwdslsh/toolkit) - Containerized Development Environment
Docker-based toolkit containing all fwdslsh tools in one convenient package.

#### [Website](https://github.com/fwdslsh/website) - Official fwdslsh Website
The main website showcasing all fwdslsh tools and resources.

### Unify Ecosystem

#### [Unify VSCode Extension](https://github.com/fwdslsh/unify-vscode)
VSCode extension providing syntax highlighting and tools for Unify development.

#### [Create Unify Site](https://github.com/fwdslsh/create-unify-site)  
NPM package for quickly scaffolding new Unify sites with `npm create unify-site`.

#### [Unify Starter](https://github.com/fwdslsh/unify-starter)
Template repository for creating new Unify sites with best practices.

#### [Unify Examples](https://github.com/fwdslsh/unify-examples)
Collection of example Unify sites demonstrating various features and use cases.

## 📚 Documentation Index

### Getting Started Guides
- [fwdslsh Tools: Complete Introduction](docs/ecosystem-overview.md) - High-level overview of all tools and their relationships
- [Getting Started with fwdslsh Tools](docs/getting-started.md) - Your first steps with the ecosystem
- [Complete Development Environment Setup](docs/dev-environment-setup.md) - Docker, native, and hybrid installation options

#### Quick Start Tutorials (5-10 minutes each)
- [5-Minute Unify Tutorial](docs/unify/5-minute-tutorial.md) - From zero to live site in 5 minutes
- [First Steps with Giv](docs/giv/first-steps.md) - AI-powered Git workflows and setup
- [Inform in 10 Minutes](docs/inform/10-minute-guide.md) - Content extraction and conversion basics
- [Zero to Live Site in 10 Minutes](docs/tutorials/zero-to-live-10-minutes.md) - Complete workflow with all tools

#### Tool-Specific Setup
- [Unify Quick Start Guide](docs/unify/quick-start.md) - Build your first static site
- [Inform Installation & Setup](docs/inform/installation.md) - Web crawling and content extraction
- [Giv Setup & Configuration](docs/giv/setup.md) - AI-powered Git workflows
- [Toolkit Docker Environment](docs/toolkit/setup.md) - All-in-one containerized development

### User Guides
- [Unify User Guide](docs/unify/user-guide.md) - Complete guide to static site generation
- [Inform User Guide](docs/inform/user-guide.md) - Web crawling and content management
- [Giv User Guide](docs/giv/user-guide.md) - AI-assisted Git workflows and documentation

### Tutorials
- [Building a Documentation Site with Unify](docs/tutorials/unify-docs-site.md)
- [Building a Blog with Unify](docs/tutorials/unify-blog.md) - Complete blog creation with components, SEO, and deployment
- [Extracting Documentation with Inform](docs/tutorials/inform-docs-extraction.md)
- [Automating Release Notes with Giv](docs/tutorials/giv-release-automation.md)

### Integration Guides
- [Unify + GitHub Pages Integration](docs/integrations/unify-github-pages.md)
- [Inform + Documentation Workflows](docs/integrations/inform-doc-workflows.md)
- [Giv + CI/CD Integration](docs/integrations/giv-cicd.md)
- [Using the fwdslsh Toolkit](docs/toolkit/setup.md) - Docker-based development environment

### Usage Examples
- [Unify Examples](docs/examples/unify/) - Real-world Unify site examples
- [Inform Examples](docs/examples/inform/) - Web crawling scenarios and scripts
- [Giv Examples](docs/examples/giv/) - Git workflow automation examples

### Best Practices
- [Static Site Best Practices](docs/best-practices/static-sites.md)
- [Documentation Workflow Best Practices](docs/best-practices/documentation-workflows.md)
- [Git History Management](docs/best-practices/git-history.md)

### Reference
- [Unify CLI Reference](docs/reference/unify-cli.md)
- [Inform CLI Reference](docs/reference/inform-cli.md)
- [Giv CLI Reference](docs/reference/giv-cli.md)
- [Configuration Reference](docs/reference/configuration.md)

### Troubleshooting
- [Common Issues & Solutions](docs/troubleshooting/common-issues.md)
- [Unify Troubleshooting](docs/troubleshooting/unify.md)
- [Inform Troubleshooting](docs/troubleshooting/inform.md)
- [Giv Troubleshooting](docs/troubleshooting/giv.md)

## 🚀 Quick Start

### Install the Toolkit (Recommended)
```bash
# Using Docker (includes all tools)
docker pull fwdslsh/toolkit:latest
docker run -it fwdslsh/toolkit

# Or install tools individually
bun add -g @fwdslsh/unify @fwdslsh/inform
pip install giv
```

### Your First fwdslsh Project
```bash
# Create a new Unify site
npm create unify-site my-site
cd my-site

# Extract content with Inform
inform https://example.com/docs --output-dir content

# Generate commit messages with Giv
giv message

# Build and serve your site
unify serve
```

## 🤝 Contributing

We welcome contributions to both the tools and documentation! See:
- [Contributing to fwdslsh](docs/contributing/contributing.md)
- [Documentation Style Guide](docs/contributing/style-guide.md)
- [Tool-specific Contributing Guides](docs/contributing/)

## 📄 License

All fwdslsh tools and documentation are released under CC-BY-4.0 license.

## 🔗 Links

- [fwdslsh Organization](https://github.com/fwdslsh)
- [Official Website](https://github.com/fwdslsh/website)
- [Community Discussions](https://github.com/orgs/fwdslsh/discussions)
