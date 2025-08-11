# Using Giv and Unify Together for Automated Documentation and Static Site Generation

## Introduction

The combination of Giv's AI-assisted Git workflow management and Unify's static site generation capabilities creates a powerful automated documentation pipeline. This integration enables teams to maintain high-quality documentation websites with minimal manual intervention, leveraging AI to enhance commit messages and content while automatically updating and deploying static sites.

### Benefits of the Integration

- **Automated Documentation Workflows**: Changes to documentation sources automatically trigger AI-enhanced commit messages and site rebuilds
- **Enhanced Content Quality**: AI-assisted commit messages provide better change tracking and release note generation
- **Streamlined Deployment**: Direct integration between Git workflows and static site deployment
- **Improved Team Collaboration**: Consistent documentation standards and automated quality checks

### Prerequisites

Before starting this tutorial, ensure you have:

- **Git** installed and configured
- **Node.js** 16+ installed
- **Docker** (optional, for containerized development)
- Basic familiarity with static site generators
- Understanding of Git workflows and CI/CD concepts

### Tool Versions

This guide is compatible with:
- Giv: v1.0.0+
- Unify: v1.0.0+
- Node.js: 16.x, 18.x, 20.x

## Setting Up the Integration

### Installing and Configuring Both Tools

First, install both Giv and Unify in your project:

```bash
# Install Giv globally for AI-assisted Git workflows
npm install -g @fwdslsh/giv

# Install Unify CLI for static site generation
npm install -g @fwdslsh/unify

# Verify installations
giv --version
unify --version
```

### Directory Structure for Documentation Projects

Create a well-organized project structure that supports both tools:

```
my-documentation-site/
├── docs/                    # Source documentation
│   ├── guides/
│   ├── tutorials/
│   └── reference/
├── src/                     # Unify site source
│   ├── layouts/
│   ├── components/
│   └── pages/
├── public/                  # Static assets
├── dist/                    # Generated site output
├── .giv/                    # Giv configuration
│   ├── prompts/
│   └── config.json
├── unify.config.js          # Unify configuration
├── package.json
└── README.md
```

### Git Repository Setup with Proper Branching Strategy

Configure your repository with a branching strategy that supports automated documentation:

```bash
# Initialize repository with main branch
git init
git checkout -b main

# Create development branch for ongoing work
git checkout -b develop

# Create documentation-specific branch for content updates
git checkout -b docs/updates

# Set up branch protection and merge strategies
cat > .gitattributes << EOF
# Ensure consistent line endings
* text=auto

# Mark documentation files for special handling
docs/** linguist-documentation
*.md linguist-documentation

# Binary files
*.png binary
*.jpg binary
*.gif binary
EOF
```

## Basic Workflow Configuration

### Configuring Giv for Documentation-Focused Commit Messages

Create specialized Giv prompts for documentation work:

```bash
# Create Giv configuration directory
mkdir -p .giv/prompts

# Create documentation-specific prompt
cat > .giv/prompts/docs.md << 'EOF'
You are a technical documentation specialist helping to create clear, informative commit messages for documentation changes.

## Context
The repository contains technical documentation for software tools, including:
- User guides and tutorials
- API reference documentation  
- Integration examples
- Migration guides

## Instructions
Analyze the provided git diff and create a commit message that:

1. **Starts with a docs: prefix** for documentation changes
2. **Uses present tense** ("Add guide" not "Added guide")
3. **Describes the specific content changes** made
4. **Mentions the target audience** when relevant
5. **Highlights new features or improvements** covered

## Format
```
docs: [action] [content type] for [target audience/use case]

Optional longer description if the change:
- Introduces new concepts or workflows
- Updates existing documentation significantly
- Adds important examples or code samples
- Addresses common user questions or issues
```

## Examples
- `docs: Add comprehensive Unify tutorial for new developers`
- `docs: Update API reference with new authentication methods`
- `docs: Improve Getting Started guide with Docker setup instructions`
- `docs: Add troubleshooting section for common installation issues`

Focus on making the commit message immediately useful for:
- Release note generation
- Change tracking across documentation updates
- Team coordination and review processes
EOF

# Create main Giv configuration
cat > .giv/config.json << 'EOF'
{
  "defaultPrompt": "docs",
  "prompts": {
    "docs": ".giv/prompts/docs.md"
  },
  "provider": "openai",
  "model": "gpt-4",
  "maxTokens": 150,
  "temperature": 0.3
}
EOF
```

### Setting Up Unify Site Structure for Documentation

Initialize your Unify site with documentation-optimized structure:

```bash
# Create Unify configuration
cat > unify.config.js << 'EOF'
export default {
  input: 'src',
  output: 'dist',
  
  // Documentation-specific optimizations
  site: {
    title: 'Documentation Portal',
    description: 'Comprehensive documentation for our tools',
    url: 'https://docs.example.com',
    author: 'Development Team'
  },

  // Content processing
  markdown: {
    highlightCode: true,
    generateToc: true,
    linkifyHeadings: true
  },

  // SEO and performance
  seo: {
    generateSitemap: true,
    generateRobots: true,
    socialCards: true
  },

  // Documentation features
  features: {
    search: true,
    navigation: true,
    breadcrumbs: true,
    lastModified: true,
    editOnGitHub: true
  }
}
EOF

# Create package.json with documentation scripts
cat > package.json << 'EOF'
{
  "name": "documentation-site",
  "version": "1.0.0",
  "description": "Automated documentation site with Giv and Unify",
  "scripts": {
    "dev": "unify serve --watch",
    "build": "unify build",
    "preview": "unify serve --output dist",
    "commit": "giv commit",
    "deploy": "npm run build && npm run upload",
    "upload": "echo 'Configure your deployment command here'"
  },
  "devDependencies": {
    "@fwdslsh/unify": "^1.0.0"
  }
}
EOF
```

### Creating Templates for Different Document Types

Create reusable templates for consistent documentation:

