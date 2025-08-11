# Building a Blog with Unify

Learn how to create a modern, fast blog using Unify's powerful static site generation capabilities. This comprehensive tutorial covers everything from basic setup to advanced features like RSS feeds, SEO optimization, and dynamic content.

## What You'll Build

By the end of this tutorial, you'll have:
- A fully functional blog with posts, categories, and tags
- Automatic RSS feed generation
- SEO-optimized pages with meta tags and structured data
- Responsive design that works on all devices
- Fast loading times with optimized assets
- Comment system integration (optional)
- Search functionality
- Archive pages and pagination

## Prerequisites

- Basic knowledge of HTML and CSS
- [Unify installed](../unify/quick-start.md) or [fwdslsh Toolkit](../dev-environment-setup.md)
- Text editor or IDE (VS Code recommended)
- 45-60 minutes

## Step 1: Project Setup

### Create Your Blog Project

```bash
# Create a new Unify site with blog template
npm create unify-site my-blog -- --template blog
cd my-blog

# Or start from scratch
npm create unify-site my-blog
cd my-blog
```

### Project Structure Overview

```
my-blog/
├── src/
│   ├── .components/         # Reusable components
│   │   ├── layout.html      # Main page layout
│   │   ├── header.html      # Site header
│   │   ├── footer.html      # Site footer
│   │   ├── post-card.html   # Blog post preview
│   │   └── post-layout.html # Individual post layout
│   ├── posts/               # Blog posts (Markdown)
│   │   ├── 2024-01-01-first-post.md
│   │   └── 2024-01-15-second-post.md
│   ├── assets/              # Images, CSS, JS
│   │   ├── styles.css
│   │   └── images/
│   ├── index.html           # Homepage
│   ├── blog.html            # Blog listing page
│   └── about.html           # About page
├── unify.config.js          # Unify configuration
└── package.json
```

## Step 2: Configure Your Blog

### Update unify.config.js

```javascript
export default {
  build: {
    baseUrl: process.env.BASE_URL || '/',
    outputDir: 'dist',
    prettyUrls: true,
    generateSitemap: true
  },
  
  blog: {
    postsDir: 'src/posts',
    postsPerPage: 6,
    generateRSS: true,
    rssTitle: 'My Amazing Blog',
    rssDescription: 'Thoughts, tutorials, and insights',
    authorName: 'Your Name',
    authorEmail: 'your.email@example.com'
  },
  
  seo: {
    defaultTitle: 'My Amazing Blog',
    defaultDescription: 'A blog about web development, technology, and life',
    defaultImage: '/assets/images/og-image.jpg',
    twitterHandle: '@yourusername'
  }
}
```

### Set Up Site Metadata

Create `src/site-data.json`:

```json
{
  "site": {
    "title": "My Amazing Blog",
    "description": "Thoughts, tutorials, and insights on web development",
    "url": "https://yourdomain.com",
    "author": {
      "name": "Your Name",
      "email": "your.email@example.com",
      "twitter": "@yourusername",
      "github": "yourusername"
    }
  },
  "navigation": [
    { "title": "Home", "url": "/" },
    { "title": "Blog", "url": "/blog" },
    { "title": "About", "url": "/about" },
    { "title": "Contact", "url": "/contact" }
  ],
  "social": [
    { "platform": "Twitter", "url": "https://twitter.com/yourusername" },
    { "platform": "GitHub", "url": "https://github.com/yourusername" }
  ]
}
```

## Step 3: Create the Main Layout

