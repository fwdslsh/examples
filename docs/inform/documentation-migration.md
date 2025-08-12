# Using Inform for Documentation Migration

Complete guide to migrating documentation from legacy platforms to modern static site generators using Inform's powerful content extraction and conversion capabilities.

## Migration Planning

### Source Platform Assessment

**Supported Source Platforms:**
- **Wiki Systems**: MediaWiki, Confluence, Notion, GitBook
- **Legacy CMSs**: WordPress, Drupal, Joomla
- **Documentation Platforms**: GitBook, Bookstack, TiddlyWiki
- **Static Sites**: Jekyll, Hugo, Gatsby (for restructuring)
- **Custom Platforms**: Any HTML-based documentation system

**Assessment Checklist:**
```markdown
## Platform Analysis

### Content Inventory
- [ ] Total page count: _____
- [ ] Content types: Articles, tutorials, API docs, FAQs
- [ ] Media assets: Images, videos, downloads
- [ ] Internal linking structure
- [ ] Navigation hierarchy
- [ ] Search functionality requirements

### Technical Assessment  
- [ ] Authentication requirements
- [ ] Rate limiting constraints
- [ ] API access availability
- [ ] Export functionality
- [ ] Content format (HTML, Markdown, etc.)
- [ ] Asset hosting method
```

### Content Inventory and Mapping

**Inventory Process:**
```bash
# Create content inventory with Inform
inform https://old-docs.company.com \
  --output-dir inventory \
  --max-pages 1000 \
  --generate-sitemap \
  --extract-metadata \
  --inventory-mode

# Generate content map
ls inventory/ > content-inventory.txt
find inventory/ -name "*.md" -exec wc -l {} \; > word-counts.txt
```

**Content Mapping Template:**
```markdown
# Content Migration Map

## High-Priority Content (Migrate First)
- [ ] Getting Started Guide: `/docs/getting-started` → `/getting-started/`
- [ ] API Documentation: `/docs/api/*` → `/api/`
- [ ] Troubleshooting: `/docs/troubleshooting` → `/help/troubleshooting/`

## Medium-Priority Content (Migrate Second)
- [ ] Tutorials: `/docs/tutorials/*` → `/tutorials/`
- [ ] Integration Guides: `/docs/integrations/*` → `/integrations/`
- [ ] Examples: `/docs/examples/*` → `/examples/`

## Low-Priority Content (Migrate Last)
- [ ] Archive Content: `/docs/archive/*` → `/archive/`
- [ ] Legacy Versions: `/docs/v1/*` → `/legacy/v1/`
- [ ] Draft Content: `/docs/drafts/*` → Review and decide

## Content to Exclude
- [ ] Spam/test pages
- [ ] Duplicate content
- [ ] Severely outdated content
- [ ] Internal-only content
```

### Migration Timeline and Phases

**Phase-Based Migration Strategy:**

**Phase 1: Foundation (Week 1-2)**
- Extract core documentation (Getting Started, API docs)
- Set up target platform infrastructure
- Establish content review process
- Test migration pipeline

**Phase 2: Bulk Migration (Week 3-6)**
- Extract remaining high and medium priority content
- Process and clean up content
- Implement redirects and URL mapping
- Bulk import to target platform

**Phase 3: Optimization (Week 7-8)**
- Fix broken links and formatting issues
- Optimize images and media assets
- Implement search functionality
- User acceptance testing

**Phase 4: Go-Live (Week 9-10)**
- Final content review and approval
- DNS and redirect implementation
- Launch new documentation site
- Monitor and respond to user feedback

## Inform Configuration for Migration

### Site-Specific Crawling Strategies

**Confluence Migration Configuration:**
```yaml
# .inform-confluence.yaml
extraction:
  # Confluence-specific selectors
  content_selectors:
    - ".wiki-content"
    - ".confluence-content" 
    - "#main-content"
  
  exclude_selectors:
    - ".confluence-sidebar"
    - ".page-metadata"
    - ".like-button"
    - ".comment-section"
    - ".confluence-footer"
  
  # Confluence URL patterns
  url_patterns:
    include:
      - "*/display/*"
      - "*/spaces/*"
    exclude:
      - "*/admin/*"
      - "*/people/*"
      - "*/labels/*"

crawling:
  delay: 2000              # Respectful crawling
  max_pages: 500           # Limit scope initially
  follow_redirects: true
  respect_robots: true
  
output:
  format: "markdown"
  preserve_hierarchy: true
  clean_html: true
  extract_frontmatter: true
```

**WordPress Migration Configuration:**
```yaml
# .inform-wordpress.yaml
extraction:
  # WordPress-specific selectors
  content_selectors:
    - ".entry-content"
    - ".post-content"
    - "article .content"
  
  exclude_selectors:
    - ".sidebar"
    - ".comments"
    - ".related-posts"
    - ".social-share"
    - ".navigation"
  
  # WordPress URL patterns  
  url_patterns:
    include:
      - "*/docs/*"
      - "*/help/*"
      - "*/guide/*"
    exclude:
      - "*/wp-admin/*"
      - "*/wp-content/plugins/*"
      - "*/?author=*"

processing:
  remove_wordpress_shortcodes: true
  convert_galleries: true
  extract_featured_images: true
  clean_wordpress_artifacts: true
