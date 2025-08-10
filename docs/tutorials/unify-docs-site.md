# Building a Documentation Site with Unify

This tutorial will walk you through creating a professional documentation site using Unify, complete with navigation, search, and responsive design.

## What We'll Build

By the end of this tutorial, you'll have:
- A multi-page documentation site
- Responsive navigation with mobile menu
- Search functionality
- Code syntax highlighting
- SEO-optimized pages
- Dark/light theme toggle

## Prerequisites

- [Bun](https://bun.sh) installed
- Basic knowledge of HTML, CSS, and Markdown
- Text editor (VS Code recommended with Unify extension)

## Project Setup

### 1. Create the Project

```bash
# Create a new Unify site
npm create unify-site my-docs
cd my-docs

# Start the development server
unify serve
```

Your site is now running at http://localhost:3000.

### 2. Project Structure

Let's organize our documentation site:

```bash
# Create directory structure
mkdir -p src/{docs,api,guides,examples}
mkdir -p src/.components/{nav,layout,ui}
mkdir -p src/assets/{css,js,images}
```

Final structure:
```
src/
├── .components/
│   ├── nav/
│   ├── layout/
│   └── ui/
├── assets/
│   ├── css/
│   ├── js/
│   └── images/
├── docs/
├── api/
├── guides/
├── examples/
└── index.html
```

## Building the Layout System

### 1. Create Base Layout

Create `src/.components/layout/base.html`:

```html
<!DOCTYPE html>
<html lang="en" data-theme="light">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><!--#echo var="title" default="Documentation" --> | My Docs</title>
    <meta name="description" content="<!--#echo var="description" default="Comprehensive documentation and guides" -->">
    
    <!-- Favicon -->
    <link rel="icon" type="image/svg+xml" href="/assets/images/favicon.svg">
    
    <!-- Styles -->
    <link rel="stylesheet" href="/assets/css/main.css">
    <link rel="stylesheet" href="/assets/css/syntax.css">
    
    <!-- Preload fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
</head>
<body>
    <!--#include virtual="/.components/nav/header.html" -->
    
    <div class="layout">
        <!--#include virtual="/.components/nav/sidebar.html" -->
        
        <main class="main-content">
            <div class="content-wrapper">
                <!--#echo var="content" -->
            </div>
        </main>
        
        <!--#include virtual="/.components/nav/toc.html" -->
    </div>
    
    <!--#include virtual="/.components/layout/footer.html" -->
    <!--#include virtual="/.components/ui/search-modal.html" -->
    
    <script src="/assets/js/main.js"></script>
</body>
</html>
```

### 2. Create Header Navigation

Create `src/.components/nav/header.html`:

```html
<header class="header">
    <nav class="navbar">
        <div class="nav-brand">
            <a href="/" class="brand-link">
                <img src="/assets/images/logo.svg" alt="Logo" class="brand-logo">
                <span class="brand-text">My Docs</span>
            </a>
        </div>
        
        <div class="nav-center">
            <button class="search-trigger" data-search-open>
                <svg class="search-icon" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <circle cx="11" cy="11" r="8"></circle>
                    <path d="m21 21-4.35-4.35"></path>
                </svg>
                <span>Search docs...</span>
                <kbd class="search-kbd">⌘K</kbd>
            </button>
        </div>
        
        <div class="nav-actions">
            <button class="theme-toggle" data-theme-toggle>
                <svg class="theme-icon theme-icon-light" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <circle cx="12" cy="12" r="5"></circle>
                    <line x1="12" y1="1" x2="12" y2="3"></line>
                    <line x1="12" y1="21" x2="12" y2="23"></line>
                    <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
                    <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
                    <line x1="1" y1="12" x2="3" y2="12"></line>
                    <line x1="21" y1="12" x2="23" y2="12"></line>
                    <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
                    <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
                </svg>
                <svg class="theme-icon theme-icon-dark" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path>
                </svg>
            </button>
            
            <a href="https://github.com/your-org/your-repo" class="github-link" target="_blank" rel="noopener">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                    <path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/>
                </svg>
            </a>
            
            <button class="mobile-menu-toggle" data-mobile-menu-toggle>
                <span class="mobile-menu-line"></span>
                <span class="mobile-menu-line"></span>
                <span class="mobile-menu-line"></span>
            </button>
        </div>
    </nav>
</header>
```

### 3. Create Sidebar Navigation

Create `src/.components/nav/sidebar.html`:

```html
<aside class="sidebar" data-sidebar>
    <div class="sidebar-content">
        <div class="sidebar-section">
            <h3 class="sidebar-title">Getting Started</h3>
            <ul class="sidebar-menu">
                <li><a href="/docs/installation" class="sidebar-link">Installation</a></li>
                <li><a href="/docs/quick-start" class="sidebar-link">Quick Start</a></li>
                <li><a href="/docs/configuration" class="sidebar-link">Configuration</a></li>
            </ul>
        </div>
        
        <div class="sidebar-section">
            <h3 class="sidebar-title">Guides</h3>
            <ul class="sidebar-menu">
                <li><a href="/guides/basic-usage" class="sidebar-link">Basic Usage</a></li>
                <li><a href="/guides/advanced-features" class="sidebar-link">Advanced Features</a></li>
                <li><a href="/guides/best-practices" class="sidebar-link">Best Practices</a></li>
            </ul>
        </div>
        
        <div class="sidebar-section">
            <h3 class="sidebar-title">API Reference</h3>
            <ul class="sidebar-menu">
                <li><a href="/api/overview" class="sidebar-link">Overview</a></li>
                <li><a href="/api/authentication" class="sidebar-link">Authentication</a></li>
                <li><a href="/api/endpoints" class="sidebar-link">Endpoints</a></li>
            </ul>
        </div>
        
        <div class="sidebar-section">
            <h3 class="sidebar-title">Examples</h3>
            <ul class="sidebar-menu">
                <li><a href="/examples/basic" class="sidebar-link">Basic Example</a></li>
                <li><a href="/examples/advanced" class="sidebar-link">Advanced Example</a></li>
                <li><a href="/examples/integrations" class="sidebar-link">Integrations</a></li>
            </ul>
        </div>
    </div>
</aside>
```

## Creating Documentation Pages

### 1. Homepage

Update `src/index.html`:

```html
<!--#set var="title" value="Home" -->
<!--#set var="description" value="Welcome to our comprehensive documentation" -->
<!--#set var="content" value="
<div class='hero'>
    <h1 class='hero-title'>Welcome to Our Documentation</h1>
    <p class='hero-description'>Everything you need to get started and master our tools.</p>
    <div class='hero-actions'>
        <a href='/docs/quick-start' class='btn btn-primary'>Get Started</a>
        <a href='/api/overview' class='btn btn-secondary'>API Reference</a>
    </div>
</div>

<div class='features'>
    <div class='feature-grid'>
        <div class='feature-card'>
            <h3>Quick Start</h3>
            <p>Get up and running in minutes with our step-by-step guide.</p>
            <a href='/docs/quick-start'>Learn more →</a>
        </div>
        <div class='feature-card'>
            <h3>API Reference</h3>
            <p>Complete API documentation with examples and response formats.</p>
            <a href='/api/overview'>Explore API →</a>
        </div>
        <div class='feature-card'>
            <h3>Examples</h3>
            <p>Real-world examples and code samples to help you implement quickly.</p>
            <a href='/examples/basic'>View Examples →</a>
        </div>
    </div>
</div>
" -->
<!--#include virtual="/.components/layout/base.html" -->
```

### 2. Documentation Pages

Create `src/docs/quick-start.md`:

```markdown
---
title: Quick Start Guide
description: Get started with our platform in just a few minutes
layout: docs
---

# Quick Start Guide

Welcome! This guide will get you up and running in just a few minutes.

## Installation

Choose your preferred installation method:

### npm

```bash
npm install @yourorg/yourpackage
```

### yarn

```bash
yarn add @yourorg/yourpackage
```

### CDN

```html
<script src="https://cdn.jsdelivr.net/npm/@yourorg/yourpackage@latest/dist/index.js"></script>
```

## Basic Usage

Here's a simple example to get you started:

```javascript
import { YourClass } from '@yourorg/yourpackage';

const instance = new YourClass({
  apiKey: 'your-api-key',
  environment: 'production'
});

// Make your first API call
const result = await instance.getData();
console.log(result);
```

## Configuration

Configure the library with these options:

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `apiKey` | string | - | Your API key (required) |
| `environment` | string | 'production' | Environment setting |
| `timeout` | number | 5000 | Request timeout in ms |

## Next Steps

Now that you have the basics working:

1. [Read the full documentation](./installation)
2. [Explore advanced features](../guides/advanced-features)
3. [Check out examples](../examples/basic)

## Need Help?

- [Check our FAQ](./faq)
- [Join our Discord](https://discord.gg/yourserver)
- [Submit an issue](https://github.com/yourorg/yourrepo/issues)
```

### 3. Create a Documentation Layout

Create `src/.components/layout/docs.html`:

```html
<!--#set var="content" value="
<div class='docs-layout'>
    <div class='docs-header'>
        <h1><!--#echo var='title' --></h1>
        <!--#if expr='${description}' -->
        <p class='docs-description'><!--#echo var='description' --></p>
        <!--#endif -->
    </div>
    <div class='docs-content'>
        <!--#echo var='markdown_content' -->
    </div>
    <div class='docs-footer'>
        <div class='docs-nav-links'>
            <!--#if expr='${prev_page}' -->
            <a href='<!--#echo var=\"prev_page_url\" -->' class='docs-nav-link docs-nav-prev'>
                ← <!--#echo var='prev_page' -->
            </a>
            <!--#endif -->
            <!--#if expr='${next_page}' -->
            <a href='<!--#echo var=\"next_page_url\" -->' class='docs-nav-link docs-nav-next'>
                <!--#echo var='next_page' --> →
            </a>
            <!--#endif -->
        </div>
        <div class='docs-edit'>
            <a href='https://github.com/yourorg/yourrepo/edit/main/src<!--#echo var=\"page_path\" -->' target='_blank' rel='noopener'>
                Edit this page on GitHub
            </a>
        </div>
    </div>
</div>
" -->
<!--#include virtual="/.components/layout/base.html" -->
```

## Styling the Documentation

### 1. Main CSS

Create `src/assets/css/main.css`:

```css
:root {
  /* Colors */
  --color-primary: #3b82f6;
  --color-primary-hover: #2563eb;
  --color-secondary: #6b7280;
  --color-success: #10b981;
  --color-warning: #f59e0b;
  --color-danger: #ef4444;
  
  /* Background */
  --bg-primary: #ffffff;
  --bg-secondary: #f8fafc;
  --bg-tertiary: #f1f5f9;
  
  /* Text */
  --text-primary: #1e293b;
  --text-secondary: #64748b;
  --text-tertiary: #94a3b8;
  
  /* Border */
  --border-color: #e2e8f0;
  --border-radius: 8px;
  
  /* Shadows */
  --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1);
  
  /* Typography */
  --font-family: 'Inter', system-ui, sans-serif;
  --font-family-mono: 'JetBrains Mono', monospace;
  
  /* Layout */
  --header-height: 60px;
  --sidebar-width: 280px;
  --toc-width: 240px;
}

/* Dark theme */
[data-theme="dark"] {
  --bg-primary: #0f172a;
  --bg-secondary: #1e293b;
  --bg-tertiary: #334155;
  
  --text-primary: #f8fafc;
  --text-secondary: #cbd5e1;
  --text-tertiary: #94a3b8;
  
  --border-color: #334155;
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: var(--font-family);
  background-color: var(--bg-primary);
  color: var(--text-primary);
  line-height: 1.7;
}

/* Header */
.header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  height: var(--header-height);
  background-color: var(--bg-primary);
  border-bottom: 1px solid var(--border-color);
  z-index: 1000;
}

.navbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 100%;
  padding: 0 24px;
  max-width: 1400px;
  margin: 0 auto;
}

.nav-brand {
  display: flex;
  align-items: center;
}

.brand-link {
  display: flex;
  align-items: center;
  text-decoration: none;
  color: var(--text-primary);
  font-weight: 600;
  font-size: 18px;
}

.brand-logo {
  width: 32px;
  height: 32px;
  margin-right: 12px;
}

/* Layout */
.layout {
  display: flex;
  margin-top: var(--header-height);
  min-height: calc(100vh - var(--header-height));
}

/* Sidebar */
.sidebar {
  width: var(--sidebar-width);
  background-color: var(--bg-secondary);
  border-right: 1px solid var(--border-color);
  padding: 24px 0;
  overflow-y: auto;
  height: calc(100vh - var(--header-height));
  position: sticky;
  top: var(--header-height);
}

.sidebar-content {
  padding: 0 24px;
}

.sidebar-section {
  margin-bottom: 32px;
}

.sidebar-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-secondary);
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin: 0 0 12px 0;
}

.sidebar-menu {
  list-style: none;
  margin: 0;
  padding: 0;
}

.sidebar-menu li {
  margin-bottom: 4px;
}

.sidebar-link {
  display: block;
  padding: 8px 12px;
  text-decoration: none;
  color: var(--text-secondary);
  border-radius: var(--border-radius);
  transition: all 0.2s ease;
}

.sidebar-link:hover,
.sidebar-link.active {
  background-color: var(--bg-tertiary);
  color: var(--text-primary);
}

/* Main content */
.main-content {
  flex: 1;
  max-width: calc(100% - var(--sidebar-width) - var(--toc-width));
}

.content-wrapper {
  padding: 32px 48px;
  max-width: 800px;
}

/* Hero section */
.hero {
  text-align: center;
  padding: 80px 0;
}

.hero-title {
  font-size: 48px;
  font-weight: 700;
  margin: 0 0 16px 0;
  background: linear-gradient(135deg, var(--color-primary) 0%, #8b5cf6 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.hero-description {
  font-size: 20px;
  color: var(--text-secondary);
  margin: 0 0 32px 0;
}

.hero-actions {
  display: flex;
  gap: 16px;
  justify-content: center;
}

/* Buttons */
.btn {
  display: inline-flex;
  align-items: center;
  padding: 12px 24px;
  border-radius: var(--border-radius);
  text-decoration: none;
  font-weight: 500;
  transition: all 0.2s ease;
  border: 1px solid transparent;
}

.btn-primary {
  background-color: var(--color-primary);
  color: white;
}

.btn-primary:hover {
  background-color: var(--color-primary-hover);
}

.btn-secondary {
  background-color: transparent;
  color: var(--text-primary);
  border-color: var(--border-color);
}

.btn-secondary:hover {
  background-color: var(--bg-secondary);
}

/* Features grid */
.features {
  padding: 80px 0;
}

.feature-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 32px;
}

.feature-card {
  padding: 32px;
  background-color: var(--bg-secondary);
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius);
  transition: all 0.2s ease;
}

.feature-card:hover {
  box-shadow: var(--shadow-lg);
  transform: translateY(-2px);
}

.feature-card h3 {
  margin: 0 0 16px 0;
  color: var(--text-primary);
}

.feature-card p {
  margin: 0 0 16px 0;
  color: var(--text-secondary);
}

.feature-card a {
  color: var(--color-primary);
  text-decoration: none;
  font-weight: 500;
}

/* Documentation styles */
.docs-layout {
  max-width: none;
}

.docs-header {
  margin-bottom: 32px;
  padding-bottom: 16px;
  border-bottom: 1px solid var(--border-color);
}

.docs-header h1 {
  margin: 0 0 8px 0;
  font-size: 36px;
  font-weight: 700;
}

.docs-description {
  margin: 0;
  font-size: 18px;
  color: var(--text-secondary);
}

.docs-content {
  margin-bottom: 48px;
}

.docs-content h2 {
  margin: 32px 0 16px 0;
  font-size: 24px;
  font-weight: 600;
}

.docs-content h3 {
  margin: 24px 0 12px 0;
  font-size: 20px;
  font-weight: 600;
}

.docs-content p {
  margin: 16px 0;
}

.docs-content ul,
.docs-content ol {
  margin: 16px 0;
  padding-left: 24px;
}

.docs-content li {
  margin: 8px 0;
}

/* Code blocks */
pre {
  background-color: var(--bg-tertiary);
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius);
  padding: 16px;
  overflow-x: auto;
  font-family: var(--font-family-mono);
  font-size: 14px;
  line-height: 1.4;
}

code {
  font-family: var(--font-family-mono);
  font-size: 0.9em;
  background-color: var(--bg-tertiary);
  padding: 2px 6px;
  border-radius: 4px;
}

pre code {
  background: none;
  padding: 0;
}

/* Tables */
table {
  width: 100%;
  border-collapse: collapse;
  margin: 24px 0;
}

th,
td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid var(--border-color);
}

th {
  font-weight: 600;
  background-color: var(--bg-secondary);
}

/* Responsive design */
@media (max-width: 1024px) {
  .sidebar {
    position: fixed;
    left: -100%;
    transition: left 0.3s ease;
  }
  
  .sidebar.open {
    left: 0;
  }
  
  .main-content {
    max-width: 100%;
  }
  
  .content-wrapper {
    padding: 24px;
  }
}

@media (max-width: 768px) {
  .hero-title {
    font-size: 36px;
  }
  
  .hero-actions {
    flex-direction: column;
    align-items: center;
  }
  
  .feature-grid {
    grid-template-columns: 1fr;
  }
}
```

### 2. Syntax Highlighting CSS

Create `src/assets/css/syntax.css`:

```css
/* Syntax highlighting for code blocks */
.hljs {
  color: var(--text-primary);
  background: transparent;
}

.hljs-comment,
.hljs-quote {
  color: var(--text-tertiary);
  font-style: italic;
}

.hljs-keyword,
.hljs-selector-tag,
.hljs-addition {
  color: #c678dd;
}

.hljs-number,
.hljs-string,
.hljs-meta .hljs-meta-string,
.hljs-literal,
.hljs-doctag,
.hljs-regexp {
  color: #98c379;
}

.hljs-title,
.hljs-section,
.hljs-name,
.hljs-selector-id,
.hljs-selector-class {
  color: #e06c75;
}

.hljs-attribute,
.hljs-attr,
.hljs-variable,
.hljs-template-variable,
.hljs-class .hljs-title,
.hljs-type {
  color: #d19a66;
}

.hljs-symbol,
.hljs-bullet,
.hljs-subst,
.hljs-meta,
.hljs-meta .hljs-keyword,
.hljs-selector-attr,
.hljs-selector-pseudo,
.hljs-link {
  color: #61dafb;
}

.hljs-built_in,
.hljs-deletion {
  color: #e6c07b;
}
```

## Adding Interactive Features

### 1. JavaScript Functionality

Create `src/assets/js/main.js`:

```javascript
// Theme toggle
function initThemeToggle() {
  const toggle = document.querySelector('[data-theme-toggle]');
  const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
  const currentTheme = localStorage.getItem('theme') || (prefersDark ? 'dark' : 'light');
  
  document.documentElement.setAttribute('data-theme', currentTheme);
  
  toggle?.addEventListener('click', () => {
    const current = document.documentElement.getAttribute('data-theme');
    const next = current === 'dark' ? 'light' : 'dark';
    
    document.documentElement.setAttribute('data-theme', next);
    localStorage.setItem('theme', next);
  });
}

// Mobile menu
function initMobileMenu() {
  const toggle = document.querySelector('[data-mobile-menu-toggle]');
  const sidebar = document.querySelector('[data-sidebar]');
  
  toggle?.addEventListener('click', () => {
    sidebar?.classList.toggle('open');
  });
  
  // Close on outside click
  document.addEventListener('click', (e) => {
    if (!sidebar?.contains(e.target) && !toggle?.contains(e.target)) {
      sidebar?.classList.remove('open');
    }
  });
}

// Search functionality
function initSearch() {
  const trigger = document.querySelector('[data-search-open]');
  const modal = document.querySelector('[data-search-modal]');
  const input = document.querySelector('[data-search-input]');
  const close = document.querySelector('[data-search-close]');
  
  // Open search
  trigger?.addEventListener('click', () => {
    modal?.classList.add('open');
    input?.focus();
  });
  
  // Close search
  close?.addEventListener('click', () => {
    modal?.classList.remove('open');
  });
  
  // Keyboard shortcuts
  document.addEventListener('keydown', (e) => {
    if ((e.metaKey || e.ctrlKey) && e.key === 'k') {
      e.preventDefault();
      modal?.classList.add('open');
      input?.focus();
    }
    
    if (e.key === 'Escape') {
      modal?.classList.remove('open');
    }
  });
}

// Active navigation
function initActiveNav() {
  const links = document.querySelectorAll('.sidebar-link');
  const currentPath = window.location.pathname;
  
  links.forEach(link => {
    if (link.getAttribute('href') === currentPath) {
      link.classList.add('active');
    }
  });
}

// Copy code blocks
function initCodeCopy() {
  document.querySelectorAll('pre').forEach(pre => {
    const button = document.createElement('button');
    button.className = 'copy-code';
    button.textContent = 'Copy';
    
    button.addEventListener('click', async () => {
      const code = pre.querySelector('code')?.textContent || pre.textContent;
      await navigator.clipboard.writeText(code);
      
      button.textContent = 'Copied!';
      setTimeout(() => {
        button.textContent = 'Copy';
      }, 2000);
    });
    
    pre.style.position = 'relative';
    pre.appendChild(button);
  });
}

// Initialize everything
document.addEventListener('DOMContentLoaded', () => {
  initThemeToggle();
  initMobileMenu();
  initSearch();
  initActiveNav();
  initCodeCopy();
});
```

### 2. Search Modal Component

Create `src/.components/ui/search-modal.html`:

```html
<div class="search-modal" data-search-modal>
    <div class="search-backdrop" data-search-close></div>
    <div class="search-content">
        <div class="search-header">
            <input 
                type="text" 
                placeholder="Search documentation..." 
                class="search-input"
                data-search-input
            >
            <button class="search-close" data-search-close>
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <line x1="18" y1="6" x2="6" y2="18"></line>
                    <line x1="6" y1="6" x2="18" y2="18"></line>
                </svg>
            </button>
        </div>
        <div class="search-results" data-search-results>
            <div class="search-empty">
                <p>Start typing to search documentation...</p>
            </div>
        </div>
    </div>
</div>
```

## Building and Deploying

### 1. Build Configuration

Create a build script in `package.json`:

```json
{
  "name": "my-docs",
  "scripts": {
    "dev": "unify serve",
    "build": "unify build --pretty-urls --base-url https://yourdomain.com",
    "preview": "unify serve --port 8080 --dir dist"
  },
  "devDependencies": {
    "@fwdslsh/unify": "latest"
  }
}
```

### 2. GitHub Actions Deployment

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy Documentation

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
        uses: actions/checkout@v3

      - name: Setup Bun
        uses: oven-sh/setup-bun@v1

      - name: Install dependencies
        run: bun install

      - name: Build site
        run: bun run build

      - name: Upload build artifacts
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./dist

  deploy:
    if: github.ref == 'refs/heads/main'
    needs: build
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

### 3. Local Development

```bash
# Start development server
bun run dev

# Build for production
bun run build

# Preview production build
bun run preview
```

## Next Steps

Congratulations! You've built a professional documentation site with Unify. Here are some enhancements you can add:

### Advanced Features
- **Search integration**: Add Algolia DocSearch or local search
- **Versioning**: Support multiple documentation versions
- **Analytics**: Add Google Analytics or privacy-friendly alternatives
- **Comments**: Integrate Giscus or Disqus for page comments
- **PDF generation**: Export documentation as PDF

### Content Management
- **Content from CMS**: Integrate with headless CMS
- **API documentation**: Auto-generate from OpenAPI specs
- **Multi-language**: Add internationalization support

### Performance
- **Image optimization**: Add automatic image optimization
- **Service worker**: Implement offline support
- **CDN**: Use a CDN for faster global delivery

## Resources

- [Unify Documentation](https://github.com/fwdslsh/unify)
- [More Examples](../examples/unify/)
- [Deployment Guides](../integrations/)
- [Community Discussion](https://github.com/orgs/fwdslsh/discussions)

Happy documenting! 📚