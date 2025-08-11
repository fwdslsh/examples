# Optimizing Unify Sites for Search Engines

Master SEO optimization for Unify-powered websites. This comprehensive guide covers meta tags, structured data, sitemaps, performance optimization, and advanced SEO techniques for maximum search engine visibility.

## Overview

Unify provides built-in SEO optimization features:
- **Automatic sitemap generation** with configurable options
- **Meta tag management** through frontmatter and templates
- **Structured data support** for rich search results
- **Performance optimization** for Core Web Vitals
- **Social media integration** with Open Graph and Twitter Cards
- **Accessibility features** that improve SEO rankings

## Basic SEO Setup

### Meta Tags and HTML Structure

Configure proper meta tags in your layout templates:

**`src/.components/layouts/base.html`**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  
  <!-- Primary Meta Tags -->
  <title><slot name="title">Default Site Title</slot></title>
  <meta name="title" content="<slot name='meta-title'><slot name='title'>Default Site Title</slot></slot>">
  <meta name="description" content="<slot name='meta-description'>Default site description for SEO</slot>">
  <meta name="keywords" content="<slot name='meta-keywords'>default, keywords, seo</slot>">
  <meta name="author" content="<slot name='meta-author'>Site Author</slot>">
  <meta name="robots" content="<slot name='meta-robots'>index, follow</slot>">
  
  <!-- Canonical URL -->
  <link rel="canonical" href="<slot name='canonical-url'>https://yoursite.com</slot>">
  
  <!-- Open Graph / Facebook -->
  <meta property="og:type" content="<slot name='og-type'>website</slot>">
  <meta property="og:url" content="<slot name='og-url'>https://yoursite.com</slot>">
  <meta property="og:title" content="<slot name='og-title'><slot name='meta-title'><slot name='title'>Default Site Title</slot></slot></slot>">
  <meta property="og:description" content="<slot name='og-description'><slot name='meta-description'>Default site description</slot></slot>">
  <meta property="og:image" content="<slot name='og-image'>https://yoursite.com/og-image.jpg</slot>">
  <meta property="og:image:width" content="<slot name='og-image-width'>1200</slot>">
  <meta property="og:image:height" content="<slot name='og-image-height'>630</slot>">
  <meta property="og:site_name" content="<slot name='og-site-name'>Your Site Name</slot>">
  
  <!-- Twitter -->
  <meta property="twitter:card" content="<slot name='twitter-card'>summary_large_image</slot>">
  <meta property="twitter:url" content="<slot name='twitter-url'><slot name='og-url'>https://yoursite.com</slot></slot>">
  <meta property="twitter:title" content="<slot name='twitter-title'><slot name='og-title'><slot name='meta-title'><slot name='title'>Default Site Title</slot></slot></slot></slot>">
  <meta property="twitter:description" content="<slot name='twitter-description'><slot name='og-description'><slot name='meta-description'>Default description</slot></slot></slot>">
  <meta property="twitter:image" content="<slot name='twitter-image'><slot name='og-image'>https://yoursite.com/og-image.jpg</slot></slot>">
  <meta property="twitter:creator" content="<slot name='twitter-creator'>@yourtwitterhandle</slot>">
  
  <!-- Additional SEO Meta -->
  <slot name="additional-meta">
    <!-- Page-specific additional meta tags -->
  </slot>
  
  <!-- Structured Data -->
  <script type="application/ld+json">
    <slot name="structured-data">
    {
      "@context": "https://schema.org",
      "@type": "WebSite",
      "name": "<slot name='site-name'>Your Site Name</slot>",
      "url": "<slot name='site-url'>https://yoursite.com</slot>",
      "description": "<slot name='site-description'>Your site description</slot>"
    }
    </slot>
  </script>
  
  <!-- CSS -->
  <link rel="stylesheet" href="/styles.css">
  <slot name="styles">
    <!-- Additional styles -->
  </slot>
</head>
<body>
  <!-- Skip to main content for accessibility -->
  <a href="#main-content" class="skip-to-main">Skip to main content</a>
  
  <!-- Header -->
  <header>
    <slot name="header">
      <!-- Site header -->
    </slot>
  </header>
  
  <!-- Main content -->
  <main id="main-content">
    <slot name="main">
      <!-- Page content -->
    </slot>
  </main>
  
  <!-- Footer -->
  <footer>
    <slot name="footer">
      <!-- Site footer -->
    </slot>
  </footer>
  
  <!-- Scripts -->
  <slot name="scripts">
    <!-- Additional scripts -->
  </slot>