```

### Content Extraction Rules

**Advanced Content Extraction:**
```javascript
// Custom extraction rules for complex sites
const extractionRules = {
  // Multi-tab content extraction
  tabs: {
    selector: '.tab-content',
    process: (element) => {
      const tabs = element.querySelectorAll('.tab-pane');
      return Array.from(tabs).map(tab => ({
        title: tab.getAttribute('data-title'),
        content: tab.innerHTML
      }));
    }
  },
  
  // Code example extraction
  codeExamples: {
    selector: '.code-example',
    process: (element) => {
      const language = element.getAttribute('data-language') || 'text';
      const code = element.querySelector('code').textContent;
      return `\`\`\`${language}\n${code}\n\`\`\``;
    }
  },
  
  // API documentation extraction
  apiDocs: {
    selector: '.api-endpoint',
    process: (element) => {
      const method = element.querySelector('.method').textContent;
      const url = element.querySelector('.url').textContent;
      const description = element.querySelector('.description').textContent;
      
      return {
        method,
        url,
        description,
        markdown: `## ${method} ${url}\n\n${description}`
      };
    }
  }
};
```

**Quality Validation Filters:**
```yaml
# .inform-quality.yaml
quality:
  min_content_length: 100    # Skip pages with <100 chars
  max_content_length: 50000  # Split very long pages
  
  required_elements:
    - title                  # Must have title
    - content               # Must have main content
  
  content_validation:
    # Skip pages that are mostly navigation
    max_nav_percentage: 0.7
    
    # Skip pages with too many external links
    max_external_links: 50
    
    # Require minimum text content
    min_text_ratio: 0.3
    
  exclusion_patterns:
    # Skip obvious non-content pages
    - "404"
    - "search"
    - "login"
    - "admin"
    - "test"
    
    # Skip generated pages
    - "sitemap"
    - "feed"
    - "rss"
```

## Content Processing Pipeline

### HTML to Markdown Conversion

**Advanced Markdown Conversion:**
```javascript
// Custom Turndown configuration for documentation
const turndownService = new TurndownService({
  headingStyle: 'atx',           // Use # headings
  codeBlockStyle: 'fenced',      // Use ``` code blocks
  fence: '```',                  // Code fence style
  emDelimiter: '_',              // Use _ for emphasis
  strongDelimiter: '**',        // Use ** for strong
  linkStyle: 'inlined',          // Inline links
  linkReferenceStyle: 'full'     // Full reference links
});

// Custom rules for documentation-specific elements
turndownService.addRule('admonitions', {
  filter: ['.note', '.warning', '.tip', '.important'],
  replacement: (content, node) => {
    const type = node.className.includes('warning') ? '⚠️ Warning' :
                node.className.includes('tip') ? '💡 Tip' :
                node.className.includes('important') ? '❗ Important' :
                'ℹ️ Note';
    
    return `\n> **${type}**: ${content}\n\n`;
  }
});

// Code example preservation
turndownService.addRule('codeExamples', {
  filter: '.code-example',
  replacement: (content, node) => {
    const language = node.getAttribute('data-language') || '';
    const title = node.getAttribute('data-title');
    
    const titleLine = title ? `// ${title}\n` : '';
    return `\n\`\`\`${language}\n${titleLine}${content}\n\`\`\`\n\n`;
  }
});
```

**Frontmatter Extraction:**
```javascript
// Extract metadata for frontmatter
function extractFrontmatter(document, url) {
  const meta = {};
  
  // Basic metadata
  meta.title = document.querySelector('title')?.textContent || 
               document.querySelector('h1')?.textContent || 'Untitled';
  
  // SEO metadata
  meta.description = document.querySelector('meta[name="description"]')?.content;
  meta.keywords = document.querySelector('meta[name="keywords"]')?.content;
  
  // Documentation-specific metadata
  meta.category = extractCategory(url);
  meta.tags = extractTags(document);
  meta.last_updated = extractLastUpdated(document);
  meta.author = extractAuthor(document);
  meta.difficulty = extractDifficulty(document);
  
  // Migration metadata
  meta.original_url = url;
  meta.migrated_date = new Date().toISOString();
  meta.migration_notes = [];
  
  return meta;
}

function extractCategory(url) {
  // Extract category from URL structure
  const pathParts = new URL(url).pathname.split('/');
  return pathParts[2] || 'general'; // Assuming /docs/category/page structure
}

function extractTags(document) {
  // Extract tags from various sources
  const tags = new Set();
  
  // From meta tags
  document.querySelectorAll('meta[name="article:tag"]')
    .forEach(tag => tags.add(tag.content));
  
  // From class names (common in documentation)
  document.querySelectorAll('.tag, .label, .badge')
    .forEach(el => tags.add(el.textContent.trim()));
  
  return Array.from(tags);
}
```

### Structure Preservation Techniques

**Hierarchy Preservation:**
```javascript
// Preserve document hierarchy during migration
class HierarchyPreserver {
  constructor(basePath) {
    this.basePath = basePath;
    this.urlMap = new Map();
  }
  
  preserveStructure(pages) {
    // Build URL hierarchy
    const hierarchy = this.buildHierarchy(pages);
    
    // Generate consistent file paths
    return this.generateFilePaths(hierarchy);
  }
  
  buildHierarchy(pages) {
    const tree = {};
    
    pages.forEach(page => {
      const urlPath = new URL(page.url).pathname;
      const pathParts = urlPath.split('/').filter(part => part);
      
      let current = tree;
      pathParts.forEach(part => {
        if (!current[part]) {
          current[part] = { children: {}, page: null };
        }
        current = current[part].children;
      });
      
      // Store page at leaf node
      current._page = page;
    });
    
    return tree;
  }
  
  generateFilePaths(hierarchy, basePath = '') {
    const filePaths = [];
    
    Object.entries(hierarchy).forEach(([segment, node]) => {
      const currentPath = basePath ? `${basePath}/${segment}` : segment;
      
      if (node.page) {
        // Generate file path for page
        const fileName = this.generateFileName(node.page);
        filePaths.push({
          originalUrl: node.page.url,
          filePath: `${currentPath}/${fileName}`,
          page: node.page
        });
      }
      
      // Recursively process children
      if (node.children && Object.keys(node.children).length > 0) {
        filePaths.push(...this.generateFilePaths(node.children, currentPath));
      }
    });
    
    return filePaths;
  }
  
