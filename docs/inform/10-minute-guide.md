# Inform in 10 Minutes

Learn how to extract and convert web content to documentation-ready Markdown with Inform, the powerful web crawler designed for content teams and developers.

## What is Inform?

Inform is a specialized web crawler that:
- **Extracts main content** from web pages, filtering out navigation and ads
- **Converts to Markdown** with proper formatting and structure
- **Preserves links and images** for complete content migration
- **Generates LLMS.txt** for AI-friendly content summaries
- **Maintains site structure** for easy navigation

Perfect for documentation migration, content archival, and knowledge base creation!

## Quick Installation

### Option 1: Using Bun (Recommended)
```bash
# Install Bun if you haven't already
curl -fsSL https://bun.sh/install | bash

# Install Inform globally
bun add -g @fwdslsh/inform
```

### Option 2: Using npm
```bash
npm install -g @fwdslsh/inform
```

### Option 3: Docker Toolkit
```bash
docker pull fwdslsh/toolkit:latest
docker run -it fwdslsh/toolkit
# Inform is pre-installed and ready to use
```

## Your First Content Extraction

Let's extract documentation from a website in just a few commands:

### 1. Extract a Single Page (30 seconds)

```bash
# Extract one page to see how Inform works
inform https://docs.example.com/getting-started

# Output: Creates a markdown file in the current directory
# getting-started.md
```

That's it! You now have a clean Markdown file with just the main content.

### 2. Extract an Entire Documentation Site (2 minutes)

```bash
# Extract up to 50 pages from a documentation site
inform https://docs.example.com \
  --output-dir extracted-docs \
  --max-pages 50 \
  --delay 1000

# Creates:
# extracted-docs/
# ├── index.md
# ├── getting-started.md  
# ├── api/
# │   ├── authentication.md
# │   └── endpoints.md
# └── guides/
#     ├── installation.md
#     └── configuration.md
```

### 3. Generate LLMS.txt for AI (1 minute)

```bash
# Create AI-friendly content summary
inform https://docs.example.com \
  --output-dir ai-content \
  --max-pages 20 \
  --llms-txt

# Creates:
# ai-content/
# ├── [extracted markdown files]
# ├── llms.txt         # Summary for AI consumption
# └── llms-full.txt    # Complete content for AI
```

## Understanding Inform's Output

### File Structure
Inform preserves the original site's URL structure:

```
Original URLs:
├── https://docs.example.com/
├── https://docs.example.com/guide/setup
└── https://docs.example.com/api/auth

Becomes:
├── index.md
├── guide/setup.md  
└── api/auth.md
```

### Markdown Format
Extracted content includes:
```markdown
---
title: "Page Title from HTML"
url: "https://original-url.com/page"
extracted_at: "2024-01-15T10:30:00Z"
---

# Main Heading

Clean content with proper formatting, links, and images preserved.

## Subheadings maintained

- Lists are properly formatted
- **Bold** and *italic* text preserved
- [Links](https://example.com) work correctly
- Images ![alt text](image-url) are included

> Blockquotes and code blocks are maintained
```

## Common Use Cases & Workflows

### 1. Documentation Migration (5 minutes)

Moving from an old documentation platform? Here's the complete workflow:

```bash
# Step 1: Extract all content
inform https://old-docs.company.com \
  --output-dir migrated-content \
  --max-pages 200 \
  --delay 2000 \
  --include-images

# Step 2: Review and organize
ls migrated-content/
# Edit files as needed, organize structure

# Step 3: Generate for new platform
# Use with Unify for static site generation:
npm create unify-site new-docs-site
cp -r migrated-content/* new-docs-site/src/pages/
```

### 2. Competitive Research (3 minutes)

Quickly analyze competitor documentation:

```bash
# Extract competitor docs for analysis
inform https://competitor.com/docs \
  --output-dir research/competitor-docs \
  --max-pages 30 \
  --delay 3000

# Create summary for analysis
inform https://competitor.com/docs \
  --llms-txt \
  --output-dir research/summaries
```

### 3. Content Backup & Archival (2 minutes)

Preserve important web content:

```bash
# Create complete backup
inform https://important-site.com \
  --output-dir backups/important-site-$(date +%Y-%m-%d) \
  --max-pages 100 \
  --include-images \
  --preserve-html
```

### 4. Knowledge Base Creation (7 minutes)

Build a searchable knowledge base:

```bash
# Extract from multiple sources
inform https://docs.tool1.com --output-dir kb/tool1 --max-pages 50
inform https://docs.tool2.com --output-dir kb/tool2 --max-pages 50  
inform https://help.service.com --output-dir kb/service --max-pages 30

# Combine with Inform's AI features
cd kb/
find . -name "*.md" -exec inform --llms-txt {} \;

# Result: Unified knowledge base ready for AI or search
```

## Advanced Configuration Options

### Performance Tuning

```bash
# High-performance extraction
inform https://large-site.com \
  --concurrency 5 \           # 5 parallel requests
  --delay 500 \              # 500ms between requests
  --max-pages 1000 \         # Large site
  --timeout 30               # 30 second timeout per page
```

### Content Filtering