```bash
# Create layout templates
mkdir -p src/layouts

# Main documentation layout
cat > src/layouts/docs.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ title }} | {{ site.title }}</title>
    <meta name="description" content="{{ description || site.description }}">
    
    <!-- Documentation-specific meta tags -->
    <meta property="og:type" content="article">
    <meta property="og:title" content="{{ title }}">
    <meta property="og:description" content="{{ description }}">
    
    <link rel="stylesheet" href="/styles/docs.css">
    <link rel="stylesheet" href="/styles/syntax-highlighting.css">
</head>
<body>
    <header class="docs-header">
        <nav class="docs-nav">
            <a href="/" class="logo">{{ site.title }}</a>
            <div class="nav-links">
                <a href="/guides/">Guides</a>
                <a href="/tutorials/">Tutorials</a>
                <a href="/reference/">Reference</a>
            </div>
        </nav>
    </header>

    <main class="docs-main">
        <aside class="docs-sidebar">
            {{ include 'navigation.html' }}
        </aside>
        
        <article class="docs-content">
            <header class="content-header">
                <h1>{{ title }}</h1>
                {% if description %}
                <p class="description">{{ description }}</p>
                {% endif %}
                
                <div class="meta">
                    {% if lastModified %}
                    <span class="last-modified">Last updated: {{ lastModified | date }}</span>
                    {% endif %}
                    {% if editUrl %}
                    <a href="{{ editUrl }}" class="edit-link">Edit on GitHub</a>
                    {% endif %}
                </div>
            </header>
            
            <div class="content">
                {{ content }}
            </div>
            
            <footer class="content-footer">
                {{ include 'content-navigation.html' }}
            </footer>
        </article>
    </main>
    
    <script src="/scripts/docs.js"></script>
</body>
</html>
EOF

# Tutorial template
cat > src/layouts/tutorial.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ title }} | Tutorial | {{ site.title }}</title>
    <meta name="description" content="{{ description || site.description }}">
    
    <link rel="stylesheet" href="/styles/docs.css">
    <link rel="stylesheet" href="/styles/tutorial.css">
    <link rel="stylesheet" href="/styles/syntax-highlighting.css">
</head>
<body>
    <header class="tutorial-header">
        <nav class="breadcrumb">
            <a href="/">Home</a> →
            <a href="/tutorials/">Tutorials</a> →
            <span>{{ title }}</span>
        </nav>
    </header>

    <main class="tutorial-main">
        <article class="tutorial-content">
            <header class="tutorial-intro">
                <h1>{{ title }}</h1>
                {% if description %}
                <p class="description">{{ description }}</p>
                {% endif %}
                
                <div class="tutorial-meta">
                    {% if difficulty %}
                    <span class="difficulty difficulty-{{ difficulty }}">{{ difficulty | title }}</span>
                    {% endif %}
                    {% if duration %}
                    <span class="duration">⏱️ {{ duration }}</span>
                    {% endif %}
                    {% if prerequisites %}
                    <div class="prerequisites">
                        <h3>Prerequisites:</h3>
                        <ul>
                        {% for prereq in prerequisites %}
                            <li>{{ prereq }}</li>
                        {% endfor %}
                        </ul>
                    </div>
                    {% endif %}
                </div>
            </header>
            
            <div class="tutorial-steps">
                {{ content }}
            </div>
            
            <footer class="tutorial-footer">
                <div class="next-steps">
                    <h3>What's Next?</h3>
                    {% if nextSteps %}
                    <ul>
                    {% for step in nextSteps %}
                        <li><a href="{{ step.url }}">{{ step.title }}</a></li>
                    {% endfor %}
                    </ul>
                    {% endif %}
                </div>
            </footer>
        </article>
    </main>
    
    <script src="/scripts/tutorial.js"></script>
</body>
</html>
EOF
```

## Automated Pipeline Setup

### GitHub Actions Workflow Configuration

Create a comprehensive CI/CD pipeline that integrates both tools:

```bash
# Create GitHub Actions workflow
mkdir -p .github/workflows

cat > .github/workflows/docs-pipeline.yml << 'EOF'
name: Documentation Pipeline

on:
  push:
    branches: [ main, develop ]
    paths: 
      - 'docs/**'
      - 'src/**'
      - 'unify.config.js'
      - 'package.json'
  pull_request:
    branches: [ main ]
    paths:
      - 'docs/**'
      - 'src/**'

jobs:
  validate-commits:
    name: Validate Commit Messages
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Giv
        run: npm install -g @fwdslsh/giv
      
      - name: Validate Commit Messages
        run: |
          # Check if commits follow documentation standards
          giv validate --since origin/main
      
      - name: Generate Release Notes Preview
        if: github.event_name == 'pull_request'
        run: |
          giv release-notes --since origin/main --format markdown > release-notes-preview.md
      
      - name: Comment PR with Release Notes
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const notes = fs.readFileSync('release-notes-preview.md', 'utf8');
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `## 📝 Documentation Changes Preview\n\n${notes}`
            });

  build-site:
    name: Build Documentation Site
    runs-on: ubuntu-latest
    needs: validate-commits
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Install Unify
        run: npm install -g @fwdslsh/unify
      
      - name: Build Site
        run: |
          unify build --verbose
      
      - name: Run Site Tests
        run: |
          # Test for broken links
          npm run test:links
          
          # Test for accessibility
          npm run test:a11y
          
          # Test for performance
          npm run test:performance
      
      - name: Upload Build Artifacts
        uses: actions/upload-artifact@v4
        with:
          name: documentation-site
          path: dist/
          retention-days: 7

  deploy-preview:
    name: Deploy Preview
    runs-on: ubuntu-latest
    needs: build-site
    if: github.event_name == 'pull_request'
    steps:
      - name: Download Build Artifacts
        uses: actions/download-artifact@v4
        with:
          name: documentation-site
          path: dist/
      
      - name: Deploy to Preview Environment
        run: |
          # Configure your preview deployment here
          echo "Deploying to preview environment..."
          echo "Preview URL: https://preview-${{ github.event.number }}.docs.example.com"

  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: build-site
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - name: Download Build Artifacts
        uses: actions/download-artifact@v4
        with:
          name: documentation-site
          path: dist/
      
      - name: Deploy to Production
        run: |
          # Configure your production deployment here
          echo "Deploying to production..."
          echo "Production URL: https://docs.example.com"
      
      - name: Generate and Archive Release Notes
        run: |
          # Generate comprehensive release notes
          giv release-notes --since $(git describe --tags --abbrev=0) --format markdown > RELEASE_NOTES.md
          
          # Archive for future reference
          cp RELEASE_NOTES.md "releases/$(date +%Y-%m-%d).md"

  update-search-index:
    name: Update Search Index
    runs-on: ubuntu-latest
    needs: deploy-production
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      
      - name: Update Algolia Search Index
        run: |
          # Update search index with new content
          npm run search:update
        env:
          ALGOLIA_APP_ID: ${{ secrets.ALGOLIA_APP_ID }}
          ALGOLIA_API_KEY: ${{ secrets.ALGOLIA_API_KEY }}