  generateFileName(page) {
    // Generate clean file names
    let name = page.title
      .toLowerCase()
      .replace(/[^a-z0-9\s-]/g, '')  // Remove special chars
      .replace(/\s+/g, '-')          // Replace spaces with dashes
      .replace(/-+/g, '-')           // Remove multiple dashes
      .replace(/^-|-$/g, '');        // Remove leading/trailing dashes
    
    // Ensure unique names
    const extension = page.format === 'markdown' ? '.md' : '.html';
    return `${name}${extension}`;
  }
}
```

### Asset Migration Handling

**Image and Media Processing:**
```bash
#!/bin/bash
# migrate-assets.sh

# Create asset directories
mkdir -p migrated-content/{images,downloads,videos}

# Process extracted content for assets
echo "Processing assets from migrated content..."

# Find all image references
grep -r "!\[.*\](" migrated-content/ | while IFS=: read file line; do
  # Extract image URLs
  image_urls=$(echo "$line" | grep -o 'https\?://[^)]*\.(png\|jpg\|jpeg\|gif\|svg\|webp)')
  
  for url in $image_urls; do
    # Download image
    filename=$(basename "$url")
    if curl -s "$url" -o "migrated-content/images/$filename"; then
      # Update reference in markdown
      sed -i "s|$url|/images/$filename|g" "$file"
      echo "Downloaded: $filename"
    else
      echo "Failed to download: $url"
    fi
  done
done

# Process downloadable assets
grep -r "href.*\.(pdf\|zip\|doc\|docx\|xls\|xlsx)" migrated-content/ | while IFS=: read file line; do
  # Extract download URLs
  download_urls=$(echo "$line" | grep -o 'https\?://[^"]*\.(pdf\|zip\|doc\|docx\|xls\|xlsx)')
  
  for url in $download_urls; do
    filename=$(basename "$url")
    if curl -s "$url" -o "migrated-content/downloads/$filename"; then
      # Update reference in markdown
      sed -i "s|$url|/downloads/$filename|g" "$file"
      echo "Downloaded: $filename"
    fi
  done
done

echo "Asset migration completed."
```

**Asset Optimization:**
```javascript
// Optimize images during migration
const sharp = require('sharp');
const path = require('path');

async function optimizeAssets(assetsDir) {
  const imageFiles = await glob(`${assetsDir}/**/*.{jpg,jpeg,png}`);
  
  for (const imagePath of imageFiles) {
    const fileName = path.basename(imagePath, path.extname(imagePath));
    const dir = path.dirname(imagePath);
    
    try {
      // Create optimized versions
      await sharp(imagePath)
        .resize(1200, 1200, { fit: 'inside', withoutEnlargement: true })
        .jpeg({ quality: 85 })
        .toFile(`${dir}/${fileName}_optimized.jpg`);
      
      // Create thumbnail
      await sharp(imagePath)
        .resize(300, 300, { fit: 'cover' })
        .jpeg({ quality: 75 })
        .toFile(`${dir}/${fileName}_thumb.jpg`);
        
      console.log(`Optimized: ${fileName}`);
    } catch (error) {
      console.error(`Failed to optimize ${fileName}:`, error);
    }
  }
}
```

## Quality Assurance

### Content Validation Workflows

**Automated Quality Checks:**
```bash
#!/bin/bash
# validate-migration.sh

echo "Running migration quality checks..."

# Check for broken internal links
echo "1. Checking for broken internal links..."
find migrated-content/ -name "*.md" -exec grep -l '\[.*\](.*\.html)' {} \; > broken-links.txt
if [ -s broken-links.txt ]; then
  echo "⚠️  Files with potential broken links found:"
  cat broken-links.txt
else
  echo "✅ No broken internal links found"
fi

# Check for missing images
echo "2. Checking for missing images..."
find migrated-content/ -name "*.md" -exec grep -H '!\[.*\](.*/)' {} \; | while IFS=: read file image_ref; do
  image_path=$(echo "$image_ref" | grep -o '(/[^)]*)' | tr -d '()')
  if [ ! -f "migrated-content$image_path" ]; then
    echo "Missing image: $image_path in $file"
  fi
done

# Check for malformed frontmatter
echo "3. Checking frontmatter..."
find migrated-content/ -name "*.md" | while read file; do
  if ! head -n 1 "$file" | grep -q '^---$'; then
    echo "⚠️  Malformed frontmatter in: $file"
  fi
done

# Check content length
echo "4. Checking content length..."
find migrated-content/ -name "*.md" | while read file; do
  word_count=$(wc -w < "$file")
  if [ "$word_count" -lt 50 ]; then
    echo "⚠️  Very short content in: $file ($word_count words)"
  fi
done

# Check for HTML artifacts
echo "5. Checking for HTML artifacts..."
grep -r '<[^>]*>' migrated-content/ | head -10 | while IFS=: read file line; do
  echo "HTML found in: $file"
done

echo "Quality check completed."
```

**Content Review Checklist:**
```markdown
# Content Review Checklist

## Page-Level Review
- [ ] Title is descriptive and accurate
- [ ] Content is complete and readable
- [ ] All images display correctly
- [ ] Internal links work properly
- [ ] Code examples are formatted correctly
- [ ] Tables render properly
- [ ] Lists and formatting are preserved

## Technical Review  
- [ ] Frontmatter is complete and valid
- [ ] File path follows naming conventions
- [ ] No HTML artifacts in markdown
- [ ] Proper heading hierarchy (H1 → H2 → H3)
- [ ] Code blocks have appropriate language tags

## SEO and Metadata
- [ ] Meta description is present and under 160 chars
- [ ] Keywords are relevant and not over-optimized
- [ ] URL structure is clean and logical
- [ ] Page is categorized correctly