```bash
# Extract only specific content types
inform https://mixed-content-site.com \
  --include-patterns "*/docs/*,*/guides/*" \
  --exclude-patterns "*/blog/*,*/news/*" \
  --content-selector "main, .content, article"
```

### Custom Output Formats

```bash
# Customize output structure
inform https://docs.site.com \
  --output-format "{{title}}.md" \
  --url-prefix "/docs" \
  --add-frontmatter "category: documentation" \
  --preserve-structure false
```

## Integration with Other fwdslsh Tools

### Inform + Unify Workflow

```bash
# 1. Extract content with Inform
inform https://source-docs.com --output-dir content

# 2. Create Unify site
npm create unify-site docs-site
cd docs-site

# 3. Move content to Unify
mv ../content/* src/pages/

# 4. Build with Unify
bun run build

# Result: Modern static site with extracted content
```

### Inform + Giv Workflow

```bash
# 1. Extract and organize content  
inform https://changelog.site.com --output-dir releases

# 2. Generate commit messages with Giv
cd releases/
git init
git add .
giv message
# "docs: Add extracted release documentation"

# 3. Generate changelog
giv changelog > CHANGELOG.md
```

### Inform + Lift Workflow

```bash
# 1. Extract documentation
inform https://docs.example.com --output-dir extracted

# 2. Generate LLMS.txt with Lift
cd extracted/
lift --input . --output ai-ready/

# Result: AI-optimized documentation ready for training or RAG
```

## Configuration File

Create `.inform.yaml` for consistent settings:

```yaml
# .inform.yaml
defaults:
  delay: 1000
  concurrency: 3
  max_pages: 100
  timeout: 30

output:
  format: markdown
  include_frontmatter: true
  preserve_images: true
  base_url: ""

extraction:
  content_selectors:
    - "main"
    - ".content"
    - "article"
    - ".documentation"
  
  exclude_selectors:
    - "nav"
    - ".sidebar"
    - "footer"
    - ".advertisement"

llms:
  generate_summary: true
  max_chunk_size: 4000
  overlap_size: 200
```

Use with:
```bash
inform https://docs.example.com --config .inform.yaml
```

## Best Practices

### 1. Respectful Crawling
- Use appropriate delays (1000ms+ for production sites)
- Respect robots.txt automatically (Inform does this by default)
- Don't overwhelm servers with high concurrency
- Check site terms of service before large extractions

### 2. Content Quality
- Review extracted content for accuracy
- Clean up formatting inconsistencies
- Verify links and images work correctly
- Add proper metadata and frontmatter

### 3. Organization
- Use meaningful output directory structures
- Include extraction date in filenames/metadata
- Document your extraction parameters
- Keep original URLs in frontmatter for reference

## Troubleshooting Common Issues

### "Connection timeout" or "Rate limited"
```bash
# Increase delays and reduce concurrency
inform https://site.com \
  --delay 3000 \
  --concurrency 1 \
  --timeout 60
```

### "No content extracted"
```bash
# Try different content selectors
inform https://site.com \
  --content-selector ".main-content, .post-content, .entry-content"

# Or check if site uses JavaScript rendering
# (Inform extracts static HTML only)
```

### "Too many files created"
```bash
# Limit extraction scope
inform https://site.com \
  --max-pages 50 \
  --include-patterns "*/docs/*" \
  --depth 3
```

## What's Next?

### Learn More About Inform
- [Complete User Guide](user-guide.md) - Master all Inform features
- [Advanced Content Filtering](advanced-filtering.md) - Complex extraction patterns
- [Performance Optimization](performance-optimization.md) - Scale for large sites

### Integration Guides
- [Inform + Static Site Generators](../integrations/inform-static-sites.md) - Hugo, Jekyll, Gatsby
- [Inform + Documentation Workflows](../integrations/inform-doc-workflows.md) - Complete pipelines
- [Inform + AI Tools](../integrations/inform-ai.md) - RAG and training data

### Real Examples
- [Documentation Migration Examples](../examples/inform/migrations/) - Proven migration patterns
- [Content Processing Scripts](../examples/inform/scripts/) - Automation helpers
- [LLMS.txt Generation Examples](../examples/inform/llms/) - AI-ready content

## Getting Help

- 📖 [Full Documentation](user-guide.md)
- 🔧 [CLI Reference](../reference/inform-cli.md)
- 🐛 [Troubleshooting](../troubleshooting/inform.md)
- 💬 [Community Discussions](https://github.com/orgs/fwdslsh/discussions)

**Happy content extraction with Inform!** 🚀

## Quick Reference

### Essential Commands
```bash
# Basic extraction
inform https://site.com

# Batch extraction  
inform https://site.com --max-pages 50 --output-dir content

# With AI summary
inform https://site.com --llms-txt

# Performance tuned
inform https://site.com --delay 2000 --concurrency 2

# Custom selectors
inform https://site.com --content-selector "main, .content"
```

### Useful Flags
- `--delay N` - Milliseconds between requests
- `--max-pages N` - Limit number of pages
- `--output-dir DIR` - Output directory
- `--llms-txt` - Generate AI summaries
- `--include-images` - Download images
- `--concurrency N` - Parallel requests
- `--verbose` - Detailed logging