EOF
```

### Triggering Builds on Documentation Changes

Configure automated triggers for documentation updates:

```bash
# Create pre-commit hook for Giv integration
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

# Check if this is a documentation change
if git diff --cached --name-only | grep -E "(docs/|\.md$)" > /dev/null; then
    echo "Documentation changes detected..."
    
    # Run Giv to enhance commit message if in interactive mode
    if [ -t 0 ]; then
        echo "Running Giv to generate commit message..."
        giv commit --interactive
    fi
    
    # Validate documentation format
    echo "Validating documentation format..."
    npm run docs:validate
    
    if [ $? -ne 0 ]; then
        echo "Documentation validation failed. Please fix issues before committing."
        exit 1
    fi
fi
EOF

chmod +x .git/hooks/pre-commit

# Create documentation validation script
cat > scripts/validate-docs.js << 'EOF'
const fs = require('fs');
const path = require('path');
const glob = require('glob');

async function validateDocumentation() {
    console.log('🔍 Validating documentation...');
    
    const errors = [];
    
    // Find all markdown files
    const mdFiles = glob.sync('docs/**/*.md');
    
    for (const file of mdFiles) {
        const content = fs.readFileSync(file, 'utf8');
        
        // Check for required frontmatter
        if (!content.startsWith('---\n')) {
            errors.push(`${file}: Missing frontmatter`);
        }
        
        // Check for title
        if (!content.includes('title:')) {
            errors.push(`${file}: Missing title in frontmatter`);
        }
        
        // Check for description
        if (!content.includes('description:')) {
            errors.push(`${file}: Missing description in frontmatter`);
        }
        
        // Check for broken internal links
        const linkRegex = /\[([^\]]+)\]\(([^)]+)\)/g;
        let match;
        while ((match = linkRegex.exec(content)) !== null) {
            const link = match[2];
            if (link.startsWith('./') || link.startsWith('../')) {
                const linkPath = path.resolve(path.dirname(file), link);
                if (!fs.existsSync(linkPath)) {
                    errors.push(`${file}: Broken internal link: ${link}`);
                }
            }
        }
    }
    
    if (errors.length > 0) {
        console.error('❌ Documentation validation failed:');
        errors.forEach(error => console.error(`  ${error}`));
        process.exit(1);
    }
    
    console.log('✅ Documentation validation passed');
}

validateDocumentation().catch(console.error);
EOF

# Add validation script to package.json
npm pkg set scripts.docs:validate="node scripts/validate-docs.js"
npm pkg set scripts.test:links="echo 'Configure link checker'"
npm pkg set scripts.test:a11y="echo 'Configure accessibility tests'"
npm pkg set scripts.test:performance="echo 'Configure performance tests'"
```

## Advanced Integration Patterns

### Using Giv to Generate Release Notes that Feed into Unify

Create automated release note generation and integration:

```bash
# Create release notes template for Unify
mkdir -p src/pages/releases

cat > src/pages/releases/template.md << 'EOF'
---
layout: docs
title: "Release Notes - {{ version }}"
description: "Latest updates and improvements in version {{ version }}"
date: {{ date }}
version: {{ version }}
---

# Release Notes - {{ version }}

*Released on {{ date }}*

{{ releaseNotes }}

## Upgrade Instructions

```bash
# Update to the latest version
npm update @fwdslsh/unify @fwdslsh/giv

# Verify installation
unify --version
giv --version
```

## Breaking Changes

{{ breakingChanges }}

## Migration Guide

{{ migrationGuide }}

## Support

If you encounter any issues with this release:

1. Check our [troubleshooting guide](/docs/troubleshooting/)
2. Search [existing issues](https://github.com/fwdslsh/examples/issues)
3. Create a [new issue](https://github.com/fwdslsh/examples/issues/new) if needed

---

*For previous releases, see our [release archive](/releases/).*
EOF

# Create automated release note generator
cat > scripts/generate-release-notes.js << 'EOF'
const { execSync } = require('child_process');
const fs = require('fs');
const path = require('path');

async function generateReleaseNotes() {
    try {
        // Get version from package.json
        const packageJson = JSON.parse(fs.readFileSync('package.json', 'utf8'));
        const version = packageJson.version;
        
        // Generate release notes using Giv
        const releaseNotes = execSync('giv release-notes --format markdown', { encoding: 'utf8' });
        
        // Get breaking changes
        const breakingChanges = execSync('giv breaking-changes --format markdown', { encoding: 'utf8' }) || 'No breaking changes in this release.';
        
        // Load template
        let template = fs.readFileSync('src/pages/releases/template.md', 'utf8');
        
        // Replace placeholders
        template = template
            .replace(/\{\{ version \}\}/g, version)
            .replace(/\{\{ date \}\}/g, new Date().toISOString().split('T')[0])
            .replace(/\{\{ releaseNotes \}\}/g, releaseNotes)
            .replace(/\{\{ breakingChanges \}\}/g, breakingChanges)
            .replace(/\{\{ migrationGuide \}\}/g, 'See individual sections above for migration details.');
        
        // Write release notes file
        const outputPath = `src/pages/releases/v${version}.md`;
        fs.writeFileSync(outputPath, template);
        
        console.log(`✅ Generated release notes: ${outputPath}`);
        
        // Update releases index
        updateReleasesIndex(version);
        
    } catch (error) {
        console.error('❌ Failed to generate release notes:', error.message);
        process.exit(1);
    }
}

function updateReleasesIndex(version) {
    const indexPath = 'src/pages/releases/index.md';
    let content = fs.readFileSync(indexPath, 'utf8');
    
    const newEntry = `- [v${version}](/releases/v${version}/) - ${new Date().toISOString().split('T')[0]}`;
    
    // Insert new entry at the top of the list
    content = content.replace(
        /^(## All Releases\s*\n)/m,
        `$1\n${newEntry}\n`
    );
    
    fs.writeFileSync(indexPath, content);
    console.log(`✅ Updated releases index`);
}

generateReleaseNotes().catch(console.error);
EOF

# Create releases index page
cat > src/pages/releases/index.md << 'EOF'
---
layout: docs
title: "Release Notes"
description: "Complete history of updates and improvements"
---

# Release Notes

Stay up to date with the latest features, improvements, and bug fixes.

## Latest Release

Check out our [latest release](/releases/latest/) for the most recent updates.

## All Releases

*Release history will be automatically populated here*

## Release Schedule

We follow [Semantic Versioning](https://semver.org/) and typically release:

- **Major versions** (1.0.0): Breaking changes, major new features
- **Minor versions** (1.1.0): New features, backward compatible
- **Patch versions** (1.1.1): Bug fixes, security updates

## Subscribe to Updates

- 🔔 [Watch our repository](https://github.com/fwdslsh/examples) for notifications
- 📧 Subscribe to our [mailing list](mailto:updates@fwdslsh.dev)
- 📡 Follow us on [Twitter](https://twitter.com/fwdslsh) for announcements

## Changelog Format

Our release notes follow the [Keep a Changelog](https://keepachangelog.com/) format and include:

- **Added** for new features
- **Changed** for changes in existing functionality  
- **Deprecated** for soon-to-be removed features
- **Removed** for now removed features
- **Fixed** for any bug fixes
- **Security** for vulnerability fixes
EOF

# Add to package.json scripts
npm pkg set scripts.release:notes="node scripts/generate-release-notes.js"
```

### Creating Documentation Changelogs with AI Assistance

Implement AI-powered changelog generation:

```bash
# Create changelog configuration for Giv
cat > .giv/prompts/changelog.md << 'EOF'
You are a technical writer creating changelog entries for documentation updates.

## Context
This repository contains technical documentation that is regularly updated with:
- New guides and tutorials
- API reference updates
- Integration examples
- Bug fixes and improvements
- Content reorganization

## Instructions
Analyze the provided git diff and create a changelog entry that:

1. **Categorizes changes** using standard changelog sections:
   - Added (new documentation)
   - Changed (updates to existing docs)
   - Fixed (corrections and improvements)
   - Removed (deprecated or obsolete content)

2. **Describes user impact** rather than technical implementation
3. **Links to relevant documentation** when helpful
4. **Maintains consistency** with previous changelog entries

## Format
```markdown
### [Version] - YYYY-MM-DD

#### Added
- New comprehensive guide for [topic] targeting [audience]
- Code examples for [specific use case]
- Troubleshooting section for [common issue]

#### Changed  
- Improved [guide/section] with [specific enhancement]
- Updated [API/feature] documentation for version [X.Y.Z]
- Restructured [content area] for better navigation

#### Fixed
- Corrected [specific error or inaccuracy]
- Fixed broken links in [section/guide]
- Improved clarity in [specific area]

#### Removed
- Deprecated documentation for [obsolete feature]
- Redundant content that was merged into [other guide]
```

## Examples
- Added: "New Docker setup guide for development environments"
- Changed: "Updated API authentication examples with latest security practices"  
- Fixed: "Corrected installation commands in Quick Start guide"
- Removed: "Legacy configuration examples no longer supported"

Focus on helping users understand what changed and how it benefits them.
EOF

# Create automated changelog updater
cat > scripts/update-changelog.js << 'EOF'
const { execSync } = require('child_process');
const fs = require('fs');

async function updateChangelog() {
    try {
        // Get latest changes since last tag
        const lastTag = execSync('git describe --tags --abbrev=0', { encoding: 'utf8' }).trim();
        
        // Generate changelog entry using Giv
        const changelogEntry = execSync(`giv changelog --since ${lastTag} --prompt changelog`, { encoding: 'utf8' });
        
        // Read current changelog
        const changelogPath = 'CHANGELOG.md';
        let changelog = fs.readFileSync(changelogPath, 'utf8');
        
        // Find insertion point (after the header)
        const headerEnd = changelog.indexOf('\n## ');
        if (headerEnd === -1) {
            throw new Error('Could not find insertion point in CHANGELOG.md');
        }
        
        // Insert new entry
        const updatedChangelog = 
            changelog.slice(0, headerEnd) + 
            '\n' + changelogEntry + '\n' + 
            changelog.slice(headerEnd);
        
        // Write updated changelog
        fs.writeFileSync(changelogPath, updatedChangelog);
        
        console.log('✅ Updated CHANGELOG.md with latest changes');
        
        // Create Unify page for this changelog entry
        await createChangelogPage(changelogEntry);
        
    } catch (error) {
        console.error('❌ Failed to update changelog:', error.message);
        process.exit(1);
    }
}

async function createChangelogPage(entry) {
    const version = require('../package.json').version;
    const date = new Date().toISOString().split('T')[0];
    
    const pageContent = `---
layout: docs
title: "What's New in v${version}"
description: "Latest documentation updates and improvements"
date: ${date}
version: ${version}
---

# What's New in v${version}

*Updated on ${date}*

${entry}

## Previous Updates

See our [complete changelog](/changelog/) for all updates and improvements.

---

*Have feedback on these updates? [Let us know!](https://github.com/fwdslsh/examples/issues/new)*
`;

    fs.writeFileSync(`src/pages/whats-new/v${version}.md`, pageContent);
    console.log(`✅ Created changelog page for v${version}`);
}

updateChangelog().catch(console.error);
EOF

# Add script to package.json
npm pkg set scripts.changelog:update="node scripts/update-changelog.js"
```

### Multi-Repository Documentation Aggregation

Set up documentation aggregation across multiple repositories:

```bash
# Create aggregation configuration
cat > docs-aggregation.config.js << 'EOF'
export default {
  sources: [
    {
      name: 'giv',
      repo: 'https://github.com/fwdslsh/giv.git',
      branch: 'main',
      docs: 'docs/',
      prefix: 'giv/'
    },
    {
      name: 'unify', 
      repo: 'https://github.com/fwdslsh/unify.git',
      branch: 'main',
      docs: 'docs/',
      prefix: 'unify/'
    },
    {
      name: 'inform',
      repo: 'https://github.com/fwdslsh/inform.git', 
      branch: 'main',
      docs: 'docs/',
      prefix: 'inform/'
    }
  ],
  
  aggregation: {
    outputDir: 'docs/aggregated/',
    updateSchedule: '0 6 * * *', // Daily at 6 AM
    preserveStructure: true,
    generateIndex: true
  },
  
  processing: {
    updateLinks: true,
    normalizeFrontmatter: true,
    addSourceInfo: true
  }
}
EOF

# Create aggregation script
cat > scripts/aggregate-docs.js << 'EOF'
const { execSync } = require('child_process');
const fs = require('fs');
const path = require('path');
const config = require('../docs-aggregation.config.js').default;

async function aggregateDocs() {
    console.log('🔄 Starting documentation aggregation...');
    
    const tempDir = path.join(__dirname, '../temp-repos');
    const outputDir = config.aggregation.outputDir;
    
    // Clean and create directories
    if (fs.existsSync(tempDir)) {
        fs.rmSync(tempDir, { recursive: true });
    }
    fs.mkdirSync(tempDir, { recursive: true });
    fs.mkdirSync(outputDir, { recursive: true });
    
    for (const source of config.sources) {
        console.log(`📥 Fetching documentation from ${source.name}...`);
        
        const repoDir = path.join(tempDir, source.name);
        
        try {
            // Clone repository
            execSync(`git clone --depth 1 --branch ${source.branch} ${source.repo} ${repoDir}`, 
                { stdio: 'inherit' });
            
            const docsPath = path.join(repoDir, source.docs);
            const targetPath = path.join(outputDir, source.prefix);
            
            if (fs.existsSync(docsPath)) {
                // Copy documentation
                fs.cpSync(docsPath, targetPath, { recursive: true });
                
                // Process files
                await processDocFiles(targetPath, source);
                
                console.log(`✅ Processed documentation from ${source.name}`);
            } else {
                console.warn(`⚠️ No docs found at ${docsPath} for ${source.name}`);
            }
            
        } catch (error) {
            console.error(`❌ Failed to process ${source.name}:`, error.message);
        }
    }
    
    // Generate aggregated index
    await generateAggregatedIndex();
    
    // Clean up
    fs.rmSync(tempDir, { recursive: true });
    
    console.log('✅ Documentation aggregation complete');
}

async function processDocFiles(dirPath, source) {
    const files = fs.readdirSync(dirPath, { withFileTypes: true });
    
    for (const file of files) {
        const filePath = path.join(dirPath, file.name);
        
        if (file.isDirectory()) {
            await processDocFiles(filePath, source);
        } else if (file.name.endsWith('.md')) {
            await processMarkdownFile(filePath, source);
        }
    }
}

async function processMarkdownFile(filePath, source) {
    let content = fs.readFileSync(filePath, 'utf8');
    
    // Add source information to frontmatter
    if (config.processing.addSourceInfo) {
        if (content.startsWith('---\n')) {
            const frontmatterEnd = content.indexOf('\n---\n', 4);
            if (frontmatterEnd !== -1) {
                const frontmatter = content.slice(4, frontmatterEnd);
                const body = content.slice(frontmatterEnd + 5);
                
                content = `---
${frontmatter}
source:
  repository: ${source.name}
  originalPath: ${path.relative(source.docs, filePath)}
  lastUpdated: ${new Date().toISOString()}
---${body}`;
            }
        }
    }
    
    // Update internal links
    if (config.processing.updateLinks) {
        content = content.replace(/\]\((?!http)([^)]+)\)/g, (match, link) => {
            if (link.startsWith('./') || link.startsWith('../')) {
                return `](/docs/${source.prefix}${link})`;
            }
            return match;
        });
    }
    
    fs.writeFileSync(filePath, content);
}

async function generateAggregatedIndex() {
    const indexContent = `---
layout: docs
title: "Complete Documentation Index"
description: "Aggregated documentation from all fwdslsh tools"
---

# Complete Documentation Index

This page aggregates documentation from all fwdslsh repositories, updated automatically.

*Last updated: ${new Date().toISOString()}*

## Available Documentation

${config.sources.map(source => `
### ${source.name.charAt(0).toUpperCase() + source.name.slice(1)}

Browse the [${source.name} documentation](/docs/${source.prefix}) for detailed guides and references.

- Repository: [${source.repo}](${source.repo})
- Branch: ${source.branch}
- Local path: \`/docs/${source.prefix}\`
`).join('\n')}

## Integration Guides

- [Using Giv and Unify Together](/docs/integrations/giv-unify-automation/)
- [Complete Workflow with All Tools](/docs/integrations/complete-workflow/)
- [Enterprise Integration Patterns](/docs/enterprise/)

---

*This index is automatically generated from source repositories. For the most up-to-date information, visit the individual repository documentation.*
`;

    fs.writeFileSync(path.join(config.aggregation.outputDir, 'index.md'), indexContent);
}

aggregateDocs().catch(console.error);
EOF

# Add to GitHub Actions workflow
cat >> .github/workflows/docs-pipeline.yml << 'EOF'

  aggregate-docs:
    name: Aggregate Documentation
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    schedule:
      - cron: '0 6 * * *'  # Daily at 6 AM
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          
      - name: Aggregate Documentation
        run: node scripts/aggregate-docs.js
        
      - name: Commit Changes
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          
          if [ -n "$(git status --porcelain)" ]; then
            git add docs/aggregated/
            giv commit --message "docs: Update aggregated documentation from source repositories"
            git push
          fi
EOF

# Add aggregation script to package.json
npm pkg set scripts.docs:aggregate="node scripts/aggregate-docs.js"
```

## Best Practices and Troubleshooting

### Performance Optimization for Large Documentation Sites

Implement performance optimizations for enterprise-scale documentation:

```bash
# Create performance optimization configuration
cat > performance.config.js << 'EOF'
export default {
  // Build optimization
  build: {
    parallel: true,
    incrementalBuilds: true,
    caching: {
      enabled: true,
      directory: '.cache/',
      maxAge: '7d'
    }
  },
  
  // Content optimization  
  content: {
    imageOptimization: true,
    codeHighlighting: {
      lazy: true,
      theme: 'github-light'
    },
    markdown: {
      smartQuotes: true,
      typographer: true
    }
  },
  
  // Runtime optimization
  runtime: {
    lazyLoading: true,
    prefetch: 'viewport',
    compression: 'gzip'
  }
}
EOF

# Create performance monitoring script
cat > scripts/monitor-performance.js << 'EOF'
const lighthouse = require('lighthouse');
const chromeLauncher = require('chrome-launcher');
const fs = require('fs');

async function runPerformanceTests() {
    console.log('🚀 Running performance tests...');
    
    const urls = [
        'http://localhost:3000/',
        'http://localhost:3000/guides/',
        'http://localhost:3000/tutorials/',
        'http://localhost:3000/reference/'
    ];
    
    const results = {};
    
    for (const url of urls) {
        console.log(`Testing ${url}...`);
        
        const chrome = await chromeLauncher.launch({chromeFlags: ['--headless']});
        const options = {
            logLevel: 'info',
            output: 'json',
            onlyCategories: ['performance', 'accessibility', 'best-practices', 'seo'],
            port: chrome.port,
        };
        
        try {
            const runnerResult = await lighthouse(url, options);
            const scores = runnerResult.lhr.categories;
            
            results[url] = {
                performance: Math.round(scores.performance.score * 100),
                accessibility: Math.round(scores.accessibility.score * 100),
                bestPractices: Math.round(scores['best-practices'].score * 100),
                seo: Math.round(scores.seo.score * 100)
            };
            
            console.log(`✅ ${url}:`, results[url]);
            
        } catch (error) {
            console.error(`❌ Failed to test ${url}:`, error.message);
            results[url] = { error: error.message };
        }
        
        await chrome.kill();
    }
    
    // Save results
    fs.writeFileSync('performance-report.json', JSON.stringify(results, null, 2));
    
    // Check if scores meet thresholds
    const thresholds = { performance: 90, accessibility: 95, bestPractices: 90, seo: 95 };
    let allPassed = true;
    
    for (const [url, scores] of Object.entries(results)) {
        if (scores.error) continue;
        
        for (const [metric, score] of Object.entries(scores)) {
            if (score < thresholds[metric]) {
                console.error(`❌ ${url} ${metric}: ${score} < ${thresholds[metric]}`);
                allPassed = false;
            }
        }
    }
    
    if (!allPassed) {
        console.error('❌ Performance tests failed');
        process.exit(1);
    }
    
    console.log('✅ All performance tests passed');
}

runPerformanceTests().catch(console.error);
EOF

# Add performance testing to package.json
npm pkg set scripts.test:performance="node scripts/monitor-performance.js"
npm install --save-dev lighthouse chrome-launcher
```

### Handling Merge Conflicts in Automated Workflows

Create conflict resolution strategies:

```bash
# Create merge conflict resolution script
cat > scripts/resolve-conflicts.js << 'EOF'
const { execSync } = require('child_process');
const fs = require('fs');

async function resolveDocumentationConflicts() {
    try {
        // Check for merge conflicts
        const conflictFiles = execSync('git diff --name-only --diff-filter=U', { encoding: 'utf8' })
            .split('\n')
            .filter(file => file.trim())
            .filter(file => file.endsWith('.md') || file.includes('docs/'));
        
        if (conflictFiles.length === 0) {
            console.log('✅ No documentation conflicts to resolve');
            return;
        }
        
        console.log(`🔧 Resolving conflicts in ${conflictFiles.length} files...`);
        
        for (const file of conflictFiles) {
            await resolveFileConflict(file);
        }
        
        // Generate AI-assisted resolution summary
        const summary = execSync('giv conflict-summary', { encoding: 'utf8' });
        console.log('📝 Conflict resolution summary:');
        console.log(summary);
        
    } catch (error) {
        console.error('❌ Failed to resolve conflicts:', error.message);
        process.exit(1);
    }
}

async function resolveFileConflict(filePath) {
    console.log(`🔧 Resolving conflict in ${filePath}...`);
    
    const content = fs.readFileSync(filePath, 'utf8');
    
    // Simple automatic resolution for documentation files
    if (content.includes('<<<<<<< HEAD')) {
        // For documentation, prefer the incoming changes (usually more recent)
        const resolved = content
            .replace(/<<<<<<< HEAD\n/g, '')
            .replace(/=======\n/g, '')
            .replace(/>>>>>>> .+\n/g, '');
        
        fs.writeFileSync(filePath, resolved);
        execSync(`git add ${filePath}`);
        
        console.log(`✅ Auto-resolved ${filePath}`);
    }
}

resolveDocumentationConflicts().catch(console.error);
EOF

# Create conflict prevention pre-merge hook
cat > .git/hooks/pre-merge-commit << 'EOF'
#!/bin/bash

echo "🔍 Checking for potential documentation conflicts..."

# Check if any documentation files have been modified in both branches
CONFLICT_FILES=$(git diff --name-only HEAD..MERGE_HEAD | grep -E "(docs/|\.md$)" | head -10)

if [ ! -z "$CONFLICT_FILES" ]; then
    echo "⚠️ Documentation files modified in both branches:"
    echo "$CONFLICT_FILES"
    echo ""
    echo "💡 Consider using 'npm run docs:resolve-conflicts' if conflicts occur"
fi
EOF

chmod +x .git/hooks/pre-merge-commit

# Add conflict resolution to package.json
npm pkg set scripts.docs:resolve-conflicts="node scripts/resolve-conflicts.js"
```

### Monitoring and Alerting for Failed Builds

Set up comprehensive monitoring:

```bash
# Create monitoring configuration
cat > monitoring.config.js << 'EOF'
export default {
  healthChecks: {
    buildStatus: {
      enabled: true,
      endpoint: '/health/build',
      timeout: 30000
    },
    contentUpdates: {
      enabled: true,
      checkInterval: '*/10 * * * *',
      maxAge: '1h'
    },
    searchIndex: {
      enabled: true,
      endpoint: '/search/health',
      timeout: 5000
    }
  },
  
  alerts: {
    slack: {
      webhook: process.env.SLACK_WEBHOOK_URL,
      channel: '#docs-alerts'
    },
    email: {
      enabled: true,
      recipients: ['team@fwdslsh.dev']
    }
  },
  
  metrics: {
    buildTime: true,
    deploymentSuccess: true,
    searchPerformance: true,
    userEngagement: true
  }
}
EOF

# Create health check endpoints
cat > scripts/health-check.js << 'EOF'
const express = require('express');
const fs = require('fs');
const { execSync } = require('child_process');

const app = express();
const port = process.env.HEALTH_CHECK_PORT || 3001;

// Build status endpoint
app.get('/health/build', (req, res) => {
    try {
        const buildStatus = {
            timestamp: new Date().toISOString(),
            status: 'healthy',
            lastBuild: getLastBuildTime(),
            version: require('../package.json').version
        };
        
        // Check if build directory exists and is recent
        const buildTime = getLastBuildTime();
        const ageMinutes = (Date.now() - buildTime) / (1000 * 60);
        
        if (ageMinutes > 60) {
            buildStatus.status = 'stale';
            buildStatus.warning = 'Build is over 1 hour old';
        }
        
        res.json(buildStatus);
    } catch (error) {
        res.status(500).json({
            status: 'error',
            error: error.message,
            timestamp: new Date().toISOString()
        });
    }
});

// Search health endpoint
app.get('/health/search', (req, res) => {
    try {
        // Test search functionality
        const searchTest = {
            timestamp: new Date().toISOString(),
            status: 'healthy',
            indexSize: getSearchIndexSize(),
            lastUpdate: getSearchIndexTime()
        };
        
        res.json(searchTest);
    } catch (error) {
        res.status(500).json({
            status: 'error',
            error: error.message,
            timestamp: new Date().toISOString()
        });
    }
});

// Overall health endpoint
app.get('/health', (req, res) => {
    const health = {
        status: 'healthy',
        timestamp: new Date().toISOString(),
        uptime: process.uptime(),
        checks: {
            filesystem: checkFilesystem(),
            git: checkGitStatus(),
            dependencies: checkDependencies()
        }
    };
    
    res.json(health);
});

function getLastBuildTime() {
    try {
        const stats = fs.statSync('dist/index.html');
        return stats.mtime.getTime();
    } catch {
        return 0;
    }
}

function getSearchIndexSize() {
    try {
        const stats = fs.statSync('dist/search-index.json');
        return stats.size;
    } catch {
        return 0;
    }
}

function getSearchIndexTime() {
    try {
        const stats = fs.statSync('dist/search-index.json');
        return stats.mtime.toISOString();
    } catch {
        return null;
    }
}

function checkFilesystem() {
    try {
        fs.accessSync('dist/', fs.constants.R_OK);
        return { status: 'ok', message: 'Build directory accessible' };
    } catch {
        return { status: 'error', message: 'Build directory not accessible' };
    }
}

function checkGitStatus() {
    try {
        const status = execSync('git status --porcelain', { encoding: 'utf8' });
        return { 
            status: 'ok', 
            clean: status.trim() === '',
            uncommitted: status.split('\n').filter(line => line.trim()).length
        };
    } catch (error) {
        return { status: 'error', message: error.message };
    }
}

function checkDependencies() {
    try {
        execSync('npm ls --depth=0', { stdio: 'pipe' });
        return { status: 'ok', message: 'All dependencies installed' };
    } catch {
        return { status: 'warning', message: 'Some dependencies may be missing' };
    }
}

app.listen(port, () => {
    console.log(`Health check server running on port ${port}`);
});

module.exports = app;
EOF

# Create alerting script
cat > scripts/send-alerts.js << 'EOF'
const axios = require('axios');
const nodemailer = require('nodemailer');
const config = require('../monitoring.config.js').default;

async function sendBuildAlert(status, details) {
    const message = {
        text: `🚨 Documentation Build Alert: ${status}`,
        details: details,
        timestamp: new Date().toISOString(),
        environment: process.env.NODE_ENV || 'development'
    };
    
    // Send Slack notification
    if (config.alerts.slack.webhook) {
        await sendSlackAlert(message);
    }
    
    // Send email notification
    if (config.alerts.email.enabled) {
        await sendEmailAlert(message);
    }
}

async function sendSlackAlert(message) {
    try {
        const payload = {
            channel: config.alerts.slack.channel,
            username: 'Docs Bot',
            icon_emoji: ':warning:',
            text: message.text,
            attachments: [
                {
                    color: message.details.includes('error') ? 'danger' : 'warning',
                    fields: [
                        {
                            title: 'Environment',
                            value: message.environment,
                            short: true
                        },
                        {
                            title: 'Timestamp',
                            value: message.timestamp,
                            short: true
                        },
                        {
                            title: 'Details',
                            value: message.details,
                            short: false
                        }
                    ]
                }
            ]
        };
        
        await axios.post(config.alerts.slack.webhook, payload);
        console.log('✅ Slack alert sent');
    } catch (error) {
        console.error('❌ Failed to send Slack alert:', error.message);
    }
}

async function sendEmailAlert(message) {
    try {
        const transporter = nodemailer.createTransporter({
            host: process.env.SMTP_HOST,
            port: process.env.SMTP_PORT,
            secure: process.env.SMTP_SECURE === 'true',
            auth: {
                user: process.env.SMTP_USER,
                pass: process.env.SMTP_PASS
            }
        });
        
        const mailOptions = {
            from: process.env.SMTP_FROM || 'noreply@fwdslsh.dev',
            to: config.alerts.email.recipients.join(','),
            subject: `Documentation Build Alert - ${message.environment}`,
            html: `
                <h2>${message.text}</h2>
                <p><strong>Environment:</strong> ${message.environment}</p>
                <p><strong>Timestamp:</strong> ${message.timestamp}</p>
                <p><strong>Details:</strong></p>
                <pre>${message.details}</pre>
            `
        };
        
        await transporter.sendMail(mailOptions);
        console.log('✅ Email alert sent');
    } catch (error) {
        console.error('❌ Failed to send email alert:', error.message);
    }
}

module.exports = { sendBuildAlert };
EOF

# Add monitoring scripts to package.json
npm pkg set scripts.health:start="node scripts/health-check.js"
npm pkg set scripts.health:check="curl -f http://localhost:3001/health || exit 1"
npm install --save-dev express axios nodemailer
```

## Real-World Examples

### Open Source Project Documentation Pipeline

Complete example for open source documentation automation:

```bash
# Create open source specific configuration
cat > examples/open-source-pipeline.yml << 'EOF'
name: Open Source Documentation Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 2 * * 0'  # Weekly updates

jobs:
  community-docs:
    name: Community Documentation
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Setup Environment
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Tools
        run: |
          npm install -g @fwdslsh/giv @fwdslsh/unify
          npm ci
      
      - name: Generate Community Content
        run: |
          # Generate contributor guide
          giv contributors --format markdown > docs/CONTRIBUTORS.md
          
          # Generate release notes
          giv release-notes --since v1.0.0 --format markdown > docs/RELEASES.md
          
          # Update changelog
          giv changelog --since $(git describe --tags --abbrev=0) >> CHANGELOG.md
      
      - name: Build Documentation Site
        run: |
          unify build --config unify.config.js
      
      - name: Deploy to GitHub Pages
        if: github.ref == 'refs/heads/main'
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
          cname: docs.fwdslsh.dev
      
      - name: Update Search Index
        if: github.ref == 'refs/heads/main'
        run: |
          npm run search:build
          npm run search:deploy
      
      - name: Notify Community
        if: github.ref == 'refs/heads/main'
        run: |
          # Post to Discord/Slack about updates
          node scripts/notify-community.js
EOF

# Create community notification script
cat > examples/notify-community.js << 'EOF'
const { execSync } = require('child_process');

async function notifyDocumentationUpdate() {
    try {
        // Get recent changes
        const changes = execSync('giv summary --since 24h --format json', { encoding: 'utf8' });
        const changeData = JSON.parse(changes);
        
        if (changeData.documentationChanges > 0) {
            const message = `📚 Documentation Updated!\n\n` +
                `${changeData.documentationChanges} files updated\n` +
                `New guides: ${changeData.newGuides}\n` +
                `Improvements: ${changeData.improvements}\n\n` +
                `View latest: https://docs.fwdslsh.dev\n` +
                `Changelog: https://docs.fwdslsh.dev/changelog/`;
            
            await postToDiscord(message);
            await postToTwitter(message);
        }
    } catch (error) {
        console.error('Failed to notify community:', error.message);
    }
}

async function postToDiscord(message) {
    // Discord webhook implementation
    console.log('Posted to Discord:', message);
}

async function postToTwitter(message) {
    // Twitter API implementation
    console.log('Posted to Twitter:', message);
}

notifyDocumentationUpdate();
EOF
```

### Enterprise API Documentation Workflow

Enterprise-focused documentation automation:

```bash
# Create enterprise workflow example
cat > examples/enterprise-api-docs.yml << 'EOF'
name: Enterprise API Documentation

on:
  push:
    branches: [ main, develop, release/* ]
    paths: [ 'api/**', 'docs/api/**' ]

jobs:
  api-docs-enterprise:
    name: API Documentation Pipeline
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.ENTERPRISE_TOKEN }}
      
      - name: Setup Enterprise Environment
        run: |
          # Install enterprise tools
          npm install -g @fwdslsh/giv-enterprise @fwdslsh/unify-enterprise
          
          # Configure enterprise settings
          giv config set enterprise.mode true
          giv config set enterprise.compliance.enabled true
      
      - name: Generate API Documentation
        run: |
          # Extract API changes with compliance tracking
          giv api-changes --compliance-mode --format enterprise-json > api-changes.json
          
          # Generate API documentation with audit trail
          node scripts/generate-api-docs.js --audit --compliance
          
          # Update OpenAPI specifications
          npm run api:generate-specs
      
      - name: Compliance Validation
        run: |
          # Check for breaking changes
          npm run api:breaking-changes
          
          # Validate security documentation
          npm run security:validate-docs
          
          # Check accessibility compliance
          npm run a11y:validate
      
      - name: Enterprise Build
        run: |
          unify build --enterprise --compliance-mode
          
          # Generate executive summary
          giv executive-summary --api-changes > dist/executive-summary.pdf
      
      - name: Deploy to Enterprise Portal
        run: |
          # Deploy to internal portal
          npm run deploy:enterprise
          
          # Update developer portal
          npm run deploy:developer-portal
          
          # Notify stakeholders
          npm run notify:stakeholders
      
      - name: Archive and Audit
        run: |
          # Archive documentation version
          npm run archive:version
          
          # Update audit trail
          npm run audit:update
          
          # Generate compliance report
          npm run compliance:report
EOF
```

### Team Collaboration Patterns

Collaboration workflow examples:

```bash
# Create team collaboration documentation
cat > examples/team-collaboration.md << 'EOF'
# Team Collaboration Patterns with Giv and Unify

## Review Workflow

### 1. Documentation Review Process

```bash
# Reviewer workflow
git checkout feature/new-docs
npm run docs:review-mode

# Generate review summary with Giv
giv review-summary --focus documentation > review-notes.md

# Check documentation quality
npm run docs:quality-check

# Approve and merge
giv commit --review-approval
```

### 2. Multi-Author Documentation

```bash
# Configure team-specific prompts
cat > .giv/prompts/team-docs.md << 'EOF'
You are helping a documentation team create consistent, high-quality content.

## Team Guidelines
- Use inclusive language and clear explanations
- Include code examples that work
- Add troubleshooting sections for complex topics
- Reference related documentation appropriately

## Author Context
Author: {{ author }}
Team: {{ team }}
Expertise: {{ expertise }}

Create commit messages that reflect the collaborative nature of documentation work.
EOF

# Team member workflow
giv commit --prompt team-docs --author "Jane Doe" --team "Platform" --expertise "API Design"
```

### 3. Knowledge Transfer Documentation

```bash
# Generate knowledge transfer materials
giv knowledge-transfer --project-area "Authentication" --audience "New Developers" > docs/knowledge-transfer/auth-overview.md

# Create onboarding documentation
unify build --template onboarding --variables team=platform,role=developer

# Generate training materials
npm run docs:generate-training-materials
```

## Conclusion

The integration of Giv and Unify provides a powerful foundation for automated documentation workflows. This setup enables:

- **Consistent Quality**: AI-assisted commit messages and content generation ensure documentation maintains high standards
- **Automated Workflows**: Minimal manual intervention while maintaining quality control
- **Team Collaboration**: Standardized processes that scale across teams and projects
- **Enterprise Ready**: Compliance, security, and audit features for enterprise environments

By following these patterns and examples, teams can establish robust documentation pipelines that grow with their projects and maintain quality at scale.

### Next Steps

1. **Start Small**: Begin with basic Giv and Unify integration
2. **Add Automation**: Gradually introduce CI/CD pipelines
3. **Scale Up**: Implement enterprise features as needed
4. **Monitor and Improve**: Use metrics to continuously optimize the workflow

For more advanced integration patterns, see:
- [Building a Unified Workflow with Giv, Unify, and Inform](/docs/integrations/complete-workflow/)
- [Enterprise Documentation Patterns](/docs/enterprise/developer-portal/)
- [Advanced CI/CD Integration](/docs/enterprise/github-actions-automation/)