## Accessibility
- [ ] Images have alt text
- [ ] Links have descriptive text (not "click here")
- [ ] Proper heading structure for screen readers
- [ ] Color contrast is sufficient
```

### Broken Link Detection

**Comprehensive Link Validation:**
```javascript
// link-validator.js
const fs = require('fs').promises;
const path = require('path');
const glob = require('glob');

class LinkValidator {
  constructor(contentDir) {
    this.contentDir = contentDir;
    this.internalLinks = new Map();
    this.externalLinks = new Map();
    this.brokenLinks = [];
  }
  
  async validateAllLinks() {
    // Find all markdown files
    const files = await glob(`${this.contentDir}/**/*.md`);
    
    // Extract all links
    for (const file of files) {
      await this.extractLinks(file);
    }
    
    // Validate internal links
    await this.validateInternalLinks();
    
    // Validate external links (sample)
    await this.validateExternalLinks();
    
    return this.generateReport();
  }
  
  async extractLinks(filePath) {
    const content = await fs.readFile(filePath, 'utf-8');
    const linkRegex = /\[([^\]]*)\]\(([^)]+)\)/g;
    
    let match;
    while ((match = linkRegex.exec(content)) !== null) {
      const [, text, url] = match;
      
      if (url.startsWith('http')) {
        // External link
        if (!this.externalLinks.has(url)) {
          this.externalLinks.set(url, []);
        }
        this.externalLinks.get(url).push({ file: filePath, text });
      } else {
        // Internal link
        if (!this.internalLinks.has(url)) {
          this.internalLinks.set(url, []);
        }
        this.internalLinks.get(url).push({ file: filePath, text });
      }
    }
  }
  
  async validateInternalLinks() {
    for (const [url, references] of this.internalLinks) {
      // Convert relative URL to file path
      const targetPath = this.resolveInternalPath(url);
      
      try {
        await fs.access(targetPath);
      } catch (error) {
        this.brokenLinks.push({
          url,
          type: 'internal',
          references,
          error: 'File not found'
        });
      }
    }
  }
  
  async validateExternalLinks() {
    // Sample external links to avoid overwhelming external servers
    const externalUrls = Array.from(this.externalLinks.keys());
    const sampleSize = Math.min(50, externalUrls.length);
    const sample = externalUrls.slice(0, sampleSize);
    
    for (const url of sample) {
      try {
        const response = await fetch(url, { method: 'HEAD' });
        if (!response.ok) {
          this.brokenLinks.push({
            url,
            type: 'external',
            references: this.externalLinks.get(url),
            error: `HTTP ${response.status}`
          });
        }
      } catch (error) {
        this.brokenLinks.push({
          url,
          type: 'external',
          references: this.externalLinks.get(url),
          error: error.message
        });
      }
    }
  }
  
  resolveInternalPath(url) {
    // Handle various internal link formats
    if (url.startsWith('/')) {
      return path.join(this.contentDir, url.substring(1));
    } else if (url.endsWith('.md')) {
      return path.join(this.contentDir, url);
    } else if (url.endsWith('/')) {
      return path.join(this.contentDir, url, 'index.md');
    } else {
      return path.join(this.contentDir, `${url}.md`);
    }
  }
  
  generateReport() {
    const report = {
      summary: {
        totalInternalLinks: this.internalLinks.size,
        totalExternalLinks: this.externalLinks.size,
        brokenLinks: this.brokenLinks.length,
        validationDate: new Date().toISOString()
      },
      brokenLinks: this.brokenLinks,
      recommendations: this.generateRecommendations()
    };
    
    return report;
  }
  
  generateRecommendations() {
    const recommendations = [];
    
    if (this.brokenLinks.length > 0) {
      recommendations.push('Fix broken links before publishing');
    }
    
    const highFrequencyBroken = this.brokenLinks
      .filter(link => link.references.length > 3);
    
    if (highFrequencyBroken.length > 0) {
      recommendations.push('Priority: Fix high-frequency broken links first');
    }
    
    return recommendations;
  }
}

// Usage
async function validateMigration() {
  const validator = new LinkValidator('./migrated-content');
  const report = await validator.validateAllLinks();
  
  await fs.writeFile(
    'link-validation-report.json',
    JSON.stringify(report, null, 2)
  );
  
  console.log(`Link validation complete. ${report.summary.brokenLinks} broken links found.`);
}

validateMigration().catch(console.error);
```

### Format Consistency Checks

**Markdown Consistency Validation:**
```bash
#!/bin/bash
# format-consistency.sh

echo "Checking markdown format consistency..."

# Check heading consistency
echo "1. Heading hierarchy check..."
find migrated-content/ -name "*.md" | while read file; do
  # Check if H1 comes before H2, H2 before H3, etc.
  awk '
    /^# / { if (h1_found) print FILENAME ": Multiple H1 headers"; h1_found=1; last_level=1 }
    /^## / { if (!h1_found) print FILENAME ": H2 without H1"; if (last_level > 2) print FILENAME ": Heading level skip"; last_level=2 }
    /^### / { if (last_level > 3) print FILENAME ": Heading level skip"; last_level=3 }
    /^#### / { if (last_level > 4) print FILENAME ": Heading level skip"; last_level=4 }
  ' "$file"
done

# Check frontmatter consistency
echo "2. Frontmatter consistency..."
find migrated-content/ -name "*.md" | while read file; do
  # Check required frontmatter fields
  if ! grep -q "^title:" "$file"; then
    echo "Missing title in: $file"
  fi
  if ! grep -q "^date:" "$file"; then
    echo "Missing date in: $file"
  fi
  if ! grep -q "^category:" "$file"; then
    echo "Missing category in: $file"
  fi
done