</body>
</html>
```

### Page-Level SEO Implementation

Use the SEO-optimized layout in your pages:

**`src/about.html`**:
```html
<div data-include=".components/layouts/base.html">
  <!-- SEO Meta Information -->
  <div slot="title">About Us - Innovative Solutions</div>
  <div slot="meta-description">Learn about our company's mission, values, and the innovative team behind our success. Discover our story and commitment to excellence.</div>
  <div slot="meta-keywords">about us, company, team, mission, values, innovation</div>
  <div slot="canonical-url">https://yoursite.com/about</div>
  
  <!-- Open Graph -->
  <div slot="og-type">website</div>
  <div slot="og-url">https://yoursite.com/about</div>
  <div slot="og-title">About Our Innovative Team</div>
  <div slot="og-description">Meet the passionate team driving innovation in web development tools.</div>
  <div slot="og-image">https://yoursite.com/images/about-team.jpg</div>
  
  <!-- Structured Data -->
  <div slot="structured-data">
  {
    "@context": "https://schema.org",
    "@type": "AboutPage",
    "name": "About Us",
    "description": "Learn about our company's mission and team",
    "url": "https://yoursite.com/about",
    "mainEntity": {
      "@type": "Organization",
      "name": "Your Company Name",
      "url": "https://yoursite.com",
      "logo": "https://yoursite.com/logo.png",
      "description": "We create innovative web development tools",
      "foundingDate": "2024",
      "founders": [
        {
          "@type": "Person",
          "name": "Founder Name",
          "jobTitle": "CEO"
        }
      ]
    }
  }
  </div>
  
  <!-- Page Header -->
  <div slot="header">
    <nav class="main-navigation">
      <!-- Navigation with proper linking structure -->
      <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/about" aria-current="page">About</a></li>
        <li><a href="/products">Products</a></li>
        <li><a href="/contact">Contact</a></li>
      </ul>
    </nav>
  </div>
  
  <!-- Main Content with SEO-friendly structure -->
  <div slot="main">
    <article>
      <header>
        <h1>About Our Company</h1>
        <p class="lead">We're passionate about creating tools that make web development faster and more enjoyable.</p>
      </header>
      
      <section>
        <h2>Our Mission</h2>
        <p>To revolutionize web development by providing tools that eliminate complexity while maintaining power and flexibility.</p>
      </section>
      
      <section>
        <h2>Our Team</h2>
        <div class="team-grid">
          <!-- Team member cards with proper markup -->
          <div class="team-member" itemscope itemtype="https://schema.org/Person">
            <img src="/team/john.jpg" alt="John Doe, CEO" itemprop="image">
            <h3 itemprop="name">John Doe</h3>
            <p itemprop="jobTitle">Chief Executive Officer</p>
            <p itemprop="description">Leading our vision for the future of web development.</p>
          </div>
        </div>
      </section>
    </article>
  </div>
  
  <!-- Footer -->
  <div slot="footer">
    <div class="footer-content">
      <div class="footer-links">
        <h3>Quick Links</h3>
        <ul>
          <li><a href="/privacy">Privacy Policy</a></li>
          <li><a href="/terms">Terms of Service</a></li>
          <li><a href="/sitemap.xml">Sitemap</a></li>
        </ul>
      </div>
    </div>
  </div>
