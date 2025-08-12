# Creating a Documentation Site with Unify

Build a professional documentation site from scratch using Unify's powerful templating system, component architecture, and Markdown processing capabilities. This comprehensive tutorial will guide you through creating a full-featured docs site with search, navigation, and responsive design.

## What You'll Build

By the end of this tutorial, you'll have created:

- **Multi-section documentation site** with Getting Started, API Reference, Guides, and Tutorials
- **Responsive navigation** with sidebar and mobile menu
- **Search functionality** with client-side search
- **Code syntax highlighting** for multiple languages
- **Automatic table of contents** generation
- **SEO optimization** with meta tags and sitemaps
- **Dark/light theme toggle**
- **Print-friendly styling**

## Prerequisites

- [Bun](https://bun.sh) installed (v1.2.0 or later)
- Basic HTML/CSS knowledge
- Familiarity with Markdown
- Text editor (VS Code recommended)

## Project Setup

### 1. Create the Project Structure

```bash
# Create new Unify documentation site
npm create unify-site my-docs-site
cd my-docs-site

# Create documentation-specific structure
mkdir -p src/{guides,api,tutorials,reference}
mkdir -p src/.components/{navigation,layout,search,theme}
mkdir -p src/assets/{css,js,images}
```

### 2. Configure Unify for Documentation

Create `unify.config.js`:

```javascript
export default {
  build: {
    baseUrl: process.env.BASE_URL || '/',
    outputDir: 'dist',
    prettyUrls: true,
    minify: process.env.NODE_ENV === 'production'
  },
  
  dev: {
    port: process.env.PORT || 3000,
    liveReload: true,
    openBrowser: true
  },
  
  components: {
    globalComponents: [
      'src/.components/layout/head.html',
      'src/.components/layout/header.html', 
      'src/.components/layout/footer.html'
    ]
  },
  
  markdown: {
    codeHighlighting: true,
    tableOfContents: true,
    linkify: true,
    typographer: true
  },
  
  seo: {
    generateSitemap: true,
    defaultMeta: {
      author: 'Your Documentation Team',
      description: 'Comprehensive documentation for our platform',
      keywords: 'documentation, api, guides, tutorials'
    }
  }
};
```

## Core Layout Components

### 1. Document Layout Template

Create `src/.components/layout/doc-layout.html`:

```html
<!DOCTYPE html>
<html lang="en" data-theme="light">
<head>
  <!-- @include .components/layout/head.html -->
  <title>{{ title }} - Documentation</title>
  <meta name="description" content="{{ description || 'Documentation page' }}">
  
  <link rel="stylesheet" href="/assets/css/documentation.css">
  <link rel="stylesheet" href="/assets/css/syntax-highlight.css">
  
  <script src="/assets/js/search.js" defer></script>
  <script src="/assets/js/theme-toggle.js" defer></script>
</head>
<body class="documentation-page">
  <a href="#main-content" class="skip-link">Skip to content</a>
  
  <!-- @include .components/layout/header.html -->
  
  <div class="doc-container">
    <aside class="doc-sidebar" id="sidebar">
      <!-- @include .components/navigation/sidebar.html -->
    </aside>
    
    <main class="doc-content" id="main-content">
      <!-- @include .components/navigation/breadcrumbs.html -->
      
      <article class="doc-article">
        <header class="page-header">
          <h1 class="page-title">{{ title }}</h1>
          {% if subtitle %}
          <p class="page-subtitle">{{ subtitle }}</p>
          {% endif %}
          
          <div class="page-meta">
            {% if category %}
            <span class="page-category">{{ category }}</span>
            {% endif %}
            {% if difficulty %}
            <span class="page-difficulty difficulty-{{ difficulty }}">{{ difficulty }}</span>
            {% endif %}
            {% if reading_time %}
            <span class="reading-time">{{ reading_time }} min read</span>
            {% endif %}
          </div>
        </header>
        
        {% if show_toc !== false %}
        <nav class="table-of-contents">
          <h2>On This Page</h2>
          <!-- @include .components/navigation/toc.html -->
        </nav>
        {% endif %}
        
        <div class="page-content">
          <!-- @slot -->
          {{ content }}
          <!-- @endslot -->
        </div>
        
        <footer class="page-footer">
          {% if edit_url %}
          <a href="{{ edit_url }}" class="edit-link" target="_blank" rel="noopener">
            📝 Edit this page
          </a>
          {% endif %}
          
          {% if last_updated %}
          <p class="last-updated">Last updated: {{ last_updated }}</p>
          {% endif %}
          
          <!-- @include .components/navigation/page-nav.html -->
        </footer>
      </article>
    </main>
    
    <aside class="doc-aside">
      <!-- @include .components/navigation/on-page-nav.html -->
    </aside>
  </div>
  
  <!-- @include .components/layout/footer.html -->
  
  <button class="mobile-menu-toggle" aria-label="Toggle navigation" id="mobile-menu-toggle">
    <span></span>
    <span></span>
    <span></span>
  </button>
  
  <script src="/assets/js/documentation.js"></script>
</body>
</html>
```

## Interactive Features

### Search Functionality

Create `src/assets/js/search.js`:

```javascript
class DocumentationSearch {
  constructor() {
    this.searchIndex = [];
    this.searchInput = document.getElementById('search-input');
    this.searchResults = document.getElementById('search-results');
    
    this.init();
  }
  
  async init() {
    await this.buildSearchIndex();
    this.setupEventListeners();
  }
  
  async buildSearchIndex() {
    const content = document.querySelector('.page-content');
    if (!content) return;
    
    const headings = content.querySelectorAll('h1, h2, h3, h4, h5, h6');
    const paragraphs = content.querySelectorAll('p');
    
    headings.forEach((heading, index) => {
      this.searchIndex.push({
        title: heading.textContent,
        content: heading.textContent,
        url: `#${heading.id || 'heading-' + index}`,
        type: 'heading',
        weight: this.getHeadingWeight(heading.tagName)
      });
    });
    
    paragraphs.forEach(paragraph => {
      const text = paragraph.textContent;
      if (text.length > 50) {
        this.searchIndex.push({
          title: text.substring(0, 100) + '...',
          content: text,
          url: window.location.pathname,
          type: 'content',
          weight: 1
        });
      }
    });
  }
  
  getHeadingWeight(tagName) {
    const weights = { 'H1': 5, 'H2': 4, 'H3': 3, 'H4': 2, 'H5': 1, 'H6': 1 };
    return weights[tagName] || 1;
  }
  
  setupEventListeners() {
    if (!this.searchInput) return;
    
    let searchTimeout;
    
    this.searchInput.addEventListener('input', (e) => {
      clearTimeout(searchTimeout);
      searchTimeout = setTimeout(() => {
        this.performSearch(e.target.value);
      }, 300);
    });
  }
  
  performSearch(query) {
    if (!query.trim()) {
      this.searchResults.classList.remove('show');
      return;
    }
    
    const results = this.search(query.toLowerCase());
    this.displayResults(results);
  }
  
  search(query) {
    const words = query.split(' ').filter(word => word.length > 0);
    
    return this.searchIndex
      .map(item => {
        let score = 0;
        const content = item.content.toLowerCase();
        const title = item.title.toLowerCase();
        
        words.forEach(word => {
          if (title.includes(word)) score += item.weight * 3;
          if (content.includes(word)) score += item.weight;
        });
        
        return { ...item, score };
      })
      .filter(item => item.score > 0)
      .sort((a, b) => b.score - a.score)
      .slice(0, 10);
  }
  
  displayResults(results) {
    if (results.length === 0) {
      this.searchResults.innerHTML = '<div class="search-no-results">No results found</div>';
      this.searchResults.classList.add('show');
      return;
    }
    
    const html = results.map(result => `
      <div class="search-result-item">
        <a href="${result.url}" class="search-result-link">
          <div class="search-result-title">${this.highlightQuery(result.title, this.searchInput.value)}</div>
          <div class="search-result-content">${this.highlightQuery(result.content.substring(0, 150) + '...', this.searchInput.value)}</div>
          <div class="search-result-type">${result.type}</div>
        </a>
      </div>
    `).join('');
    
    this.searchResults.innerHTML = html;
    this.searchResults.classList.add('show');
  }
  
  highlightQuery(text, query) {
    if (!query) return text;
    const regex = new RegExp(`(${query})`, 'gi');
    return text.replace(regex, '<mark>$1</mark>');
  }
}