# Check code block consistency
echo "3. Code block format check..."
grep -r '```' migrated-content/ | cut -d: -f2 | sort | uniq -c | sort -nr | while read count format; do
  if [[ $format =~ ^[[:space:]]*\`\`\`[[:space:]]*$ ]]; then
    echo "⚠️  $count code blocks without language specification"
  fi
done

# Check link format consistency
echo "4. Link format check..."
find migrated-content/ -name "*.md" -exec grep -H '\[.*\]([^)]*\.html)' {} \; | while IFS=: read file link; do
  echo "HTML link found in: $file - $link"
done

echo "Format consistency check completed."
```

## Integration with Target Platforms

### Unify Integration Patterns

**Complete Unify Migration Setup:**
```bash
#!/bin/bash
# migrate-to-unify.sh

echo "Setting up Unify integration for migrated content..."

# Create Unify project structure
npm create unify-site documentation-site
cd documentation-site

# Copy migrated content to Unify structure
echo "Organizing content for Unify..."
mkdir -p src/pages/{api,guides,tutorials,reference}

# Copy and reorganize content
cp -r ../migrated-content/api/* src/pages/api/
cp -r ../migrated-content/guides/* src/pages/guides/
cp -r ../migrated-content/tutorials/* src/pages/tutorials/
cp -r ../migrated-content/reference/* src/pages/reference/

# Create Unify-compatible layouts
mkdir -p src/.components

cat > src/.components/doc-layout.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{ title }} - Documentation</title>
  <meta name="description" content="{{ description }}">
  <link rel="stylesheet" href="/styles/documentation.css">
</head>
<body>
  <!-- @include .components/header.html -->
  
  <main class="documentation">
    <nav class="sidebar">
      <!-- @include .components/navigation.html -->
    </nav>
    
    <article class="content">
      <header class="page-header">
        <h1>{{ title }}</h1>
        {% if description %}
        <p class="page-description">{{ description }}</p>
        {% endif %}
        
        {% if last_updated %}
        <p class="last-updated">Last updated: {{ last_updated }}</p>
        {% endif %}
      </header>
      
      <div class="page-content">
        <!-- @slot -->
        {{ content }}
        <!-- @endslot -->
      </div>
      
      <footer class="page-footer">
        <!-- @include .components/page-footer.html -->
      </footer>
    </article>
  </main>
  
  <!-- @include .components/scripts.html -->
</body>
</html>
EOF

# Convert markdown files to use Unify layout
find src/pages/ -name "*.md" | while read file; do
  # Add layout specification to frontmatter
  sed -i '1s/^---$/---\nlayout: doc-layout/' "$file"
done

# Build documentation site
bun run build

echo "Unify integration completed. Site built in dist/"
```

**Dynamic Navigation Generation:**
```javascript
// generate-navigation.js - Create navigation from migrated content
const fs = require('fs').promises;
const path = require('path');
const matter = require('gray-matter');

async function generateNavigation(contentDir) {
  const navigation = {
    sections: []
  };
  
  // Read all markdown files
  const files = await glob(`${contentDir}/**/*.md`);
  
  // Group by category
  const categories = new Map();
  
  for (const file of files) {
    const content = await fs.readFile(file, 'utf-8');
    const { data: frontmatter } = matter(content);
    
    const category = frontmatter.category || 'General';
    if (!categories.has(category)) {
      categories.set(category, []);
    }
    
    categories.get(category).push({
      title: frontmatter.title,
      url: getUrlFromFile(file, contentDir),
      weight: frontmatter.weight || 999
    });
  }
  
  // Generate navigation structure
  for (const [category, pages] of categories) {
    navigation.sections.push({
      title: category,
      pages: pages.sort((a, b) => a.weight - b.weight)
    });
  }
  
  // Write navigation data
  await fs.writeFile(
    path.join(contentDir, '../src/.components/navigation-data.json'),
    JSON.stringify(navigation, null, 2)
  );
  
  console.log('Navigation generated');
}

function getUrlFromFile(filePath, contentDir) {
  const relativePath = path.relative(contentDir, filePath);
  return '/' + relativePath.replace(/\.md$/, '/').replace(/\/index\/$/, '/');
}

generateNavigation('./migrated-content').catch(console.error);
```

### Hugo/Jekyll Integration

**Hugo Integration:**
```bash
#!/bin/bash
# migrate-to-hugo.sh

echo "Setting up Hugo integration..."

# Create Hugo site
hugo new site documentation-site
cd documentation-site

# Create content structure
mkdir -p content/{docs,api,tutorials}

# Copy migrated content
cp -r ../migrated-content/* content/

# Convert frontmatter to Hugo format
find content/ -name "*.md" | while read file; do
  # Hugo uses different frontmatter format
  sed -i 's/^category:/categories: ["/g; s/$/"]/' "$file"
  sed -i 's/^tags:/tags: ["/g; s/$/"]/' "$file"
done

# Create Hugo configuration
cat > hugo.yaml << 'EOF'
baseURL: 'https://docs.example.com'
languageCode: 'en-us'
title: 'Documentation Site'

params:
  version: '1.0'
  github_repo: 'https://github.com/company/docs'

menu:
  main:
    - name: 'Docs'
      url: '/docs/'
      weight: 10
    - name: 'API'
      url: '/api/'
      weight: 20
    - name: 'Tutorials'
      url: '/tutorials/'
      weight: 30

markup:
  goldmark:
    renderer:
      unsafe: true
  highlight:
    style: github
    lineNos: true
EOF

# Build site
hugo

echo "Hugo site built in public/"
```

**Jekyll Integration:**
```yaml
# _config.yml for Jekyll
title: Documentation Site
description: Migrated documentation site
baseurl: ""
url: "https://docs.example.com"

# Build settings
markdown: kramdown
highlighter: rouge
plugins:
  - jekyll-feed
  - jekyll-sitemap
  - jekyll-seo-tag

# Collections for different content types
collections:
  docs:
    output: true
    permalink: /:collection/:name/
  api:
    output: true
    permalink: /:collection/:name/
  tutorials:
    output: true
    permalink: /:collection/:name/

# Default frontmatter
defaults:
  - scope:
      path: ""
      type: "docs"
    values:
      layout: "doc"
      toc: true
  - scope:
      path: ""
      type: "api"
    values:
      layout: "api"
  - scope:
      path: ""
      type: "tutorials"
    values:
      layout: "tutorial"

# Sass configuration
sass:
  sass_dir: _sass
  style: compressed
```

### CMS Import Workflows

**Notion Import Integration:**
```javascript
// notion-import.js - Import migrated content to Notion
const { Client } = require('@notionhq/client');

class NotionImporter {
  constructor(apiKey, databaseId) {
    this.notion = new Client({ auth: apiKey });
    this.databaseId = databaseId;
  }
  
  async importMigratedContent(contentDir) {
    const files = await glob(`${contentDir}/**/*.md`);
    
    for (const file of files) {
      const content = await fs.readFile(file, 'utf-8');
      const { data: frontmatter, content: body } = matter(content);
      
      await this.createNotionPage(frontmatter, body);
    }
  }
  
  async createNotionPage(frontmatter, content) {
    const blocks = this.markdownToNotionBlocks(content);
    
    const properties = {
      'Title': {
        title: [{ text: { content: frontmatter.title } }]
      },
      'Category': {
        select: { name: frontmatter.category || 'General' }
      },
      'Status': {
        select: { name: 'Migrated' }
      }
    };
    
    if (frontmatter.tags) {
      properties['Tags'] = {
        multi_select: frontmatter.tags.map(tag => ({ name: tag }))
      };
    }
    
    try {
      const response = await this.notion.pages.create({
        parent: { database_id: this.databaseId },
        properties,
        children: blocks
      });
      
      console.log(`Created page: ${frontmatter.title}`);
      return response;
    } catch (error) {
      console.error(`Failed to create page ${frontmatter.title}:`, error);
    }
  }
  
  markdownToNotionBlocks(markdown) {
    // Convert markdown to Notion blocks
    const lines = markdown.split('\n');
    const blocks = [];
    
    for (const line of lines) {
      if (line.startsWith('# ')) {
        blocks.push({
          heading_1: {
            rich_text: [{ text: { content: line.substring(2) } }]
          }
        });
      } else if (line.startsWith('## ')) {
        blocks.push({
          heading_2: {
            rich_text: [{ text: { content: line.substring(3) } }]
          }
        });
      } else if (line.startsWith('```')) {
        // Handle code blocks (simplified)
        blocks.push({
          code: {
            rich_text: [{ text: { content: 'Code block content' } }],
            language: 'javascript'
          }
        });
      } else if (line.trim()) {
        blocks.push({
          paragraph: {
            rich_text: [{ text: { content: line } }]
          }
        });
      }
    }
    
    return blocks;
  }
}