### src/.components/layout.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    
    <!-- Dynamic title and meta tags -->
    <title>{{ page.title ? page.title + ' | ' + site.title : site.title }}</title>
    <meta name="description" content="{{ page.description || site.description }}">
    
    <!-- Open Graph / Facebook -->
    <meta property="og:type" content="{{ page.type || 'website' }}">
    <meta property="og:url" content="{{ site.url }}{{ page.url }}">
    <meta property="og:title" content="{{ page.title || site.title }}">
    <meta property="og:description" content="{{ page.description || site.description }}">
    <meta property="og:image" content="{{ page.image || site.url + '/assets/images/og-image.jpg' }}">

    <!-- Twitter -->
    <meta property="twitter:card" content="summary_large_image">
    <meta property="twitter:url" content="{{ site.url }}{{ page.url }}">
    <meta property="twitter:title" content="{{ page.title || site.title }}">
    <meta property="twitter:description" content="{{ page.description || site.description }}">
    <meta property="twitter:image" content="{{ page.image || site.url + '/assets/images/og-image.jpg' }}">

    <!-- RSS Feed -->
    <link rel="alternate" type="application/rss+xml" title="{{ site.title }}" href="/rss.xml">
    
    <!-- Favicon -->
    <link rel="icon" type="image/x-icon" href="/assets/images/favicon.ico">
    
    <!-- Stylesheet -->
    <link rel="stylesheet" href="/assets/styles.css">
    
    <!-- Structured Data for SEO -->
    {{ if page.type === 'article' }}
    <script type="application/ld+json">
    {
      "@context": "https://schema.org",
      "@type": "BlogPosting",
      "headline": "{{ page.title }}",
      "image": "{{ page.image || site.url + '/assets/images/og-image.jpg' }}",
      "author": {
        "@type": "Person",
        "name": "{{ site.author.name }}"
      },
      "publisher": {
        "@type": "Organization",
        "name": "{{ site.title }}",
        "logo": {
          "@type": "ImageObject",
          "url": "{{ site.url }}/assets/images/logo.png"
        }
      },
      "datePublished": "{{ page.date }}",
      "dateModified": "{{ page.modified || page.date }}"
    }
    </script>
    {{ endif }}
</head>
<body>
    <!-- Header -->
    <!-- @include .components/header.html -->
    
    <!-- Main Content -->
    <main>
        <!-- @slot -->
        <p>Default content</p>
        <!-- @endslot -->
    </main>
    
    <!-- Footer -->
    <!-- @include .components/footer.html -->
    
    <!-- Optional: Analytics -->
    {{ if process.env.NODE_ENV === 'production' }}
    <!-- Add your analytics code here -->
    {{ endif }}
</body>
</html>
```

### src/.components/header.html

```html
<header class="site-header">
    <div class="container">
        <div class="header-content">
            <div class="site-branding">
                <h1 class="site-title">
                    <a href="/">{{ site.title }}</a>
                </h1>
                <p class="site-description">{{ site.description }}</p>
            </div>
            
            <nav class="main-navigation">
                <ul class="nav-menu">
                    {{ for item in navigation }}
                    <li><a href="{{ item.url }}" {{ if page.url === item.url }}class="current"{{ endif }}>{{ item.title }}</a></li>
                    {{ endfor }}
                </ul>
            </nav>
            
            <!-- Mobile menu toggle -->
            <button class="menu-toggle" onclick="toggleMobileMenu()">
                <span></span>
                <span></span>
                <span></span>
            </button>
        </div>
    </div>
</header>

<script>
function toggleMobileMenu() {
    const nav = document.querySelector('.main-navigation');
    nav.classList.toggle('active');
}
</script>
```

## Step 4: Create Blog Components

### src/.components/post-card.html

```html
<article class="post-card">
    <header class="post-header">
        {{ if post.featured_image }}
        <div class="post-image">
            <img src="{{ post.featured_image }}" alt="{{ post.title }}" loading="lazy">
        </div>
        {{ endif }}
        
        <h2 class="post-title">
            <a href="{{ post.url }}">{{ post.title }}</a>
        </h2>
        
        <div class="post-meta">
            <time datetime="{{ post.date }}">{{ post.date | formatDate }}</time>
            {{ if post.category }}
            <span class="post-category">
                <a href="/category/{{ post.category | slug }}">{{ post.category }}</a>
            </span>
            {{ endif }}
            <span class="reading-time">{{ post.reading_time || '5' }} min read</span>
        </div>
    </header>
    
    <div class="post-excerpt">
        {{ post.excerpt || post.content | truncate(150) }}
    </div>
    
    {{ if post.tags }}
    <footer class="post-tags">
        {{ for tag in post.tags }}
        <a href="/tag/{{ tag | slug }}" class="tag">{{ tag }}</a>
        {{ endfor }}
    </footer>
    {{ endif }}