document.addEventListener('DOMContentLoaded', () => {
  new DocumentationSearch();
});
```

## Documentation CSS Styling

Create `src/assets/css/documentation.css`:

```css
:root {
  --doc-primary: #2563eb;
  --doc-secondary: #64748b;
  --doc-bg: #ffffff;
  --doc-surface: #f8fafc;
  --doc-text: #1e293b;
  --doc-text-secondary: #64748b;
  --doc-border: #e2e8f0;
  --doc-code-bg: #f1f5f9;
  --doc-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  
  --sidebar-width: 280px;
  --sidebar-bg: var(--doc-surface);
  --sidebar-border: var(--doc-border);
  
  --content-max-width: 800px;
  --aside-width: 240px;
  
  --font-family-sans: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  --font-family-mono: 'Fira Code', 'Monaco', 'Consolas', monospace;
}

[data-theme="dark"] {
  --doc-bg: #0f172a;
  --doc-surface: #1e293b;
  --doc-text: #f1f5f9;
  --doc-text-secondary: #94a3b8;
  --doc-border: #334155;
  --doc-code-bg: #1e293b;
  --sidebar-bg: #1e293b;
  --sidebar-border: #334155;
}

* {
  box-sizing: border-box;
}

body {
  font-family: var(--font-family-sans);
  line-height: 1.6;
  color: var(--doc-text);
  background: var(--doc-bg);
  margin: 0;
}

