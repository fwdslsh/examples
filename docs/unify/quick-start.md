# Unify Quick Start Guide

Unify is a modern, lightweight static site generator that brings the power of server-side includes, markdown processing, and live development to your workflow. This guide will get you building your first site in minutes.

## What Makes Unify Special?

- **Zero Learning Curve**: Uses familiar Apache SSI syntax or intuitive HTML elements
- **Lightning Fast**: Built with Bun for maximum performance
- **Live Development**: Built-in dev server with live reload
- **Component-Based**: Reusable components without framework complexity
- **SEO Optimized**: Automatic sitemap generation and clean URLs

## Prerequisites

- [Bun](https://bun.sh) installed on your system
- Basic knowledge of HTML and CSS

## Installation

```bash
# Install Bun if you haven't already
curl -fsSL https://bun.sh/install | bash

# Install Unify globally
bun add -g @fwdslsh/unify

# Verify installation
unify --version
```

## Creating Your First Site

### Method 1: Using the Starter Template

```bash
# Create a new site from template
npm create unify-site my-first-site
cd my-first-site

# Start development server
unify serve

# Your site is now live at http://localhost:3000
```

### Method 2: Manual Setup

```bash
# Create project directory
mkdir my-site && cd my-site

# Create basic structure
mkdir -p src/.components src/images src/styles
```

Create `src/index.html`:
```html
<!--#include virtual="/.components/header.html" -->
<main>
    <h1>Welcome to My Unify Site!</h1>
    <p>This is built with server-side includes.</p>
</main>
<!--#include virtual="/.components/footer.html" -->
```

Create `src/.components/header.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Unify Site</title>
    <link rel="stylesheet" href="/styles/main.css">
</head>
<body>
    <header>
        <nav>
            <a href="/">Home</a>
            <a href="/about">About</a>
            <a href="/contact">Contact</a>
        </nav>
    </header>
```

Create `src/.components/footer.html`:
```html
    <footer>
        <p>&copy; 2024 My Unify Site. Built with Unify.</p>
    </footer>
</body>
</html>
```

Create `src/styles/main.css`:
```css
/* Basic styling */
body {
    font-family: system-ui, sans-serif;
    line-height: 1.6;
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 20px;
}

header {
    border-bottom: 1px solid #eee;
    margin-bottom: 2rem;
}

nav a {
    margin-right: 1rem;
    text-decoration: none;
    color: #333;
}

nav a:hover {
    text-decoration: underline;
}

footer {
    margin-top: 2rem;
    padding-top: 2rem;
    border-top: 1px solid #eee;
    text-align: center;
    color: #666;
}
```

## Development Workflow

### Starting the Development Server

```bash
# Start with default settings
unify serve

# Custom port and host
unify serve --port 8080 --host 0.0.0.0

# Open browser automatically
unify serve --open
```

The development server provides:
- **Live reload**: Changes appear instantly
- **Error reporting**: Clear error messages in the browser
- **Asset serving**: Handles images, CSS, JS automatically

### Building for Production

```bash
# Basic build
unify build

# Build with pretty URLs (removes .html extensions)
unify build --pretty-urls

# Build with custom base URL
unify build --base-url https://mysite.com

# Build to custom output directory
unify build --output-dir dist-prod
```

## Working with Components

### Server-Side Includes (SSI) Syntax

The traditional Apache SSI syntax is fully supported:

```html
<!--#include virtual="/.components/header.html" -->
<!--#include file="sidebar.html" -->
```

### Modern Template Elements

Unify also supports more intuitive HTML elements:

```html
<include src="/.components/header.html"></include>
<template src="/.components/card.html"></template>
<slot name="content">Default content here</slot>
```

### Component Organization

Organize components in the `.components` directory:

```
src/
├── .components/
│   ├── header.html
│   ├── footer.html
│   ├── sidebar.html
│   └── cards/
│       ├── product-card.html
│       └── blog-card.html
├── pages/
│   ├── about.html
│   └── contact.html
├── styles/
└── images/
```

### Passing Data to Components

Components can accept parameters:

```html
<!-- In your page -->
<!--#set var="title" value="Welcome to My Site" -->
<!--#set var="description" value="A modern static site" -->
<!--#include virtual="/.components/meta.html" -->

<!-- In .components/meta.html -->
<title><!--#echo var="title" --></title>
<meta name="description" content="<!--#echo var="description" -->">
```

## Working with Markdown

### Markdown Files with Frontmatter

Create `src/blog/my-post.md`:

```markdown
---
title: My First Blog Post
date: 2024-01-15
author: Your Name
layout: blog-post
---

# My First Blog Post

This is a blog post written in Markdown with frontmatter.

## Features

- **Bold text** and *italic text*
- Code blocks with syntax highlighting
- Lists and tables

```javascript
console.log('Hello, Unify!');
```

The frontmatter variables are available in your layout templates.
```

### Blog Layout Template

Create `src/.components/layouts/blog-post.html`:

```html
<!--#include virtual="/.components/header.html" -->
<main>
    <article>
        <header>
            <h1><!--#echo var="title" --></h1>
            <p>By <!--#echo var="author" --> on <!--#echo var="date" --></p>
        </header>
        
        <div class="content">
            <!-- Markdown content will be inserted here -->
            <!--#include virtual="content" -->
        </div>
    </article>
</main>
<!--#include virtual="/.components/footer.html" -->
```

## Advanced Features

### Custom Variables and Logic

```html
<!--#set var="environment" value="production" -->
<!--#if expr="${environment} = production" -->
    <script src="/analytics.js"></script>
<!--#endif -->

<!--#config timefmt="%Y-%m-%d" -->
Last updated: <!--#echo var="LAST_MODIFIED" -->
```

### Asset Processing

Unify automatically processes:
- **Images**: Copies and optimizes
- **CSS**: Bundles and minifies in production
- **JavaScript**: Bundles ES modules
- **Fonts**: Includes web fonts

### SEO Features

```html
<!-- Automatic sitemap generation -->
<!-- Add to your header component -->
<link rel="sitemap" type="application/xml" href="/sitemap.xml">

<!-- Pretty URLs (removes .html extensions) -->
<!-- Use --pretty-urls flag when building -->
```

## Common Patterns

### Multi-language Sites

```
src/
├── en/
│   ├── index.html
│   └── about.html
├── es/
│   ├── index.html
│   └── about.html
└── .components/
    ├── nav-en.html
    └── nav-es.html
```

### Blog with Index

Create `src/blog/index.html`:

```html
<!--#include virtual="/.components/header.html" -->
<main>
    <h1>Blog</h1>
    <div class="posts">
        <!--#exec cmd="ls -1 *.md | head -10" -->
        <!-- Or use a script to generate post list -->
    </div>
</main>
<!--#include virtual="/.components/footer.html" -->
```

### Configuration-driven Navigation

Create `src/.components/nav.html`:

```html
<nav>
    <!--#set var="pages" value="Home:/:About:/about:Contact:/contact" -->
    <!--#exec cmd="echo $pages | tr ':' '\n' | xargs -n2 printf '<a href=\"%s\">%s</a>'" -->
</nav>
```

## Performance Tips

1. **Optimize Images**: Use modern formats (WebP, AVIF)
2. **Minimize CSS**: Use the `--minify` flag
3. **Lazy Load**: Implement lazy loading for images
4. **Cache Headers**: Configure your web server properly
5. **CDN**: Use a CDN for static assets

## Deployment

### GitHub Pages

```yaml
# .github/workflows/deploy.yml
name: Deploy to GitHub Pages
on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: oven-sh/setup-bun@v1
      - run: bun install
      - run: bun add @fwdslsh/unify
      - run: bunx unify build --pretty-urls --base-url https://yourusername.github.io/yourrepo
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

### Netlify

Create `netlify.toml`:

```toml
[build]
  command = "bun add @fwdslsh/unify && bunx unify build --pretty-urls"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 404
```

### Vercel

Create `vercel.json`:

```json
{
  "buildCommand": "bun add @fwdslsh/unify && bunx unify build --pretty-urls",
  "outputDirectory": "dist",
  "installCommand": "curl -fsSL https://bun.sh/install | bash && export PATH=\"$HOME/.bun/bin:$PATH\""
}
```

## Next Steps

- Read the [Complete User Guide](user-guide.md) for advanced features
- Explore [Examples](../examples/unify/) for real-world implementations
- Check out [Integration Guides](../integrations/) for specific platforms
- Join the [community discussions](https://github.com/orgs/fwdslsh/discussions)

## Troubleshooting

### Common Issues

**Build fails with "command not found"**
```bash
# Ensure Bun is in your PATH
export PATH="$HOME/.bun/bin:$PATH"
```

**Live reload not working**
```bash
# Try a different port
unify serve --port 3001
```

**Components not loading**
```bash
# Check file paths (use leading slash for absolute paths)
<!--#include virtual="/.components/header.html" -->
```

For more help, see the [Unify Troubleshooting Guide](../troubleshooting/unify.md).

Happy building! 🚀