</div>
```

## Advanced SEO Configuration

### Unify SEO Configuration

Configure SEO settings in your `unify.config.js`:

```javascript
export default {
  seo: {
    // Sitemap generation
    sitemap: {
      enabled: true,
      baseUrl: 'https://yoursite.com',
      changefreq: 'weekly',
      priority: 0.8,
      exclude: ['/admin/*', '/private/*'],
      include: [
        // Additional URLs not in file structure
        { url: '/api/docs', changefreq: 'monthly', priority: 0.6 }
      ]
    },
    
    // Robots.txt generation
    robots: {
      enabled: true,
      allow: ['*'],
      disallow: ['/admin/', '/private/'],
      sitemap: 'https://yoursite.com/sitemap.xml'
    },
    
    // Meta tag defaults
    defaults: {
      title: 'Your Site Name',
      description: 'Default meta description for your site',
      keywords: 'web development, tools, innovation',
      author: 'Your Name',
      ogImage: '/og-default.jpg',
      twitterCreator: '@yourhandle'
    },
    
    // Performance optimization
    performance: {
      minifyHtml: true,
      compressImages: true,
      generateWebp: true,
      inlineSmallCss: true,
      preloadFonts: true
    }
  },
  
  build: {
    // SEO-friendly URLs
    prettyUrls: true,
    baseUrl: process.env.BASE_URL || 'https://yoursite.com',
    
    // Generate social media images
    generateOgImages: {
      enabled: true,
      template: 'templates/og-image.html',
      width: 1200,
      height: 630
    }
  }
}
```

### Dynamic SEO with Frontmatter

Use frontmatter in Markdown files for SEO metadata:

**`src/blog/my-post.md`**:
```markdown
---
title: "Complete Guide to Web Performance Optimization"
description: "Learn advanced techniques for optimizing web performance, improving Core Web Vitals, and delivering faster user experiences."
keywords: "web performance, optimization, core web vitals, speed"
author: "Jane Smith"
date: "2024-01-15"
category: "Performance"
tags: ["performance", "optimization", "web vitals"]
featured: true
canonical: "https://yoursite.com/blog/web-performance-guide"

# SEO specific
meta:
  robots: "index, follow"
  ogType: "article"
  ogImage: "/blog/images/web-performance-og.jpg"
  twitterCard: "summary_large_image"

# Article specific structured data
article:
  author: "Jane Smith"
  publishedTime: "2024-01-15T10:00:00Z"
  modifiedTime: "2024-01-16T14:30:00Z"
  section: "Web Development"
  tags: ["performance", "optimization"]
---

# Complete Guide to Web Performance Optimization

Web performance is crucial for user experience and SEO rankings. In this comprehensive guide, we'll explore...

## Table of Contents