.doc-container {
  display: grid;
  grid-template-columns: var(--sidebar-width) 1fr var(--aside-width);
  grid-template-areas: "sidebar content aside";
  min-height: calc(100vh - 60px);
  gap: 0;
}

.doc-sidebar {
  grid-area: sidebar;
  background: var(--sidebar-bg);
  border-right: 1px solid var(--sidebar-border);
  overflow-y: auto;
  position: sticky;
  top: 60px;
  height: calc(100vh - 60px);
  padding: 2rem 1.5rem;
}

.doc-content {
  grid-area: content;
  padding: 2rem 3rem;
  max-width: var(--content-max-width);
  margin: 0 auto;
}

.doc-aside {
  grid-area: aside;
  padding: 2rem 1.5rem;
  border-left: 1px solid var(--doc-border);
  background: var(--doc-surface);
}

.site-header {
  background: var(--doc-bg);
  border-bottom: 1px solid var(--doc-border);
  position: sticky;
  top: 0;
  z-index: 100;
  height: 60px;
}

.header-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 2rem;
  height: 100%;
  max-width: 1400px;
  margin: 0 auto;
}

.page-header {
  margin-bottom: 2rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid var(--doc-border);
}

.page-title {
  font-size: 2.5rem;
  font-weight: 700;
  margin: 0 0 0.5rem 0;
  color: var(--doc-text);
  line-height: 1.2;
}

.page-content {
  line-height: 1.7;
}

.page-content h1,
.page-content h2,
.page-content h3,
.page-content h4 {
  color: var(--doc-text);
  margin-top: 2rem;
  margin-bottom: 1rem;
  font-weight: 600;
  line-height: 1.3;
}

.page-content pre {
  background: var(--doc-code-bg);
  padding: 1.5rem;
  border-radius: 8px;
  overflow-x: auto;
  font-family: var(--font-family-mono);
  font-size: 0.9rem;
  line-height: 1.5;
  margin: 1.5rem 0;
}

.page-content code {
  background: var(--doc-code-bg);
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  font-family: var(--font-family-mono);
  font-size: 0.875rem;
  color: var(--doc-primary);
}

.page-content pre code {
  background: none;
  padding: 0;
  color: var(--doc-text);
}