// Usage
const importer = new NotionImporter(process.env.NOTION_API_KEY, process.env.NOTION_DATABASE_ID);
importer.importMigratedContent('./migrated-content').catch(console.error);
```

## Post-Migration Tasks

### SEO Redirect Planning

**Comprehensive Redirect Strategy:**
```bash
#!/bin/bash
# generate-redirects.sh

echo "Generating SEO redirects..."

# Create redirect mapping from migration log
cat > redirects.txt << 'EOF'
# Redirect mapping for migrated documentation
# Format: old-url new-url

# Main sections
/docs/getting-started.html /getting-started/
/docs/api/index.html /api/
/docs/tutorials/index.html /tutorials/

# Individual pages
/docs/installation.html /getting-started/installation/
/docs/configuration.html /guides/configuration/
/docs/troubleshooting.html /help/troubleshooting/
EOF

# Generate different redirect formats

# Nginx redirects
echo "Generating Nginx redirects..."
cat > nginx-redirects.conf << 'EOF'
# Nginx redirects for documentation migration

# Exact matches
location = /docs/getting-started.html {
  return 301 /getting-started/;
}

location = /docs/installation.html {
  return 301 /getting-started/installation/;
}

# Pattern-based redirects
location ~ ^/docs/(.+)\.html$ {
  return 301 /$1/;
}

# Fallback for old documentation
location /docs/ {
  return 301 /;
}
EOF

# Apache .htaccess redirects
echo "Generating Apache redirects..."
cat > .htaccess << 'EOF'
RewriteEngine On

# Exact redirects
RewriteRule ^docs/getting-started\.html$ /getting-started/ [R=301,L]
RewriteRule ^docs/installation\.html$ /getting-started/installation/ [R=301,L]

# Pattern redirects
RewriteRule ^docs/(.+)\.html$ /$1/ [R=301,L]

# Fallback
RewriteRule ^docs/(.*)$ / [R=301,L]
EOF

# Netlify _redirects format
echo "Generating Netlify redirects..."
cat > _redirects << 'EOF'
# Netlify redirects for documentation migration

/docs/getting-started.html /getting-started/ 301
/docs/installation.html /getting-started/installation/ 301
/docs/configuration.html /guides/configuration/ 301

