# Inform User Guide

Inform is a high-performance web crawler that extracts main content from websites and converts it to clean Markdown format. Perfect for documentation migration, content analysis, and building knowledge bases.

## What is Inform?

Inform intelligently crawls websites, extracts meaningful content (while filtering out navigation, ads, and other noise), and saves it as well-structured Markdown files. It's particularly useful for:

- **Documentation Migration**: Moving docs from one platform to another
- **Content Archival**: Creating offline copies of important content
- **Knowledge Base Creation**: Building searchable documentation repositories
- **LLMS.txt Generation**: Creating AI-friendly content summaries

## Installation

### Option 1: Pre-built Binaries (Recommended)

Download the latest binary from [GitHub Releases](https://github.com/fwdslsh/inform/releases):

```bash
# Linux/macOS
curl -fsSL https://github.com/fwdslsh/inform/releases/latest/download/inform-linux | sudo tee /usr/local/bin/inform > /dev/null
sudo chmod +x /usr/local/bin/inform

# Windows (PowerShell)
Invoke-WebRequest -Uri "https://github.com/fwdslsh/inform/releases/latest/download/inform-windows.exe" -OutFile "inform.exe"
```

### Option 2: Using Bun

```bash
# Install Bun first
curl -fsSL https://bun.sh/install | bash

# Install Inform
bun add -g @fwdslsh/inform

# Verify installation
inform --version
```

### Option 3: Docker

```bash
# Pull the image
docker pull fwdslsh/inform:latest

# Run Inform
docker run --rm -v $(pwd):/workspace fwdslsh/inform https://example.com
```

## Basic Usage

### Simple Website Crawling

```bash
# Crawl a website with default settings
inform https://docs.example.com

# This will:
# - Crawl up to 100 pages
# - Wait 1 second between requests
# - Use 3 concurrent connections
# - Save files to ./crawled-pages/
```

### Customizing the Crawl

```bash
# Crawl with custom settings
inform https://docs.example.com \
  --max-pages 50 \
  --delay 500 \
  --concurrency 5 \
  --output-dir ./documentation

# Crawl specific sections only
inform https://docs.example.com/api \
  --include "*/api/*" \
  --exclude "*/internal/*"
```

## Command Line Options

### Basic Options

- `--max-pages <number>`: Maximum pages to crawl (default: 100)
- `--delay <ms>`: Delay between requests in milliseconds (default: 1000)
- `--concurrency <number>`: Number of concurrent requests (default: 3)
- `--output-dir <path>`: Output directory (default: crawled-pages)

### Filtering Options

- `--include <pattern>`: Include URLs matching pattern (can be used multiple times)
- `--exclude <pattern>`: Exclude URLs matching pattern (can be used multiple times)

### Special Features

- `--llms`: Download LLMS.txt files and generate AI-friendly summaries
- `--help`: Show all available options

## Content Extraction

### How Inform Finds Content

Inform uses intelligent content detection to find the main content on each page:

1. **Primary selectors**: `<main>`, `[role="main"]`
2. **Content containers**: `.main-content`, `.content`, `.post-content`
3. **Article elements**: `<article>`
4. **Bootstrap patterns**: `.container .row .col-*`
5. **Fallback**: `<body>` content with noise removed

### What Gets Removed

Inform automatically filters out:
- Navigation elements (`nav`, `.menu`, `.navigation`)
- Headers and footers
- Advertisements (`.ad`, `.advertisement`, `.ads`)
- Social sharing buttons
- Comment sections
- Scripts and styles
- Sidebar content (when not main content)

### Content Processing

After extraction, Inform:
- Converts HTML to clean Markdown
- Preserves code blocks with proper syntax highlighting
- Maintains table structure
- Converts images to proper Markdown image syntax
- Preserves links with proper formatting

## Advanced Usage

### Pattern Matching

Use glob patterns to control what gets crawled:

```bash
# Only crawl documentation pages
inform https://example.com \
  --include "*/docs/*" \
  --include "*/guide/*" \
  --exclude "*/api/internal/*"

# Crawl multiple patterns
inform https://example.com \
  --include "*/tutorial/*" \
  --include "*/howto/*" \
  --include "*/faq/*"
```

### LLMS.txt Support

LLMS.txt is a standard for providing AI-friendly content summaries:

```bash
# Download existing LLMS.txt files
inform https://example.com/llms.txt

# Probe common LLMS.txt locations
inform https://example.com --llms

# Generate LLMS.txt from crawled content
inform https://docs.example.com --llms --max-pages 100
```

This creates:
- `llms.txt`: Concise summary for quick AI consumption
- `llms-full.txt`: Complete content for comprehensive AI training

### Rate Limiting and Ethics

Always be respectful when crawling:

```bash
# Conservative crawling (good for small sites)
inform https://example.com \
  --delay 2000 \
  --concurrency 1 \
  --max-pages 25

# Aggressive crawling (only for sites that can handle it)
inform https://large-docs-site.com \
  --delay 200 \
  --concurrency 10 \
  --max-pages 500
```

### Large Site Strategies

For large sites, use a multi-pass approach:

```bash
# Pass 1: Crawl main documentation
inform https://docs.example.com \
  --include "*/docs/*" \
  --output-dir ./docs-content \
  --max-pages 200

# Pass 2: Crawl API reference
inform https://docs.example.com \
  --include "*/api/*" \
  --output-dir ./api-content \
  --max-pages 100

# Pass 3: Crawl tutorials
inform https://docs.example.com \
  --include "*/tutorial/*" \
  --output-dir ./tutorial-content \
  --max-pages 50
```

## Output Structure

### File Organization

Inform maintains the original site structure:

```
crawled-pages/
├── index.md                    # Root page
├── docs/
│   ├── getting-started.md     # /docs/getting-started
│   ├── api/
│   │   ├── index.md           # /docs/api/
│   │   └── authentication.md # /docs/api/authentication
│   └── guides/
│       └── deployment.md     # /docs/guides/deployment
└── blog/
    ├── 2024/
    │   └── january/
    │       └── new-features.md
    └── index.md
```

### Markdown Format

Generated Markdown includes:

```markdown
---
url: https://example.com/docs/getting-started
title: Getting Started Guide
date: 2024-01-15
---

# Getting Started Guide

This is the main content extracted from the page...

## Code Examples

```javascript
console.log('Code blocks are preserved with syntax highlighting');
```

## Tables

| Feature | Supported |
|---------|-----------|
| Tables  | ✅        |
| Lists   | ✅        |
```

## Integration Workflows

### With Static Site Generators

#### Hugo Integration

```bash
# Crawl content
inform https://old-docs.com --output-dir content/docs

# Create Hugo front matter script
cat > process-content.sh << 'EOF'
#!/bin/bash
for file in content/docs/**/*.md; do
  # Add Hugo frontmatter
  sed -i '1i---\nlayout: doc\n---\n' "$file"
done
EOF

chmod +x process-content.sh
./process-content.sh
```

#### Jekyll Integration

```bash
# Crawl with Jekyll-friendly structure
inform https://docs.example.com --output-dir _posts

# Process files for Jekyll
find _posts -name "*.md" -exec sed -i 's/^---$/---\nlayout: post\n---/' {} \;
```

### With Documentation Tools

#### GitBook Migration

```bash
# Crawl GitBook site
inform https://your-org.gitbook.io/docs \
  --output-dir ./gitbook-content \
  --max-pages 200

# Convert to GitBook structure
mkdir docs
cp -r gitbook-content/* docs/
echo "# Summary\n" > docs/SUMMARY.md
find docs -name "*.md" | sort | sed 's/^docs\///' | sed 's/\.md$//' | sed 's/^/* [/' | sed 's/$/.md)/' >> docs/SUMMARY.md
```

#### Notion Export Enhancement

```bash
# After Notion export, enhance with web content
inform https://public-notion-site.com \
  --output-dir ./notion-enhanced \
  --include "*/page/*"

# Merge with existing Notion export
cp -r notion-export/* notion-enhanced/
```

### With Content Management

#### Building Knowledge Bases

```bash
# Crawl multiple sources
inform https://docs.competitor1.com --output-dir ./research/competitor1
inform https://docs.competitor2.com --output-dir ./research/competitor2
inform https://industry-blog.com --output-dir ./research/blog

# Generate combined LLMS.txt
cat research/*/*.md > combined-research.txt
inform --llms combined-research.txt
```

## Performance Optimization

### Maximizing Speed

```bash
# For fast, powerful servers
inform https://example.com \
  --concurrency 10 \
  --delay 100 \
  --max-pages 1000

# Monitor progress
inform https://example.com \
  --concurrency 5 \
  --delay 300 \
  --max-pages 200 \
  --verbose
```

### Minimizing Server Load

```bash
# Conservative approach
inform https://small-site.com \
  --concurrency 1 \
  --delay 3000 \
  --max-pages 50

# Spread crawling over time
for section in docs api guides; do
  inform https://example.com/$section \
    --output-dir ./$section \
    --delay 2000 \
    --max-pages 25
  sleep 300  # Wait 5 minutes between sections
done
```

## Best Practices

### Ethical Crawling

1. **Check robots.txt**: Always respect robots.txt directives
2. **Use reasonable delays**: Don't overwhelm servers
3. **Respect rate limits**: Monitor for 429 responses
4. **Check terms of service**: Ensure crawling is permitted
5. **Contact site owners**: For large crawls, consider reaching out

### Quality Content Extraction

1. **Test on small samples first**: Start with `--max-pages 5`
2. **Review extraction quality**: Check a few files manually
3. **Adjust patterns**: Use --include/--exclude to improve targeting
4. **Handle special cases**: Some sites may need custom handling

### Organizing Output

1. **Use descriptive output directories**:
   ```bash
   inform https://docs.example.com --output-dir ./example-docs-$(date +%Y%m%d)
   ```

2. **Separate different content types**:
   ```bash
   inform https://example.com --include "*/api/*" --output-dir ./api-docs
   inform https://example.com --include "*/guides/*" --output-dir ./guides
   ```

3. **Archive crawls**:
   ```bash
   tar -czf "crawl-$(date +%Y%m%d).tar.gz" crawled-pages/
   ```

## Troubleshooting

### Common Issues

**No content extracted**
```bash
# Check if the site uses JavaScript rendering
# Try crawling a specific page first
inform https://example.com/specific-page --max-pages 1
```

**Rate limiting errors**
```bash
# Increase delays and reduce concurrency
inform https://example.com --delay 5000 --concurrency 1
```

**Missing pages**
```bash
# Check URL patterns
inform https://example.com --include "*" --exclude "*/internal/*" --verbose
```

**Large files or binary content**
Inform automatically skips binary files, but if you encounter issues:
```bash
# Add specific exclusions
inform https://example.com --exclude "*.pdf" --exclude "*/downloads/*"
```

### Getting Help

1. **Check the logs**: Inform provides detailed output about what it's crawling
2. **Test with small samples**: Use `--max-pages 5` for testing
3. **Review extraction**: Check a few output files manually
4. **Community support**: Join the [fwdslsh discussions](https://github.com/orgs/fwdslsh/discussions)

## Advanced Examples

### Documentation Site Migration

Complete example of migrating a documentation site:

```bash
#!/bin/bash
# migrate-docs.sh

# Step 1: Crawl existing documentation
echo "Crawling existing documentation..."
inform https://old-docs.example.com \
  --include "*/docs/*" \
  --include "*/guide/*" \
  --exclude "*/internal/*" \
  --output-dir ./migrated-docs \
  --max-pages 150 \
  --delay 800

# Step 2: Download LLMS.txt for AI assistance
echo "Generating LLMS.txt..."
inform https://old-docs.example.com --llms --output-dir ./migrated-docs

# Step 3: Organize content
echo "Organizing content..."
mkdir -p ./new-site/content
cp -r migrated-docs/* ./new-site/content/

# Step 4: Generate index
echo "Generating content index..."
find ./new-site/content -name "*.md" | sort > content-index.txt

echo "Migration complete! Content saved to ./new-site/content/"
echo "Review the files and run your static site generator."
```

### Research and Analysis

Gathering competitive intelligence:

```bash
#!/bin/bash
# research-competitors.sh

competitors=(
  "https://competitor1.com/docs"
  "https://competitor2.com/help"
  "https://competitor3.com/guides"
)

for url in "${competitors[@]}"; do
  domain=$(echo $url | sed 's/https:\/\///' | sed 's/\/.*$//')
  echo "Crawling $domain..."
  
  inform "$url" \
    --output-dir "./research/$domain" \
    --max-pages 50 \
    --delay 1500 \
    --llms
    
  echo "Completed $domain"
  sleep 60  # Pause between competitors
done

echo "Research complete! Check ./research/ directory"
```

## Next Steps

- Explore [Integration Examples](../examples/inform/) for specific use cases
- Read about [Documentation Workflows](../best-practices/documentation-workflows.md)
- Check out [Troubleshooting Guide](../troubleshooting/inform.md) for common issues
- Join the [community](https://github.com/orgs/fwdslsh/discussions) to share experiences

Happy crawling! 🕷️