@media (max-width: 1024px) {
  .doc-container {
    grid-template-columns: 1fr;
    grid-template-areas: "content";
  }
  
  .doc-aside {
    display: none;
  }
  
  .doc-sidebar {
    position: fixed;
    left: -280px;
    top: 60px;
    height: calc(100vh - 60px);
    width: var(--sidebar-width);
    z-index: 200;
    transition: left 0.3s ease;
    box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
  }
  
  .doc-sidebar.open {
    left: 0;
  }
  
  .mobile-menu-toggle {
    display: block;
    position: fixed;
    top: 1rem;
    left: 1rem;
    background: var(--doc-primary);
    border: none;
    padding: 0.5rem;
    border-radius: 4px;
    cursor: pointer;
    z-index: 300;
  }
  
  .mobile-menu-toggle span {
    display: block;
    width: 20px;
    height: 2px;
    background: white;
    margin: 3px 0;
    transition: 0.3s;
  }
}

@media (max-width: 768px) {
  .doc-content {
    padding: 1rem;
  }
  
  .page-title {
    font-size: 2rem;
  }
}

.skip-link {
  position: absolute;
  top: -40px;
  left: 6px;
  background: var(--doc-primary);
  color: white;
  padding: 8px;
  text-decoration: none;
  border-radius: 4px;
  transition: top 0.3s;
  z-index: 1000;
}

.skip-link:focus {
  top: 6px;
}
```

## Sample Content Pages

### Homepage

Create `src/index.html`:

```html
---
title: Documentation Home
description: Welcome to our comprehensive documentation
layout: doc-layout
show_toc: false
---

# Welcome to Our Documentation

Get started quickly with our comprehensive guides, tutorials, and API reference.

## Quick Start

<div class="quick-start-grid">
  <div class="quick-start-card">
    <h3>🚀 Installation</h3>
    <p>Get up and running in minutes with our installation guide.</p>
    <a href="/guides/installation/" class="card-link">Install Now →</a>
  </div>
  
  <div class="quick-start-card">
    <h3>📖 User Guide</h3>
    <p>Learn the basics with our comprehensive user guide.</p>
    <a href="/guides/quick-start/" class="card-link">Get Started →</a>
  </div>
  
  <div class="quick-start-card">
    <h3>🔧 API Reference</h3>
    <p>Detailed API documentation with examples and schemas.</p>
    <a href="/api/" class="card-link">View API →</a>
  </div>
  
  <div class="quick-start-card">
    <h3>🎓 Tutorials</h3>
    <p>Step-by-step tutorials for common use cases.</p>
    <a href="/tutorials/" class="card-link">Learn More →</a>
  </div>
</div>

## What's New

### Version 2.1.0 - Latest Release
- **New Feature**: Advanced user management
- **Improvement**: 50% faster API response times  
- **Fix**: Resolved authentication issues

[View Release Notes →](/reference/changelog/)

<style>
.quick-start-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.5rem;
  margin: 2rem 0;
}

.quick-start-card {
  background: var(--doc-surface);
  padding: 1.5rem;
  border-radius: 8px;
  border: 1px solid var(--doc-border);
  transition: all 0.2s;
}

.quick-start-card:hover {
  border-color: var(--doc-primary);
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}
</style>
```

## Building and Deployment

### Build Configuration

Add scripts to `package.json`:

```json
{
  "scripts": {
    "dev": "unify serve --source src --port 3000",
    "build": "unify build --source src --output dist", 
    "preview": "unify serve --source dist --port 3001",
    "clean": "rm -rf dist",
    "build:prod": "NODE_ENV=production unify build --source src --output dist --minify"
  }
}
```

### Final Steps

```bash
# Install dependencies
bun install

# Start development server
bun run dev

# Build for production
bun run build:prod
```

## 🎉 Congratulations!

You've successfully created a comprehensive documentation site with Unify! Your site includes:

- **Professional Design**: Clean, modern interface with dark/light themes
- **Excellent UX**: Search, navigation, and mobile-responsive design
- **Developer-Friendly**: Syntax highlighting, copy buttons, and clear API docs
- **SEO Optimized**: Meta tags, sitemaps, and semantic HTML
- **Accessible**: WCAG-compliant design with keyboard navigation
- **Performant**: Optimized builds and fast loading times

**Your documentation site is now ready to serve your users with an exceptional experience!** 🚀

---

*This tutorial is part of the [fwdslsh documentation](https://fwdslsh.dev). For more advanced Unify techniques and real-world examples, explore our [complete documentation](../unify/).*