# Splat redirects
/docs/* / 301
EOF

echo "Redirect files generated"
```

**Redirect Validation:**
```javascript
// validate-redirects.js
const axios = require('axios');

class RedirectValidator {
  constructor(baseUrl) {
    this.baseUrl = baseUrl;
    this.results = [];
  }
  
  async validateRedirects(redirectMappings) {
    for (const [oldPath, newPath] of redirectMappings) {
      const result = await this.testRedirect(oldPath, newPath);
      this.results.push(result);
    }
    
    return this.generateReport();
  }
  
  async testRedirect(oldPath, newPath) {
    try {
      const response = await axios.get(`${this.baseUrl}${oldPath}`, {
        maxRedirects: 0,
        validateStatus: status => status >= 300 && status < 400
      });
      
      const location = response.headers.location;
      const expectedUrl = `${this.baseUrl}${newPath}`;
      
      if (location === expectedUrl) {
        return {
          oldPath,
          newPath,
          status: 'success',
          actualLocation: location
        };
      } else {
        return {
          oldPath,
          newPath,
          status: 'incorrect',
          expectedLocation: expectedUrl,
          actualLocation: location
        };
      }
    } catch (error) {
      return {
        oldPath,
        newPath,
        status: 'error',
        error: error.message
      };
    }
  }
  
  generateReport() {
    const total = this.results.length;
    const successful = this.results.filter(r => r.status === 'success').length;
    const failed = this.results.filter(r => r.status !== 'success');
    
    return {
      summary: {
        total,
        successful,
        failed: total - successful,
        successRate: (successful / total * 100).toFixed(1)
      },
      failedRedirects: failed
    };
  }
}

// Usage
async function validateMigrationRedirects() {
  const validator = new RedirectValidator('https://docs.example.com');
  
  const redirects = [
    ['/docs/getting-started.html', '/getting-started/'],
    ['/docs/installation.html', '/getting-started/installation/'],
    ['/docs/api/index.html', '/api/']
  ];
  
  const report = await validator.validateRedirects(redirects);
  console.log('Redirect Validation Report:', report);
}

validateMigrationRedirects().catch(console.error);
```

### Content Review and Cleanup

**Final Content Review Process:**
```bash
#!/bin/bash
# final-content-review.sh

echo "Performing final content review..."

# Create review checklist for each major section
sections=("getting-started" "guides" "api" "tutorials" "reference")

for section in "${sections[@]}"; do
  echo "Reviewing $section section..."
  
  # Count pages in section
  page_count=$(find "migrated-content/$section" -name "*.md" | wc -l)
  echo "Pages to review: $page_count"
  
  # Check for common issues
  echo "Common issues found:"
  
  # Missing images
  grep -r '!\[.*\](/images/' "migrated-content/$section" | cut -d: -f2 | while read img_ref; do
    img_path=$(echo "$img_ref" | grep -o '/images/[^)]*')
    if [ ! -f "migrated-content$img_path" ]; then
      echo "  Missing image: $img_path"
    fi
  done
  
  # Broken internal links
  grep -r '\[.*\](/' "migrated-content/$section" | cut -d: -f2 | while read link_ref; do
    link_path=$(echo "$link_ref" | grep -o '(/[^)]*)' | tr -d '()')
    target_file="migrated-content${link_path}.md"
    if [ ! -f "$target_file" ] && [ ! -f "${target_file%/*}/index.md" ]; then
      echo "  Broken link: $link_path"
    fi
  done
  
  # Short pages (likely incomplete)
  find "migrated-content/$section" -name "*.md" | while read file; do
    word_count=$(wc -w < "$file")
    if [ "$word_count" -lt 100 ]; then
      echo "  Short page: $file ($word_count words)"
    fi
  done
  
  echo ""
done

# Generate content statistics
echo "Content Statistics:"
echo "==================="

total_pages=$(find migrated-content/ -name "*.md" | wc -l)
total_words=$(find migrated-content/ -name "*.md" -exec wc -w {} \; | awk '{sum+=$1} END {print sum}')
total_images=$(find migrated-content/ -name "*.{png,jpg,jpeg,gif,svg}" | wc -l)

echo "Total pages: $total_pages"
echo "Total words: $total_words"
echo "Total images: $total_images"
echo "Average words per page: $((total_words / total_pages))"

# Create review assignments
echo "Review assignments:"
echo "==================="
echo "Getting Started: @tech-writer-1"
echo "API Documentation: @developer-1 @developer-2"  
echo "Tutorials: @tech-writer-2"
echo "Guides: @tech-writer-1 @developer-1"
echo "Reference: @developer-2"

echo "Final review checklist created."
```

### User Training and Adoption

**User Migration Guide:**
```markdown
# Documentation Migration Guide for Users

## What's Changed

We've migrated our documentation to a new platform with improved:
- **Search functionality** - Find content faster
- **Mobile experience** - Better reading on all devices  
- **Navigation** - Clearer organization and structure
- **Performance** - Pages load 3x faster

## New URLs

| Old URL | New URL | Status |
|---------|---------|---------|
| `/docs/getting-started.html` | `/getting-started/` | ✅ Auto-redirect |
| `/docs/api/` | `/api/` | ✅ Auto-redirect |
| `/docs/tutorials/` | `/tutorials/` | ✅ Auto-redirect |

## Key Improvements

### Better Search
- Use the search box at the top of any page
- Search includes all content, not just titles
- Instant results as you type

### Improved Navigation
- Sidebar navigation on all pages
- Breadcrumb navigation
- "What's Next" suggestions

### Mobile Friendly
- Responsive design works on all screen sizes
- Touch-friendly navigation
- Offline reading capability

## Finding Content

### If you bookmarked old pages:
1. Visit your old bookmark
2. You'll be automatically redirected
3. Update your bookmark to the new URL

### If links are broken:
1. Use the search function
2. Browse the main navigation
3. Contact support if you can't find content

## Feedback

Found an issue or have suggestions?
- [Report a problem](mailto:docs@company.com)
- [Suggest improvements](https://github.com/company/docs/issues)
- [Join our Slack channel](https://company.slack.com/channels/documentation)
```

**Training Session Outline:**
```markdown
# Documentation Migration Training Session

## Session Overview (60 minutes)
1. Migration overview (10 min)
2. New platform tour (20 min)  
3. Content location guide (15 min)
4. Q&A and feedback (15 min)

## 1. Migration Overview
- Why we migrated
- What's new and improved
- Timeline and phases
- Support during transition

## 2. New Platform Tour
### Navigation
- Main navigation structure
- Sidebar navigation
- Breadcrumbs
- Search functionality

### Content Features  
- Improved formatting
- Code examples
- Interactive elements
- Mobile responsiveness

### Performance
- Faster loading times
- Better search results
- Offline capability

## 3. Content Location Guide
### Mapping old to new:
- Getting Started: Same location, better organized
- API Docs: Enhanced with interactive examples
- Tutorials: Step-by-step improvements
- Troubleshooting: Better search and categorization

### Finding content:
- Use search for quick access
- Browse by category
- Follow cross-references
- Check "Related Articles"

## 4. Support Resources
- Documentation feedback process
- Known issues and workarounds
- Future improvements roadmap
- Contact information for support

## Action Items
- [ ] Update bookmarks
- [ ] Test key workflows
- [ ] Provide feedback on any issues
- [ ] Share new URLs with team members
```

## Best Practices and Lessons Learned

### Migration Success Factors

**✅ Critical Success Factors:**
1. **Thorough Planning**: Detailed content inventory and mapping
2. **Quality First**: Automated quality checks and manual review
3. **User Communication**: Clear communication about changes
4. **Gradual Rollout**: Phase migration to minimize disruption
5. **Feedback Loops**: Collect and act on user feedback

**❌ Common Pitfalls:**
1. **Rushing the Process**: Inadequate testing and quality checks
2. **Ignoring SEO**: Missing redirects and broken links
3. **Poor Communication**: Users surprised by changes
4. **No Rollback Plan**: No way to revert if issues arise
5. **Insufficient Testing**: Problems discovered after launch

### Performance Optimization

**Crawling Optimization:**
```yaml
# .inform-optimized.yaml
performance:
  concurrent_requests: 5      # Balance speed vs. server load
  request_delay: 1000        # Respectful crawling
  timeout: 30                # Reasonable timeout
  retry_attempts: 3          # Handle temporary failures
  
  memory_management:
    max_memory_mb: 2048      # Prevent memory issues
    garbage_collection: true  # Regular cleanup
    streaming_mode: true     # Process incrementally
    
  caching:
    enable_http_cache: true   # Cache responses
    cache_duration: "1h"     # Reasonable cache time
    respect_cache_headers: true
```

**Content Processing:**
```bash
#!/bin/bash
# optimize-migration.sh

# Process content in parallel
echo "Optimizing content processing..."

# Use multiple processes for different content types
{
  inform https://docs.site.com/api --output-dir api/ &
  inform https://docs.site.com/guides --output-dir guides/ &
  inform https://docs.site.com/tutorials --output-dir tutorials/ &
  inform https://docs.site.com/reference --output-dir reference/ &
  wait
}

echo "Parallel processing completed"

# Optimize images in background
{
  find . -name "*.png" -exec pngquant --force --ext .png {} \; &
  find . -name "*.jpg" -exec jpegoptim --strip-all --max=85 {} \; &
  wait
}

echo "Image optimization completed"
```

### Maintenance Strategies

**Ongoing Content Maintenance:**
```yaml
# .github/workflows/content-maintenance.yml
name: Content Maintenance

on:
  schedule:
    - cron: '0 2 * * 1'  # Weekly on Monday at 2 AM

jobs:
  validate-content:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Validate links
        run: |
          npm install -g broken-link-checker
          blc http://docs.company.com --recursive --filter-level 3
          
      - name: Check for outdated content
        run: |
          # Find content not updated in 6 months
          find content/ -name "*.md" -mtime +180 -ls
          
      - name: Generate maintenance report
        run: |
          echo "# Content Maintenance Report" > maintenance-report.md
          echo "Generated: $(date)" >> maintenance-report.md
          echo "" >> maintenance-report.md
          
          # Add statistics and findings
          echo "## Statistics" >> maintenance-report.md
          echo "- Total pages: $(find content/ -name '*.md' | wc -l)" >> maintenance-report.md
          echo "- Potentially outdated: $(find content/ -name '*.md' -mtime +180 | wc -l)" >> maintenance-report.md
          
      - name: Create issue for maintenance items
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const report = fs.readFileSync('maintenance-report.md', 'utf8');
            
            github.rest.issues.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: 'Weekly Content Maintenance Report',
              body: report,
              labels: ['documentation', 'maintenance']
            });
```

## Next Steps

### Advanced Migration Topics
- [Large-Scale Migration Strategies](large-scale-migration.md) - Enterprise migration patterns
- [Multi-Source Migration](multi-source-migration.md) - Combining multiple documentation sources
- [Automated Migration Pipelines](automated-migration.md) - CI/CD for content migration

### Integration Resources
- [Static Site Generator Integration](../integrations/inform-static-sites.md) - Complete integration guides
- [CMS Import Workflows](../integrations/inform-cms.md) - Content management system integration
- [Migration Automation Tools](../examples/inform/migration-tools/) - Ready-to-use scripts and tools

### Support Resources
- [Migration Troubleshooting](../troubleshooting/inform-migration.md) - Common issues and solutions
- [Community Migration Examples](https://github.com/orgs/fwdslsh/discussions) - Real-world migration stories
- [Professional Migration Services](https://fwdslsh.dev/services) - Expert migration assistance

**Transform your legacy documentation with Inform's powerful migration capabilities!** 🚀

---

*This guide is part of the [fwdslsh documentation](https://fwdslsh.dev). For more migration strategies and integration patterns, explore our [complete Inform documentation](../inform/).*