</article>
```

### src/.components/post-layout.html

```html
<!-- @include .components/layout.html -->

<article class="blog-post">
    <header class="post-header">
        {{ if page.featured_image }}
        <div class="featured-image">
            <img src="{{ page.featured_image }}" alt="{{ page.title }}">
        </div>
        {{ endif }}
        
        <h1 class="post-title">{{ page.title }}</h1>
        
        <div class="post-meta">
            <div class="author-info">
                {{ if page.author_image }}
                <img src="{{ page.author_image }}" alt="{{ page.author || site.author.name }}" class="author-avatar">
                {{ endif }}
                <div>
                    <span class="author-name">{{ page.author || site.author.name }}</span>
                    <time datetime="{{ page.date }}" class="post-date">{{ page.date | formatDate }}</time>
                </div>
            </div>
            
            <div class="post-stats">
                <span class="reading-time">{{ page.reading_time || '5' }} min read</span>
                {{ if page.category }}
                <span class="post-category">
                    <a href="/category/{{ page.category | slug }}">{{ page.category }}</a>
                </span>
                {{ endif }}
            </div>
        </div>
        
        {{ if page.tags }}
        <div class="post-tags">
            {{ for tag in page.tags }}
            <a href="/tag/{{ tag | slug }}" class="tag">{{ tag }}</a>
            {{ endfor }}
        </div>
        {{ endif }}
    </header>
    
    <div class="post-content">
        <!-- @slot -->
        {{ content }}
        <!-- @endslot -->
    </div>
    
    <footer class="post-footer">
        <div class="post-navigation">
            {{ if previousPost }}
            <div class="nav-previous">
                <span>Previous Post</span>
                <a href="{{ previousPost.url }}">{{ previousPost.title }}</a>
            </div>
            {{ endif }}
            
            {{ if nextPost }}
            <div class="nav-next">
                <span>Next Post</span>
                <a href="{{ nextPost.url }}">{{ nextPost.title }}</a>
            </div>
            {{ endif }}
        </div>
        
        <!-- Social sharing -->
        <div class="post-sharing">
            <h3>Share this post</h3>
            <div class="sharing-buttons">
                <a href="https://twitter.com/intent/tweet?text={{ page.title | urlencode }}&url={{ site.url }}{{ page.url | urlencode }}" target="_blank" rel="noopener">
                    Twitter
                </a>
                <a href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url | urlencode }}" target="_blank" rel="noopener">
                    Facebook
                </a>
                <a href="https://www.linkedin.com/sharing/share-offsite/?url={{ site.url }}{{ page.url | urlencode }}" target="_blank" rel="noopener">
                    LinkedIn
                </a>
            </div>
        </div>
    </footer>
</article>

<!-- @endinclude -->
```

## Step 5: Create Blog Posts

### Writing Your First Post

Create `src/posts/2024-01-01-welcome-to-my-blog.md`:

```markdown
---
title: "Welcome to My Blog!"
date: "2024-01-01"
author: "Your Name"
category: "General"
tags: ["welcome", "introduction", "blogging"]
featured_image: "/assets/images/welcome-post.jpg"
excerpt: "Welcome to my new blog! Learn about what I'll be writing about and why I started this journey."
description: "An introduction to my new blog, covering topics I'll write about and my motivation for starting."
reading_time: 3
---

# Welcome to My Blog!

Hello and welcome to my new blog! I'm excited to share this space with you where I'll be writing about web development, technology trends, and personal insights.

## What You Can Expect

I'll be covering a variety of topics:

### Web Development
- Modern JavaScript frameworks and libraries
- CSS tricks and best practices
- Performance optimization techniques
- Accessibility guidelines

### Technology Insights
- Industry trends and analysis
- Tool reviews and comparisons
- Personal project showcases
- Learning resources and tutorials

### Personal Growth
- Career development tips
- Learning strategies
- Work-life balance
- Community involvement

## Why I Started This Blog

Writing has always been a passion of mine, and I believe that sharing knowledge helps both the writer and the reader grow. This blog serves as:

1. **A learning tool** - Writing about topics forces me to understand them deeply
2. **A reference** - I can look back at my own posts when I need reminders
3. **A community builder** - Connecting with like-minded developers and learners
4. **A creative outlet** - Expressing ideas and experiences beyond just code

## What's Coming Next

I have several exciting posts planned:

- A deep dive into modern CSS Grid techniques
- Building accessible web components
- My experience with [insert relevant technology]
- A guide to setting up an efficient development workflow

## Get in Touch

I'd love to hear from you! Feel free to:

- Comment on posts (when I enable comments)
- Reach out on [Twitter](https://twitter.com/yourusername)
- Connect on [LinkedIn](https://linkedin.com/in/yourprofile)
- Check out my projects on [GitHub](https://github.com/yourusername)

Thank you for reading, and I hope you'll join me on this journey!

---

*This is the first post on my blog built with [Unify](https://github.com/fwdslsh/unify). The source code for this blog is available on [GitHub](https://github.com/yourusername/blog-source).*
```

### Create More Sample Posts

Create `src/posts/2024-01-15-getting-started-with-unify.md`:

```markdown
---
title: "Getting Started with Unify: A Developer's Perspective"
date: "2024-01-15"
author: "Your Name"
category: "Web Development"
tags: ["unify", "static-site-generator", "tutorial", "web-development"]
featured_image: "/assets/images/unify-tutorial.jpg"
excerpt: "My experience learning Unify and why it's become my go-to static site generator."
description: "A comprehensive guide to getting started with Unify static site generator, including setup, configuration, and best practices."
reading_time: 8
---

# Getting Started with Unify: A Developer's Perspective

After trying numerous static site generators over the years, I recently discovered [Unify](https://github.com/fwdslsh/unify) and it has quickly become my favorite tool for building fast, maintainable websites.

## Why Unify Stands Out

### Zero Learning Curve
Unlike other static site generators that require learning new templating languages, Unify works with plain HTML. You can start building immediately without studying documentation.

### Lightning Fast
Built on [Bun](https://bun.sh), Unify generates sites incredibly quickly. My 100-page documentation site builds in under 2 seconds.

### Component-Based
The include system with slots makes creating reusable components intuitive:

```html
<!-- @include .components/card.html title="My Title" -->
<p>This content goes in the slot</p>
<!-- @endinclude -->
```

## Setting Up Your First Site

Getting started is refreshingly simple:

```bash
# Create a new site
npm create unify-site my-site
cd my-site

# Start developing
bun run dev
```

That's it! Your site is running at `http://localhost:3000` with hot reload enabled.

## Project Structure

Unify's structure is intuitive:

```
src/
├── .components/     # Reusable components
├── assets/          # CSS, JS, images
├── pages/           # Your website pages
└── index.html       # Homepage
```

## Advanced Features I Love

### Live Reload
Changes appear instantly in the browser. No build step during development.

### Pretty URLs
Clean URLs without file extensions: `/about` instead of `/about.html`.

### Component Slots
Create flexible, reusable components with content slots.

### Asset Optimization
Automatic optimization of images, CSS, and JavaScript.

## Real-World Example

Here's how I structure a typical blog post component:

```html
<!-- .components/blog-post.html -->
<article class="blog-post">
    <header>
        <h1>{{ title }}</h1>
        <time>{{ date }}</time>
    </header>
    
    <div class="content">
        <!-- @slot -->
        <!-- Post content goes here -->
        <!-- @endslot -->
    </div>
</article>
```

## Performance Results

Since switching to Unify:
- **Build time**: 10s → 2s (80% faster)
- **Page load**: 1.2s → 0.4s (Lighthouse score: 100)
- **Development experience**: Significantly improved

## Next Steps

If you're interested in trying Unify:

1. Check out the [official documentation](https://github.com/fwdslsh/unify)
2. Try the [5-minute tutorial](../unify/5-minute-tutorial.md)
3. Explore [example sites](https://github.com/fwdslsh/unify-examples)

## Conclusion

Unify has streamlined my development workflow while producing faster, more maintainable sites. The combination of simplicity and power makes it perfect for everything from personal blogs to complex documentation sites.

Have you tried Unify? I'd love to hear about your experience in the comments!
```

## Step 6: Create Blog Listing Pages

### src/blog.html

```html
<!-- @include .components/layout.html -->

<div class="blog-page">
    <header class="page-header">
        <h1>Blog</h1>
        <p>Thoughts, tutorials, and insights on web development and technology.</p>
    </header>
    
    <div class="blog-grid">
        {{ for post in posts | limit(postsPerPage, currentPage) }}
        <!-- @include .components/post-card.html post=post -->
        {{ endfor }}
    </div>
    
    <!-- Pagination -->
    {{ if totalPages > 1 }}
    <nav class="pagination">
        {{ if currentPage > 1 }}
        <a href="/blog/page/{{ currentPage - 1 }}" class="pagination-link prev">Previous</a>
        {{ endif }}
        
        {{ for page in pagination }}
        <a href="{{ page.url }}" class="pagination-link {{ page.current ? 'current' : '' }}">{{ page.number }}</a>
        {{ endfor }}
        
        {{ if currentPage < totalPages }}
        <a href="/blog/page/{{ currentPage + 1 }}" class="pagination-link next">Next</a>
        {{ endif }}
    </nav>
    {{ endif }}
    
    <!-- Categories and Tags Sidebar -->
    <aside class="blog-sidebar">
        <div class="widget">
            <h3>Categories</h3>
            <ul class="category-list">
                {{ for category in categories }}
                <li>
                    <a href="/category/{{ category.slug }}">{{ category.name }}</a>
                    <span class="count">({{ category.count }})</span>
                </li>
                {{ endfor }}
            </ul>
        </div>
        
        <div class="widget">
            <h3>Popular Tags</h3>
            <div class="tag-cloud">
                {{ for tag in popularTags }}
                <a href="/tag/{{ tag.slug }}" class="tag" style="font-size: {{ tag.weight }}em">{{ tag.name }}</a>
                {{ endfor }}
            </div>
        </div>
        
        <div class="widget">
            <h3>Recent Posts</h3>
            <ul class="recent-posts">
                {{ for post in recentPosts | limit(5) }}
                <li>
                    <a href="{{ post.url }}">{{ post.title }}</a>
                    <time>{{ post.date | formatDate }}</time>
                </li>
                {{ endfor }}
            </ul>
        </div>
    </aside>
</div>

<!-- @endinclude -->
```

## Step 7: Add Styling

### src/assets/styles.css

```css
/* CSS Variables for consistent theming */
:root {
  --primary-color: #2563eb;
  --primary-dark: #1e40af;
  --text-color: #1f2937;
  --text-light: #6b7280;
  --bg-color: #ffffff;
  --bg-light: #f9fafb;
  --border-color: #e5e7eb;
  --shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1);
  --border-radius: 0.5rem;
  --font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
  --font-mono: 'SF Mono', Monaco, 'Cascadia Code', 'Roboto Mono', monospace;
}

/* Reset and base styles */
* {
  box-sizing: border-box;
}

body {
  font-family: var(--font-family);
  line-height: 1.6;
  color: var(--text-color);
  background-color: var(--bg-color);
  margin: 0;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}

/* Header styles */
.site-header {
  background: var(--bg-color);
  border-bottom: 1px solid var(--border-color);
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 0;
}

.site-title a {
  text-decoration: none;
  color: var(--text-color);
  font-size: 1.5rem;
  font-weight: bold;
}

.site-description {
  color: var(--text-light);
  margin: 0;
  font-size: 0.9rem;
}

.main-navigation ul {
  display: flex;
  list-style: none;
  margin: 0;
  padding: 0;
  gap: 2rem;
}

.main-navigation a {
  text-decoration: none;
  color: var(--text-color);
  padding: 0.5rem 0;
  border-bottom: 2px solid transparent;
  transition: border-color 0.2s;
}

.main-navigation a:hover,
.main-navigation a.current {
  border-bottom-color: var(--primary-color);
}

/* Mobile menu */
.menu-toggle {
  display: none;
  flex-direction: column;
  background: none;
  border: none;
  cursor: pointer;
}

.menu-toggle span {
  width: 25px;
  height: 3px;
  background: var(--text-color);
  margin: 3px 0;
  transition: 0.3s;
}

/* Blog post styles */
.blog-page {
  padding: 2rem 0;
  display: grid;
  grid-template-columns: 1fr 300px;
  gap: 3rem;
  max-width: 1200px;
  margin: 0 auto;
}

.page-header {
  grid-column: 1 / -1;
  text-align: center;
  margin-bottom: 2rem;
}

.page-header h1 {
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
}

.blog-grid {
  display: grid;
  gap: 2rem;
}

.post-card {
  background: var(--bg-color);
  border-radius: var(--border-radius);
  box-shadow: var(--shadow);
  overflow: hidden;
  transition: transform 0.2s, box-shadow 0.2s;
}

.post-card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}

.post-image img {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.post-header {
  padding: 1.5rem;
}

.post-title {
  margin: 0 0 1rem 0;
}

.post-title a {
  text-decoration: none;
  color: var(--text-color);
}

.post-title a:hover {
  color: var(--primary-color);
}

.post-meta {
  display: flex;
  align-items: center;
  gap: 1rem;
  font-size: 0.9rem;
  color: var(--text-light);
  margin-bottom: 1rem;
}

.post-category a {
  background: var(--primary-color);
  color: white;
  padding: 0.25rem 0.5rem;
  border-radius: 0.25rem;
  text-decoration: none;
  font-size: 0.8rem;
}

.post-excerpt {
  padding: 0 1.5rem;
  color: var(--text-light);
}

.post-tags {
  padding: 1rem 1.5rem;
  border-top: 1px solid var(--border-color);
}

.tag {
  display: inline-block;
  background: var(--bg-light);
  color: var(--text-color);
  padding: 0.25rem 0.5rem;
  margin: 0.25rem 0.25rem 0.25rem 0;
  border-radius: 0.25rem;
  text-decoration: none;
  font-size: 0.8rem;
  transition: background-color 0.2s;
}

.tag:hover {
  background: var(--border-color);
}

/* Individual blog post */
.blog-post {
  max-width: 800px;
  margin: 2rem auto;
  padding: 0 1rem;
}

.featured-image img {
  width: 100%;
  height: 400px;
  object-fit: cover;
  border-radius: var(--border-radius);
  margin-bottom: 2rem;
}

.blog-post .post-title {
  font-size: 2.5rem;
  margin-bottom: 1rem;
}

.author-info {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-bottom: 2rem;
}

.author-avatar {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  object-fit: cover;
}

.post-content {
  font-size: 1.1rem;
  line-height: 1.8;
}

.post-content h2,
.post-content h3,
.post-content h4 {
  margin-top: 2rem;
  margin-bottom: 1rem;
}

.post-content pre {
  background: var(--bg-light);
  padding: 1rem;
  border-radius: var(--border-radius);
  overflow-x: auto;
  font-family: var(--font-mono);
}

.post-content blockquote {
  border-left: 4px solid var(--primary-color);
  background: var(--bg-light);
  margin: 1.5rem 0;
  padding: 1rem 1.5rem;
  font-style: italic;
}

/* Sidebar */
.blog-sidebar {
  padding: 1rem;
}

.widget {
  background: var(--bg-light);
  padding: 1.5rem;
  border-radius: var(--border-radius);
  margin-bottom: 2rem;
}

.widget h3 {
  margin-top: 0;
  margin-bottom: 1rem;
  font-size: 1.2rem;
}

.category-list,
.recent-posts {
  list-style: none;
  padding: 0;
  margin: 0;
}

.category-list li,
.recent-posts li {
  padding: 0.5rem 0;
  border-bottom: 1px solid var(--border-color);
}

.category-list li:last-child,
.recent-posts li:last-child {
  border-bottom: none;
}

.tag-cloud .tag {
  margin: 0.25rem;
}

/* Pagination */
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 0.5rem;
  margin: 3rem 0;
}

.pagination-link {
  padding: 0.5rem 1rem;
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius);
  text-decoration: none;
  color: var(--text-color);
  transition: all 0.2s;
}

.pagination-link:hover,
.pagination-link.current {
  background: var(--primary-color);
  color: white;
  border-color: var(--primary-color);
}

/* Responsive design */
@media (max-width: 768px) {
  .menu-toggle {
    display: flex;
  }
  
  .main-navigation ul {
    display: none;
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    background: var(--bg-color);
    flex-direction: column;
    padding: 1rem;
    box-shadow: var(--shadow);
  }
  
  .main-navigation.active ul {
    display: flex;
  }
  
  .blog-page {
    grid-template-columns: 1fr;
  }
  
  .blog-sidebar {
    order: -1;
  }
  
  .blog-post .post-title {
    font-size: 2rem;
  }
  
  .author-info {
    flex-direction: column;
    align-items: flex-start;
    gap: 0.5rem;
  }
}

/* Dark mode support */
@media (prefers-color-scheme: dark) {
  :root {
    --text-color: #f9fafb;
    --text-light: #9ca3af;
    --bg-color: #111827;
    --bg-light: #1f2937;
    --border-color: #374151;
  }
}
```

## Step 8: Development and Testing

### Start the Development Server

```bash
# Start development with hot reload
bun run dev

# Your blog is now available at:
# http://localhost:3000
```

### Test Your Blog

1. **Homepage** - Should show recent posts
2. **Blog listing** - Should show all posts with pagination  
3. **Individual posts** - Should display with proper formatting
4. **Categories/Tags** - Should filter posts correctly
5. **RSS feed** - Should be available at `/rss.xml`
6. **Responsive design** - Test on mobile devices

### Add More Content

Create additional posts to see how the pagination and categorization work:

```bash
# Create more posts
touch src/posts/2024-02-01-css-grid-mastery.md
touch src/posts/2024-02-15-javascript-async-patterns.md
touch src/posts/2024-03-01-accessibility-matters.md
```

## Step 9: SEO and Performance Optimization

### Generate RSS Feed

The RSS feed is automatically generated based on your configuration. You can customize it by creating `src/rss.xml.template`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <title>{{ site.title }}</title>
    <description>{{ site.description }}</description>
    <link>{{ site.url }}</link>
    <atom:link href="{{ site.url }}/rss.xml" rel="self" type="application/rss+xml"/>
    <language>en-us</language>
    <lastBuildDate>{{ build.date | rssDate }}</lastBuildDate>
    
    {{ for post in posts | limit(20) }}
    <item>
      <title>{{ post.title }}</title>
      <description><![CDATA[{{ post.content }}]]></description>
      <link>{{ site.url }}{{ post.url }}</link>
      <guid>{{ site.url }}{{ post.url }}</guid>
      <pubDate>{{ post.date | rssDate }}</pubDate>
      {{ if post.category }}
      <category>{{ post.category }}</category>
      {{ endif }}
    </item>
    {{ endfor }}
  </channel>
</rss>
```

### Add Sitemap Generation

Update your `unify.config.js`:

```javascript
export default {
  // ... existing config
  
  sitemap: {
    generate: true,
    changefreq: {
      '/': 'daily',
      '/blog': 'daily',
      '/posts/*': 'weekly'
    },
    priority: {
      '/': 1.0,
      '/blog': 0.8,
      '/posts/*': 0.6
    }
  }
}
```

## Step 10: Build and Deploy

### Build for Production

```bash
# Create optimized production build
bun run build

# Test the production build locally
bun run preview
```

### Deploy to GitHub Pages

```bash
# Initialize git repository
git init
git add .

# Use Giv for professional commit messages
giv message
git commit -F .giv-message

# Create GitHub repository
gh repo create my-blog --public --push

# Set up GitHub Pages
gh api repos/:owner/my-blog/pages \
  --method POST \
  --field source.branch=main \
  --field source.path=/

# Your blog will be available at:
# https://yourusername.github.io/my-blog
```

### Alternative Deployment Options

**Netlify:**
1. Connect your GitHub repository
2. Set build command: `bun run build`
3. Set publish directory: `dist`

**Vercel:**
1. Import your repository
2. Framework preset: Other
3. Build command: `bun run build`
4. Output directory: `dist`

## Advanced Features

### Add Search Functionality

Create `src/search.html`:

```html
<!-- @include .components/layout.html -->

<div class="search-page">
    <header class="page-header">
        <h1>Search</h1>
        <div class="search-form">
            <input type="text" id="search-input" placeholder="Search posts...">
            <button onclick="performSearch()">Search</button>
        </div>
    </header>
    
    <div id="search-results">
        <!-- Results will be populated by JavaScript -->
    </div>
</div>

<script>
// Simple client-side search
const posts = {{ posts | json }};

function performSearch() {
    const query = document.getElementById('search-input').value.toLowerCase();
    const results = posts.filter(post => 
        post.title.toLowerCase().includes(query) ||
        post.content.toLowerCase().includes(query) ||
        post.tags.some(tag => tag.toLowerCase().includes(query))
    );
    
    displayResults(results);
}

function displayResults(results) {
    const container = document.getElementById('search-results');
    
    if (results.length === 0) {
        container.innerHTML = '<p>No posts found.</p>';
        return;
    }
    
    const html = results.map(post => `
        <article class="search-result">
            <h3><a href="${post.url}">${post.title}</a></h3>
            <p>${post.excerpt}</p>
            <div class="post-meta">
                <time>${new Date(post.date).toLocaleDateString()}</time>
                ${post.category ? `<span class="category">${post.category}</span>` : ''}
            </div>
        </article>
    `).join('');
    
    container.innerHTML = html;
}

// Enable search on Enter key
document.getElementById('search-input').addEventListener('keypress', function(e) {
    if (e.key === 'Enter') {
        performSearch();
    }
});
</script>

<!-- @endinclude -->
```

### Add Comments (Optional)

For comments, you can integrate with services like:

**Disqus:**
```html
<div id="disqus_thread"></div>
<script>
var disqus_config = function () {
    this.page.url = "{{ site.url }}{{ page.url }}";
    this.page.identifier = "{{ page.slug }}";
};

(function() {
    var d = document, s = d.createElement('script');
    s.src = 'https://yourblog.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
})();
</script>
```

**Giscus (GitHub Discussions):**
```html
<script src="https://giscus.app/client.js"
        data-repo="yourusername/your-repo"
        data-repo-id="your-repo-id"
        data-category="General"
        data-category-id="your-category-id"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="light"
        data-lang="en"
        crossorigin="anonymous"
        async>
</script>
```

## Maintenance and Updates

### Regular Tasks

1. **Update dependencies:**
   ```bash
   bun update
   ```

2. **Generate changelog with Giv:**
   ```bash
   giv changelog > CHANGELOG.md
   ```

3. **Check for broken links:**
   ```bash
   # Add to your build process
   npx broken-link-checker http://localhost:3000
   ```

4. **Monitor performance:**
   - Use Lighthouse for performance audits
   - Monitor Core Web Vitals
   - Check RSS feed validity

### Content Strategy

- **Consistency**: Aim for regular posting schedule
- **Categories**: Keep categories focused and meaningful
- **Tags**: Use tags strategically for discoverability  
- **SEO**: Write compelling meta descriptions
- **Images**: Optimize images for web (WebP format recommended)

## Conclusion

You now have a fully functional, fast, and SEO-optimized blog built with Unify! The blog includes:

✅ **Modern Design** - Responsive and accessible
✅ **Fast Performance** - Optimized static generation
✅ **SEO Optimized** - Meta tags, structured data, sitemap
✅ **Content Management** - Easy Markdown-based posting
✅ **Social Features** - Sharing buttons and RSS feed
✅ **Search & Navigation** - Categories, tags, and search
✅ **Developer Friendly** - Git workflow with AI assistance

### Next Steps

- [Customize the design](../unify/theming-guide.md) to match your brand
- [Add analytics](../integrations/analytics-setup.md) to track visitors
- [Set up a newsletter](../integrations/newsletter-setup.md) for subscribers
- [Optimize for speed](../guides/performance-optimization.md) even further
- [Add more interactive features](../unify/interactive-components.md)

**Happy blogging!** 🚀

---

*This tutorial is part of the [fwdslsh examples repository](https://github.com/fwdslsh/examples). For more tutorials and examples, explore the [complete documentation](../getting-started.md).*