1. [Core Web Vitals](#core-web-vitals)
2. [Image Optimization](#image-optimization)
3. [JavaScript Performance](#javascript-performance)
4. [CSS Optimization](#css-optimization)

## Core Web Vitals

The Core Web Vitals are a set of specific factors that Google considers important...
```

### Blog Post Template with SEO

Create an SEO-optimized blog post template:

**`src/.components/layouts/blog-post.html`**:
```html
<div data-include="layouts/base.html">
  <!-- Dynamic SEO from frontmatter -->
  <div slot="title">{{title}} | Your Blog</div>
  <div slot="meta-description">{{description}}</div>
  <div slot="meta-keywords">{{keywords}}</div>
  <div slot="meta-author">{{author}}</div>
  <div slot="canonical-url">{{canonical}}</div>
  
  <!-- Open Graph for articles -->
  <div slot="og-type">article</div>
  <div slot="og-title">{{title}}</div>
  <div slot="og-description">{{description}}</div>
  <div slot="og-image">{{meta.ogImage}}</div>
  <div slot="og-url">{{canonical}}</div>
  
  <!-- Article-specific meta -->
  <div slot="additional-meta">
    <meta property="article:author" content="{{author}}">
    <meta property="article:published_time" content="{{article.publishedTime}}">
    <meta property="article:modified_time" content="{{article.modifiedTime}}">
    <meta property="article:section" content="{{article.section}}">
    {{#each article.tags}}
    <meta property="article:tag" content="{{this}}">
    {{/each}}
  </div>
  
  <!-- Article Structured Data -->
  <div slot="structured-data">
  {
    "@context": "https://schema.org",
    "@type": "BlogPosting",
    "headline": "{{title}}",
    "description": "{{description}}",
    "author": {
      "@type": "Person",
      "name": "{{author}}"
    },
    "datePublished": "{{article.publishedTime}}",
    "dateModified": "{{article.modifiedTime}}",
    "url": "{{canonical}}",
    "image": "{{meta.ogImage}}",
    "publisher": {
      "@type": "Organization",
      "name": "Your Blog Name",
      "logo": {
        "@type": "ImageObject",
        "url": "https://yoursite.com/logo.png"
      }
    },
    "mainEntityOfPage": {
      "@type": "WebPage",
      "@id": "{{canonical}}"
    },
    "keywords": "{{keywords}}"
  }
  </div>
  
  <!-- Breadcrumbs -->
  <div slot="header">
    <nav aria-label="Breadcrumb">
      <ol class="breadcrumb" itemscope itemtype="https://schema.org/BreadcrumbList">
        <li itemprop="itemListElement" itemscope itemtype="https://schema.org/ListItem">
          <a itemprop="item" href="/"><span itemprop="name">Home</span></a>
          <meta itemprop="position" content="1">
        </li>
        <li itemprop="itemListElement" itemscope itemtype="https://schema.org/ListItem">
          <a itemprop="item" href="/blog"><span itemprop="name">Blog</span></a>
          <meta itemprop="position" content="2">
        </li>
        <li itemprop="itemListElement" itemscope itemtype="https://schema.org/ListItem">
          <span itemprop="name">{{title}}</span>
          <meta itemprop="position" content="3">
        </li>
      </ol>
    </nav>
  </div>
  
  <!-- Article Content -->
  <div slot="main">
    <article>
      <header class="article-header">
        <h1>{{title}}</h1>
        <div class="article-meta">
          <time datetime="{{article.publishedTime}}">{{date}}</time>
          <span class="author">By {{author}}</span>
          <span class="category">{{category}}</span>
        </div>
        {{#if meta.ogImage}}
        <img src="{{meta.ogImage}}" alt="{{title}}" class="featured-image">
        {{/if}}
      </header>
      
      <div class="article-content">
        <!-- Article content goes here -->
        <slot name="content">
          <!-- Markdown content will be inserted here -->
        </slot>
      </div>
      
      <footer class="article-footer">
        <div class="article-tags">
          {{#each tags}}
          <a href="/blog/tag/{{this}}" class="tag">{{this}}</a>
          {{/each}}
        </div>
        
        <div class="share-buttons">
          <a href="https://twitter.com/intent/tweet?url={{canonical}}&text={{title}}" 
             target="_blank" rel="noopener">Share on Twitter</a>
          <a href="https://www.facebook.com/sharer/sharer.php?u={{canonical}}" 
             target="_blank" rel="noopener">Share on Facebook</a>
          <a href="https://www.linkedin.com/sharing/share-offsite/?url={{canonical}}" 
             target="_blank" rel="noopener">Share on LinkedIn</a>
        </div>
      </footer>
    </article>
    
    <!-- Related Articles -->
    <section class="related-articles">
      <h2>Related Articles</h2>
      <!-- Related article logic here -->
    </section>
  </div>
</div>
```

## Performance Optimization for SEO

### Core Web Vitals Optimization

Optimize for Google's Core Web Vitals:

**1. Largest Contentful Paint (LCP)**:
```javascript
// In your main JavaScript
// Preload important images
const criticalImages = document.querySelectorAll('img[data-critical]');
criticalImages.forEach(img => {
  const link = document.createElement('link');
  link.rel = 'preload';
  link.as = 'image';
  link.href = img.src;
  document.head.appendChild(link);
});

// Optimize image loading
function optimizeImages() {
  const images = document.querySelectorAll('img[data-optimize]');
  
  const imageObserver = new IntersectionObserver((entries, observer) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const img = entry.target;
        
        // Use WebP if supported
        if (supportsWebP()) {
          img.src = img.dataset.webp || img.src;
        }
        
        // Add loading complete class
        img.onload = () => img.classList.add('loaded');
        
        observer.unobserve(img);
      }
    });
  });
  
  images.forEach(img => imageObserver.observe(img));
}

function supportsWebP() {
  const canvas = document.createElement('canvas');
  return canvas.toDataURL('image/webp').indexOf('data:image/webp') === 0;
}
```

**2. First Input Delay (FID)**:
```javascript
// Optimize JavaScript execution
function optimizeInteractivity() {
  // Use requestIdleCallback for non-critical tasks
  if ('requestIdleCallback' in window) {
    requestIdleCallback(() => {
      // Non-critical initialization
      initializeAnalytics();
      initializeNonEssentialFeatures();
    });
  } else {
    // Fallback for browsers without requestIdleCallback
    setTimeout(() => {
      initializeAnalytics();
      initializeNonEssentialFeatures();
    }, 100);
  }
  
  // Break up long tasks
  function processLargeDataSet(data) {
    const chunkSize = 100;
    let index = 0;
    
    function processChunk() {
      const chunk = data.slice(index, index + chunkSize);
      
      // Process chunk
      chunk.forEach(item => processItem(item));
      
      index += chunkSize;
      
      if (index < data.length) {
        // Use scheduler if available
        if ('scheduler' in window && 'postTask' in scheduler) {
          scheduler.postTask(processChunk, { priority: 'background' });
        } else {
          setTimeout(processChunk, 0);
        }
      }
    }
    
    processChunk();
  }
}
```

**3. Cumulative Layout Shift (CLS)**:
```css
/* Prevent layout shifts */

/* Reserve space for images */
img {
  width: 100%;
  height: auto;
  aspect-ratio: attr(width) / attr(height);
}

/* Use placeholder dimensions */
.image-placeholder {
  width: 100%;
  aspect-ratio: 16/9;
  background: #f0f0f0;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Font loading optimization */
@font-face {
  font-family: 'CustomFont';
  src: url('/fonts/custom-font.woff2') format('woff2');
  font-display: swap; /* Prevent invisible text */
}

/* Reserve space for dynamic content */
.dynamic-content {
  min-height: 200px; /* Prevent layout shift */
}

/* Smooth transitions for content changes */
.content-container {
  transition: height 0.3s ease;
}
```

### Advanced Performance Techniques

**Resource Hints and Preloading**:
```html
<!-- In your head section -->
<div slot="styles">
  <!-- DNS prefetch for external resources -->
  <link rel="dns-prefetch" href="//fonts.googleapis.com">
  <link rel="dns-prefetch" href="//cdn.example.com">
  
  <!-- Preconnect to critical third-party origins -->
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  
  <!-- Preload critical resources -->
  <link rel="preload" href="/fonts/primary.woff2" as="font" type="font/woff2" crossorigin>
  <link rel="preload" href="/critical.css" as="style">
  <link rel="preload" href="/hero-image.webp" as="image">
  
  <!-- Prefetch likely next pages -->
  <link rel="prefetch" href="/about">
  <link rel="prefetch" href="/contact">
  
  <!-- Module preload for JavaScript -->
  <link rel="modulepreload" href="/js/main.js">
</div>
```

## Technical SEO Implementation

### Schema.org Structured Data

Implement comprehensive structured data:

**Product Schema**:
```html
<div slot="structured-data">
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "{{product.name}}",
  "description": "{{product.description}}",
  "image": "{{product.image}}",
  "brand": {
    "@type": "Brand",
    "name": "{{product.brand}}"
  },
  "offers": {
    "@type": "Offer",
    "price": "{{product.price}}",
    "priceCurrency": "{{product.currency}}",
    "availability": "https://schema.org/InStock",
    "seller": {
      "@type": "Organization",
      "name": "Your Company"
    }
  },
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "{{product.rating}}",
    "reviewCount": "{{product.reviewCount}}"
  }
}
</div>
```

**FAQ Schema**:
```html
<div slot="structured-data">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {{#each faqs}}
    {
      "@type": "Question",
      "name": "{{question}}",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "{{answer}}"
      }
    }{{#unless @last}},{{/unless}}
    {{/each}}
  ]
}
</div>
```

**Organization Schema**:
```html
<div slot="structured-data">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Your Company Name",
  "url": "https://yoursite.com",
  "logo": "https://yoursite.com/logo.png",
  "description": "Company description",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "123 Main St",
    "addressLocality": "City",
    "addressRegion": "State",
    "postalCode": "12345",
    "addressCountry": "US"
  },
  "contactPoint": {
    "@type": "ContactPoint",
    "telephone": "+1-555-123-4567",
    "contactType": "customer service",
    "availableLanguage": "English"
  },
  "sameAs": [
    "https://twitter.com/yourcompany",
    "https://linkedin.com/company/yourcompany",
    "https://facebook.com/yourcompany"
  ]
}
</div>
```

### XML Sitemap Generation

Customize sitemap generation:

```javascript
// unify.config.js
export default {
  seo: {
    sitemap: {
      enabled: true,
      baseUrl: 'https://yoursite.com',
      
      // Custom URL processing
      urlProcessor: (url, page) => {
        // Add lastmod from file stats or frontmatter
        const lastmod = page.frontmatter?.updated || page.stats?.mtime;
        
        // Set priority based on page type
        let priority = 0.5;
        if (url === '/') priority = 1.0;
        else if (url.startsWith('/blog/')) priority = 0.8;
        else if (url.startsWith('/products/')) priority = 0.9;
        
        // Set changefreq based on content type
        let changefreq = 'monthly';
        if (url.startsWith('/blog/')) changefreq = 'weekly';
        else if (url.startsWith('/news/')) changefreq = 'daily';
        
        return {
          url,
          lastmod: lastmod?.toISOString(),
          changefreq,
          priority
        };
      },
      
      // Include dynamic URLs
      additionalUrls: async () => {
        // Fetch from API or database
        const dynamicPages = await fetchDynamicPages();
        return dynamicPages.map(page => ({
          url: `/products/${page.slug}`,
          lastmod: page.updatedAt,
          changefreq: 'weekly',
          priority: 0.8
        }));
      }
    }
  }
}
```

### Robots.txt Optimization

Generate comprehensive robots.txt:

```javascript
// unify.config.js
export default {
  seo: {
    robots: {
      enabled: true,
      
      // Custom robots.txt generation
      generate: (config) => {
        const baseUrl = config.baseUrl;
        
        return `
User-agent: *
Allow: /

# Block admin and private areas
Disallow: /admin/
Disallow: /private/
Disallow: /api/
Disallow: /*?*utm_source=
Disallow: /*?*utm_medium=
Disallow: /*?*utm_campaign=

# Allow specific bots to index certain areas
User-agent: Googlebot
Allow: /api/public/

# Crawl delay for specific bots
User-agent: Bingbot
Crawl-delay: 1

# Sitemap location
Sitemap: ${baseUrl}/sitemap.xml

# Additional sitemaps
Sitemap: ${baseUrl}/sitemap-images.xml
Sitemap: ${baseUrl}/sitemap-news.xml
        `.trim();
      }
    }
  }
}
```

## Local SEO Optimization

### Local Business Schema

For local businesses, implement local SEO:

```html
<div slot="structured-data">
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "Your Business Name",
  "description": "Business description",
  "url": "https://yoursite.com",
  "telephone": "+1-555-123-4567",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "123 Business St",
    "addressLocality": "Your City",
    "addressRegion": "State",
    "postalCode": "12345",
    "addressCountry": "US"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": "40.7128",
    "longitude": "-74.0060"
  },
  "openingHours": [
    "Mo-Fr 09:00-17:00",
    "Sa 10:00-16:00"
  ],
  "priceRange": "$$",
  "servedAreas": [
    {
      "@type": "City",
      "name": "Your City"
    }
  ]
}
</div>
```

## SEO Monitoring and Testing

### SEO Testing Component

Create a component for SEO testing:

**`src/.components/seo/seo-validator.html`**:
```html
<!-- SEO Validation Component (Development Only) -->
<div class="seo-validator" data-env="development" style="display: none;">
  <div class="seo-checklist">
    <h3>SEO Checklist</h3>
    <ul id="seo-checks">
      <!-- Populated by JavaScript -->
    </ul>
  </div>
  
  <div class="seo-meta-preview">
    <h3>Search Result Preview</h3>
    <div class="search-preview">
      <div class="search-title" id="preview-title"></div>
      <div class="search-url" id="preview-url"></div>
      <div class="search-description" id="preview-description"></div>
    </div>
  </div>
</div>

<script>
class SEOValidator {
  constructor() {
    if (window.location.hostname !== 'localhost') return;
    
    this.checks = [
      { name: 'Title tag exists', test: () => document.title.length > 0 },
      { name: 'Title length (30-60 chars)', test: () => document.title.length >= 30 && document.title.length <= 60 },
      { name: 'Meta description exists', test: () => this.getMetaContent('description')?.length > 0 },
      { name: 'Meta description length (120-160 chars)', test: () => {
        const desc = this.getMetaContent('description');
        return desc && desc.length >= 120 && desc.length <= 160;
      }},
      { name: 'H1 tag exists', test: () => document.querySelector('h1') !== null },
      { name: 'Single H1 tag', test: () => document.querySelectorAll('h1').length === 1 },
      { name: 'Canonical URL exists', test: () => document.querySelector('link[rel="canonical"]') !== null },
      { name: 'Open Graph tags', test: () => document.querySelector('meta[property^="og:"]') !== null },
      { name: 'Structured data exists', test: () => document.querySelector('script[type="application/ld+json"]') !== null },
      { name: 'Images have alt text', test: () => {
        const images = document.querySelectorAll('img');
        return Array.from(images).every(img => img.alt && img.alt.trim().length > 0);
      }},
      { name: 'Links have descriptive text', test: () => {
        const links = document.querySelectorAll('a[href]');
        return Array.from(links).every(link => {
          const text = link.textContent.trim();
          return text.length > 0 && !['click here', 'read more', 'learn more'].includes(text.toLowerCase());
        });
      }}
    ];
    
    this.init();
  }
  
  init() {
    if (this.shouldShow()) {
      this.showValidator();
      this.runChecks();
      this.updatePreview();
    }
  }
  
  shouldShow() {
    return window.location.search.includes('seo=1') || 
           localStorage.getItem('seo-validator') === 'enabled';
  }
  
  showValidator() {
    const validator = document.querySelector('.seo-validator');
    if (validator) {
      validator.style.display = 'block';
      validator.style.position = 'fixed';
      validator.style.bottom = '20px';
      validator.style.right = '20px';
      validator.style.width = '300px';
      validator.style.background = 'white';
      validator.style.border = '1px solid #ccc';
      validator.style.borderRadius = '8px';
      validator.style.padding = '16px';
      validator.style.fontSize = '14px';
      validator.style.zIndex = '10000';
      validator.style.boxShadow = '0 4px 12px rgba(0,0,0,0.1)';
    }
  }
  
  runChecks() {
    const checksList = document.getElementById('seo-checks');
    if (!checksList) return;
    
    checksList.innerHTML = '';
    
    this.checks.forEach(check => {
      const li = document.createElement('li');
      const passed = check.test();
      li.innerHTML = `
        <span class="check-status">${passed ? '✅' : '❌'}</span>
        <span class="check-name">${check.name}</span>
      `;
      li.style.marginBottom = '4px';
      checksList.appendChild(li);
    });
  }
  
  updatePreview() {
    const titleEl = document.getElementById('preview-title');
    const urlEl = document.getElementById('preview-url');
    const descEl = document.getElementById('preview-description');
    
    if (titleEl) {
      titleEl.textContent = document.title;
      titleEl.style.color = '#1a0dab';
      titleEl.style.fontSize = '18px';
      titleEl.style.fontWeight = 'normal';
      titleEl.style.textDecoration = 'underline';
      titleEl.style.cursor = 'pointer';
    }
    
    if (urlEl) {
      urlEl.textContent = window.location.href;
      urlEl.style.color = '#006621';
      urlEl.style.fontSize = '14px';
    }
    
    if (descEl) {
      const description = this.getMetaContent('description') || 'No meta description found';
      descEl.textContent = description;
      descEl.style.color = '#545454';
      descEl.style.fontSize = '13px';
      descEl.style.lineHeight = '1.4';
    }
  }
  
  getMetaContent(name) {
    const meta = document.querySelector(`meta[name="${name}"]`);
    return meta ? meta.getAttribute('content') : null;
  }
}

// Initialize SEO validator in development
document.addEventListener('DOMContentLoaded', () => {
  new SEOValidator();
});
</script>
</div>
```

## SEO Build Process

### Build-Time SEO Optimization

Integrate SEO checks into your build process:

```javascript
// scripts/seo-check.js
import { promises as fs } from 'fs';
import path from 'path';
import { JSDOM } from 'jsdom';

class SEOBuildChecker {
  constructor(distPath) {
    this.distPath = distPath;
    this.issues = [];
  }
  
  async checkAllPages() {
    const htmlFiles = await this.findHtmlFiles(this.distPath);
    
    for (const file of htmlFiles) {
      await this.checkPage(file);
    }
    
    this.generateReport();
  }
  
  async findHtmlFiles(dir) {
    const files = [];
    const entries = await fs.readdir(dir, { withFileTypes: true });
    
    for (const entry of entries) {
      const fullPath = path.join(dir, entry.name);
      
      if (entry.isDirectory()) {
        files.push(...await this.findHtmlFiles(fullPath));
      } else if (entry.name.endsWith('.html')) {
        files.push(fullPath);
      }
    }
    
    return files;
  }
  
  async checkPage(filePath) {
    const content = await fs.readFile(filePath, 'utf-8');
    const dom = new JSDOM(content);
    const document = dom.window.document;
    
    const pagePath = path.relative(this.distPath, filePath);
    const pageIssues = [];
    
    // Check title
    const title = document.title;
    if (!title) {
      pageIssues.push('Missing title tag');
    } else if (title.length < 30 || title.length > 60) {
      pageIssues.push(`Title length (${title.length}) should be 30-60 characters`);
    }
    
    // Check meta description
    const metaDesc = document.querySelector('meta[name="description"]');
    if (!metaDesc) {
      pageIssues.push('Missing meta description');
    } else {
      const descLength = metaDesc.getAttribute('content')?.length || 0;
      if (descLength < 120 || descLength > 160) {
        pageIssues.push(`Meta description length (${descLength}) should be 120-160 characters`);
      }
    }
    
    // Check H1
    const h1Tags = document.querySelectorAll('h1');
    if (h1Tags.length === 0) {
      pageIssues.push('Missing H1 tag');
    } else if (h1Tags.length > 1) {
      pageIssues.push(`Multiple H1 tags found (${h1Tags.length})`);
    }
    
    // Check images
    const images = document.querySelectorAll('img');
    images.forEach((img, index) => {
      if (!img.getAttribute('alt')) {
        pageIssues.push(`Image ${index + 1} missing alt text`);
      }
    });
    
    // Check internal links
    const links = document.querySelectorAll('a[href]');
    links.forEach((link, index) => {
      const href = link.getAttribute('href');
      if (href.startsWith('/') || href.startsWith('./')) {
        // Check if internal link is valid
        const targetPath = this.resolveInternalLink(href, filePath);
        if (!this.fileExists(targetPath)) {
          pageIssues.push(`Broken internal link: ${href}`);
        }
      }
    });
    
    if (pageIssues.length > 0) {
      this.issues.push({
        page: pagePath,
        issues: pageIssues
      });
    }
  }
  
  generateReport() {
    if (this.issues.length === 0) {
      console.log('✅ All SEO checks passed!');
      return;
    }
    
    console.log(`❌ Found SEO issues on ${this.issues.length} pages:\n`);
    
    this.issues.forEach(({ page, issues }) => {
      console.log(`📄 ${page}:`);
      issues.forEach(issue => {
        console.log(`  • ${issue}`);
      });
      console.log('');
    });
    
    // Optionally fail build
    if (process.env.FAIL_ON_SEO_ISSUES === 'true') {
      process.exit(1);
    }
  }
  
  resolveInternalLink(href, currentFile) {
    // Implement link resolution logic
    // This is a simplified version
    return path.resolve(path.dirname(currentFile), href);
  }
  
  fileExists(filePath) {
    try {
      return fs.access(filePath).then(() => true).catch(() => false);
    } catch {
      return false;
    }
  }
}

// Usage
const checker = new SEOBuildChecker('./dist');
checker.checkAllPages();
```

## Next Steps

### Advanced SEO Features
- [International SEO with hreflang](international-seo.md)
- [E-commerce SEO with Product Schema](ecommerce-seo.md)
- [Technical SEO Auditing](technical-seo-audit.md)

### Performance Optimization  
- [Core Web Vitals Optimization](../guides/core-web-vitals.md)
- [Image Optimization Strategies](../guides/image-optimization.md)
- [JavaScript Performance](../guides/javascript-performance.md)

### Monitoring and Analytics
- [SEO Performance Tracking](../integrations/seo-analytics.md)
- [Search Console Integration](../integrations/search-console.md)
- [Automated SEO Monitoring](../integrations/seo-monitoring.md)

**Your Unify site is now optimized for maximum search engine visibility!** 🚀

---

*This guide references the [Unify performance requirements](https://github.com/fwdslsh/unify) and follows [Google's SEO guidelines](https://developers.google.com/search/docs). For the latest SEO best practices, visit [fwdslsh.dev](https://fwdslsh.dev).*