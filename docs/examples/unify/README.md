# Basic Unify Site Examples

This directory contains simple, focused examples demonstrating specific Unify features and use cases.

## 📁 Example Categories

### Getting Started
- [Hello World](./hello-world/) - Minimal Unify site
- [Basic Components](./basic-components/) - Using includes and components
- [Multi-page Site](./multi-page/) - Navigation and multiple pages

### Features
- [Markdown Integration](./markdown/) - Using Markdown with frontmatter
- [Asset Handling](./assets/) - Images, CSS, and JavaScript
- [Dynamic Content](./dynamic/) - Using variables and conditions

### Layouts
- [Blog Layout](./blog/) - Blog-style site with posts
- [Documentation Layout](./docs/) - Documentation site structure
- [Portfolio Layout](./portfolio/) - Portfolio/showcase site

### Advanced
- [Custom SSI Directives](./custom-ssi/) - Advanced server-side includes
- [Multi-language](./i18n/) - Internationalization example
- [API Integration](./api/) - Fetching external data

## 🚀 Quick Start

Each example can be run independently:

```bash
# Clone or download an example
cd basic-components

# Install dependencies (if package.json exists)
npm install

# Start development server
unify serve

# Build for production
unify build
```

## 📖 Example Structure

Each example follows this structure:

```
example-name/
├── README.md          # Specific documentation
├── src/               # Source files
│   ├── index.html     # Homepage
│   ├── .components/   # Reusable components
│   └── assets/        # Static assets
├── package.json       # Dependencies (if any)
└── unify.config.js    # Configuration (if needed)
```

## 💡 Learning Path

Recommended order for beginners:

1. **[Hello World](./hello-world/)** - Understand basic Unify concepts
2. **[Basic Components](./basic-components/)** - Learn component system
3. **[Multi-page Site](./multi-page/)** - Build navigation
4. **[Markdown Integration](./markdown/)** - Add content management
5. **[Blog Layout](./blog/)** - Create dynamic content
6. **[Documentation Layout](./docs/)** - Build complex layouts

## 🔗 Related Resources

- [Unify Documentation](../../../unify/)
- [Tutorials](../../tutorials/)
- [Best Practices](../../best-practices/)
- [Integration Guides](../../integrations/)

---

# Hello World Example

The simplest possible Unify site.

## File Structure

```
hello-world/
├── src/
│   └── index.html
└── README.md
```

## Source Code

**src/index.html:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello Unify!</title>
    <style>
        body {
            font-family: system-ui, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 2rem;
            line-height: 1.6;
        }
        .highlight {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
    </style>
</head>
<body>
    <h1>Hello <span class="highlight">Unify</span>!</h1>
    <p>Welcome to your first Unify static site. This example demonstrates:</p>
    <ul>
        <li>Basic HTML structure</li>
        <li>Simple CSS styling</li>
        <li>Modern web standards</li>
    </ul>
    <p>Ready to build something amazing? Check out the <a href="https://github.com/fwdslsh/examples">other examples</a>!</p>
</body>
</html>
```

## Usage

```bash
# Start development server
unify serve

# Build for production
unify build
```

This creates the simplest possible Unify site - perfect for understanding the basics!

---

# Basic Components Example

Demonstrates Unify's component system using server-side includes.

## File Structure

```
basic-components/
├── src/
│   ├── index.html
│   ├── about.html
│   └── .components/
│       ├── header.html
│       ├── footer.html
│       └── navigation.html
└── README.md
```

## Source Code

**src/.components/header.html:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><!--#echo var="title" default="My Site" --></title>
    <style>
        body {
            font-family: system-ui, sans-serif;
            margin: 0;
            line-height: 1.6;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 0 2rem;
        }
        header {
            background: #f8fafc;
            border-bottom: 1px solid #e2e8f0;
            padding: 1rem 0;
        }
        .logo {
            font-size: 1.5rem;
            font-weight: 700;
            color: #1e293b;
            text-decoration: none;
        }
    </style>
</head>
<body>
    <header>
        <div class="container">
            <a href="/" class="logo">My Site</a>
        </div>
    </header>
```

**src/.components/navigation.html:**
```html
<nav style="background: #1e293b; color: white; padding: 1rem 0;">
    <div class="container">
        <ul style="list-style: none; margin: 0; padding: 0; display: flex; gap: 2rem;">
            <li><a href="/" style="color: white; text-decoration: none;">Home</a></li>
            <li><a href="/about" style="color: white; text-decoration: none;">About</a></li>
            <li><a href="/contact" style="color: white; text-decoration: none;">Contact</a></li>
        </ul>
    </div>
</nav>
```

**src/.components/footer.html:**
```html
    <footer style="background: #f8fafc; border-top: 1px solid #e2e8f0; margin-top: 4rem; padding: 2rem 0;">
        <div class="container">
            <p>&copy; 2024 My Site. Built with <a href="https://github.com/fwdslsh/unify">Unify</a>.</p>
        </div>
    </footer>
</body>
</html>
```

**src/index.html:**
```html
<!--#set var="title" value="Home - My Site" -->
<!--#include virtual="/.components/header.html" -->
<!--#include virtual="/.components/navigation.html" -->

<main>
    <div class="container" style="padding: 4rem 0;">
        <h1>Welcome to My Site</h1>
        <p>This example demonstrates Unify's component system using server-side includes (SSI).</p>
        
        <h2>Features Demonstrated</h2>
        <ul>
            <li><strong>Header component</strong> - Shared HTML head and site header</li>
            <li><strong>Navigation component</strong> - Reusable navigation menu</li>
            <li><strong>Footer component</strong> - Shared footer across pages</li>
            <li><strong>Variables</strong> - Setting page title dynamically</li>
        </ul>
        
        <p><a href="/about">Learn more about this example →</a></p>
    </div>
</main>

<!--#include virtual="/.components/footer.html" -->
```

**src/about.html:**
```html
<!--#set var="title" value="About - My Site" -->
<!--#include virtual="/.components/header.html" -->
<!--#include virtual="/.components/navigation.html" -->

<main>
    <div class="container" style="padding: 4rem 0;">
        <h1>About This Example</h1>
        <p>This page shares the same header, navigation, and footer components as the homepage.</p>
        
        <h2>Component Benefits</h2>
        <ul>
            <li><strong>Maintainability</strong> - Update header once, changes appear everywhere</li>
            <li><strong>Consistency</strong> - Shared components ensure uniform design</li>
            <li><strong>Efficiency</strong> - No need to copy/paste common elements</li>
            <li><strong>Flexibility</strong> - Easy to customize per page with variables</li>
        </ul>
        
        <h2>SSI Syntax</h2>
        <p>Unify supports standard Apache SSI syntax:</p>
        <pre><code>&lt;!--#include virtual="/.components/header.html" --&gt;
&lt;!--#set var="title" value="Page Title" --&gt;
&lt;!--#echo var="title" default="Default Title" --&gt;</code></pre>
        
        <p><a href="/">← Back to homepage</a></p>
    </div>
</main>

<!--#include virtual="/.components/footer.html" -->
```

## Key Concepts

1. **Components** - Reusable HTML fragments in `.components/` directory
2. **Variables** - Set and echo variables using SSI directives
3. **Includes** - Pull in components with `<!--#include virtual="path" -->`
4. **File paths** - Use absolute paths from `src/` root (leading slash)

## Usage

```bash
# Start development server
unify serve

# Build for production  
unify build
```

The build process resolves all includes and generates static HTML files with no dependencies.

---

# Markdown Integration Example

Shows how to use Markdown files with frontmatter in Unify.

## File Structure

```
markdown/
├── src/
│   ├── index.html
│   ├── blog/
│   │   ├── index.html
│   │   ├── post-1.md
│   │   └── post-2.md
│   └── .components/
│       ├── layout.html
│       └── blog-post.html
└── README.md
```

## Source Code

**src/.components/layout.html:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><!--#echo var="title" default="My Blog" --></title>
    <meta name="description" content="<!--#echo var="description" default="A blog built with Unify" -->">
    <style>
        body {
            font-family: system-ui, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 2rem;
            line-height: 1.7;
        }
        header {
            border-bottom: 2px solid #e2e8f0;
            margin-bottom: 2rem;
            padding-bottom: 1rem;
        }
        .meta {
            color: #6b7280;
            font-size: 0.9rem;
            margin: 1rem 0;
        }
        pre {
            background: #f8fafc;
            padding: 1rem;
            border-radius: 0.5rem;
            overflow-x: auto;
        }
        code {
            background: #f8fafc;
            padding: 0.2rem 0.4rem;
            border-radius: 0.25rem;
            font-size: 0.9em;
        }
        pre code {
            background: none;
            padding: 0;
        }
    </style>
</head>
<body>
    <header>
        <h1><a href="/" style="color: inherit; text-decoration: none;">My Blog</a></h1>
        <nav>
            <a href="/">Home</a> |
            <a href="/blog">Blog</a>
        </nav>
    </header>
    
    <main>
        <!--#echo var="content" -->
    </main>
    
    <footer style="margin-top: 4rem; padding-top: 2rem; border-top: 1px solid #e2e8f0; color: #6b7280;">
        <p>Built with Unify</p>
    </footer>
</body>
</html>
```

**src/.components/blog-post.html:**
```html
<!--#set var="content" value="
<article>
    <header>
        <h1><!--#echo var='title' --></h1>
        <div class='meta'>
            <time datetime='<!--#echo var=\"date\" -->'><!--#echo var='date' --></time>
            <!--#if expr='${author}' -->
            | By <!--#echo var='author' -->
            <!--#endif -->
            <!--#if expr='${tags}' -->
            | Tags: <!--#echo var='tags' -->
            <!--#endif -->
        </div>
    </header>
    
    <div class='content'>
        <!--#echo var='markdown_content' -->
    </div>
    
    <footer>
        <p><a href='/blog'>← Back to blog</a></p>
    </footer>
</article>
" -->
<!--#include virtual="/.components/layout.html" -->
```

**src/blog/post-1.md:**
```markdown
---
title: Getting Started with Unify
date: 2024-01-15
author: Jane Developer
tags: unify, static-sites, tutorial
description: Learn how to build your first static site with Unify
layout: blog-post
---

# Getting Started with Unify

Welcome to the world of static site generation with Unify! This post will guide you through creating your first site.

## What is Unify?

Unify is a modern static site generator that uses familiar server-side include syntax to create maintainable websites. Key features include:

- **Zero learning curve** - Uses standard SSI syntax
- **Live development** - Built-in dev server with hot reload
- **Component-based** - Reusable HTML components
- **Markdown support** - Write content in Markdown

## Your First Site

Creating a new site is simple:

```bash
# Create new project
mkdir my-site && cd my-site

# Create basic structure
mkdir -p src/.components

# Create index page
echo '<h1>Hello Unify!</h1>' > src/index.html

# Start development server
unify serve
```

## Next Steps

- [Read the documentation](https://github.com/fwdslsh/unify)
- [Explore examples](https://github.com/fwdslsh/examples)
- [Join the community](https://github.com/orgs/fwdslsh/discussions)

Happy building! 🚀
```

**src/blog/post-2.md:**
```markdown
---
title: Advanced Unify Features
date: 2024-01-20
author: John Builder
tags: unify, advanced, components
description: Explore advanced features and patterns in Unify
layout: blog-post
---

# Advanced Unify Features

Once you've mastered the basics, Unify offers powerful features for complex sites.

## Dynamic Content

Use SSI variables and conditions for dynamic behavior:

```html
<!--#set var="environment" value="production" -->
<!--#if expr="${environment} = production" -->
    <script src="/analytics.js"></script>
<!--#endif -->

<!--#config timefmt="%Y-%m-%d" -->
Last updated: <!--#echo var="LAST_MODIFIED" -->
```

## Component Composition

Build complex layouts by composing components:

```html
<!--#include virtual="/.components/layout/base.html" -->
<!--#include virtual="/.components/sections/hero.html" -->
<!--#include virtual="/.components/sections/features.html" -->
<!--#include virtual="/.components/layout/footer.html" -->
```

## Asset Processing

Unify automatically handles:

- CSS bundling and minification
- JavaScript module resolution
- Image optimization
- Font loading

## Production Builds

Optimize for production:

```bash
# Build with optimizations
unify build --minify --optimize-images

# Build with pretty URLs
unify build --pretty-urls

# Build for specific base URL
unify build --base-url https://yourdomain.com
```

These features make Unify perfect for everything from simple blogs to complex documentation sites.
```

**src/blog/index.html:**
```html
<!--#set var="title" value="Blog - My Site" -->
<!--#set var="content" value="
<h1>Blog Posts</h1>
<p>Welcome to my blog! Here are my latest posts:</p>

<article style='border: 1px solid #e2e8f0; border-radius: 0.5rem; padding: 1.5rem; margin: 1.5rem 0;'>
    <h2><a href='/blog/post-1' style='color: inherit; text-decoration: none;'>Getting Started with Unify</a></h2>
    <div class='meta'>January 15, 2024 | By Jane Developer</div>
    <p>Learn how to build your first static site with Unify...</p>
    <a href='/blog/post-1'>Read more →</a>
</article>

<article style='border: 1px solid #e2e8f0; border-radius: 0.5rem; padding: 1.5rem; margin: 1.5rem 0;'>
    <h2><a href='/blog/post-2' style='color: inherit; text-decoration: none;'>Advanced Unify Features</a></h2>
    <div class='meta'>January 20, 2024 | By John Builder</div>
    <p>Explore advanced features and patterns in Unify...</p>
    <a href='/blog/post-2'>Read more →</a>
</article>
" -->
<!--#include virtual="/.components/layout.html" -->
```

**src/index.html:**
```html
<!--#set var="title" value="Home - My Blog" -->
<!--#set var="content" value="
<h1>Welcome to My Blog</h1>
<p>This site demonstrates Unify's Markdown integration with frontmatter.</p>

<h2>Features Demonstrated</h2>
<ul>
    <li><strong>Markdown files</strong> - Write content in Markdown format</li>
    <li><strong>Frontmatter</strong> - YAML metadata in Markdown files</li>
    <li><strong>Layout templates</strong> - Shared layouts for blog posts</li>
    <li><strong>Dynamic variables</strong> - Access frontmatter in templates</li>
</ul>

<p><a href='/blog'>View all blog posts →</a></p>

<h2>How It Works</h2>
<p>Markdown files with frontmatter are automatically processed:</p>
<ol>
    <li>Frontmatter variables become SSI variables</li>
    <li>Markdown content is converted to HTML</li>
    <li>Layout template is applied</li>
    <li>Final HTML is generated</li>
</ol>
" -->
<!--#include virtual="/.components/layout.html" -->
```

## Key Features

1. **Frontmatter** - YAML metadata at the top of Markdown files
2. **Layout templates** - Specify layout in frontmatter
3. **Variable access** - Use frontmatter variables in templates
4. **Content separation** - Keep content separate from presentation

## Usage

```bash
# Start development server
unify serve

# Build for production
unify build
```

Markdown files are automatically processed and converted to HTML using the specified layout template.